---
layout: post
category: Android
title: Android中关于图片压缩、截图、旋转的一些方法
tagline: by hombo
tags: [Java]
---

最近做项目使用到了图片的各种处理，遇到了各种问题，经过研究和调试，在此总结一些关于图片处理的方法，以便今后使用。这些方法包括图片压缩、bitmap缩略图、图片旋转和截图。

<!--more-->

###1. 图片压缩

		// /图片压缩
		public static Bitmap compressImage(Bitmap image) {
	
			ByteArrayOutputStream baos = new ByteArrayOutputStream();
			image.compress(Bitmap.CompressFormat.JPEG, 90, baos);
			int options = 90;
			while (baos.toByteArray().length / 1024 > 100) { // 循环判断如果压缩后图片是否大于100kb,大于继续压缩
				baos.reset();// 重置baos即清空baos
				image.compress(Bitmap.CompressFormat.JPEG, options, baos);// 这里压缩options%，把压缩后的数据存放到baos中
				options -= 10;// 每次都减少10
			}
			ByteArrayInputStream isBm = new ByteArrayInputStream(baos.toByteArray());// 把压缩后的数据baos存放到ByteArrayInputStream中
			Bitmap bitmap = BitmapFactory.decodeStream(isBm, null, null);// 把ByteArrayInputStream数据生成图片
			return bitmap;
		}


###2. 从图片文件产生Bitmap缩略图

有时候图片文件很大，加载到Bitmap，然后放置到控件上会发生OOM（内存溢出）错误，下面的方法通过先压缩的方式，避免了这个问题：

	public static Bitmap compressBitmap(String filepath, Context context) {
		BitmapFactory.Options opt = new BitmapFactory.Options();
		opt.inJustDecodeBounds = true;
		Bitmap bm = BitmapFactory.decodeFile(filepath, opt);

		// 获取到这个图片的原始宽度和高度
		int picHeight = opt.outHeight;

		// 获取屏的宽度和高度
		WindowManager windowManager = ((Activity) context).getWindowManager();
		Display display = windowManager.getDefaultDisplay();
		int screenHeight = display.getHeight();

		int scale = picHeight/80;
		
		if (scale <= 0) {
			scale = 1;
		}
		opt.inSampleSize = scale;
		// 这次再真正地生成一个有像素的，经过缩放了的bitmap

		opt.inJustDecodeBounds = false;
		bm = BitmapFactory.decodeFile(filepath, opt);
		return bm;
	}

###3. 图片旋转调整

有时候拍照后图片存储的方向不对，下面的两个方法可以对图片进行调整归正：

	/**
	 * 通过ExifInterface类读取图片文件的被旋转角度
	 * 
	 * @param path
	 *            ： 图片文件的路径
	 * @return 图片文件的被旋转角度
	 */
	public static int readPicDegree(String path) {
		int degree = 0;

		// 读取图片文件信息的类ExifInterface
		ExifInterface exif = null;
		try {
			exif = new ExifInterface(path);
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		if (exif != null) {
			int orientation = exif.getAttributeInt(
					ExifInterface.TAG_ORIENTATION,
					ExifInterface.ORIENTATION_NORMAL);
			switch (orientation) {
			case ExifInterface.ORIENTATION_ROTATE_90:
				degree = 90;
				break;

			case ExifInterface.ORIENTATION_ROTATE_180:
				degree = 180;
				break;

			case ExifInterface.ORIENTATION_ROTATE_270:
				degree = 270;
				break;
			}
		}

		return degree;
	}

	/**
	 * 将图片纠正到正确方向
	 * 
	 * @param degree
	 *            ： 图片被系统旋转的角度
	 * @param bitmap
	 *            ： 需纠正方向的图片
	 * @return 纠向后的图片
	 */
	public static Bitmap rotateBitmap(int degree, Bitmap bitmap) {
		Matrix matrix = new Matrix();
		matrix.postRotate(degree);

		Bitmap bm = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(),
				bitmap.getHeight(), matrix, true);
		return bm;
	}
	
###4. 图片剪裁

下面这个方法会遍历系统的剪裁工具，然后实现图片的剪裁：

	private void doCrop(Uri uri) {
		final ArrayList<CropOption> cropOptions = new ArrayList<CropOption>();

		Intent intent = new Intent("com.android.camera.action.CROP");
		intent.setType("image/*");

		List<ResolveInfo> list = getPackageManager().queryIntentActivities(
				intent, 0);

		int size = list.size();

		if (size == 0) {
			Toast.makeText(this, "Can not find image crop app",
					Toast.LENGTH_SHORT).show();
			return;
		} else {
			intent.setData(uri);

            //剪裁的分辨率和长宽比
			intent.putExtra("outputX", 300);
			intent.putExtra("outputY", 300);
			intent.putExtra("aspectX", 1);
			intent.putExtra("aspectY", 1);
			
			intent.putExtra("scale", true);
			intent.putExtra("noFaceDetection", true); // no face detection
			intent.putExtra("return-data", false);
			intent.putExtra(MediaStore.EXTRA_OUTPUT,
					Uri.fromFile(new File(Environment
							.getExternalStorageDirectory(), filename[index])));
			intent.putExtra("outputFormat",
					Bitmap.CompressFormat.JPEG.toString());

			if (size == 1) {
				Intent i = new Intent(intent);
				ResolveInfo res = list.get(0);

				i.setComponent(new ComponentName(res.activityInfo.packageName,
						res.activityInfo.name));

				startActivityForResult(i, CROP_FROM_CAMERA);
			} else {
				for (ResolveInfo res : list) {
					final CropOption co = new CropOption();

					co.title = getPackageManager().getApplicationLabel(
							res.activityInfo.applicationInfo);
					co.icon = getPackageManager().getApplicationIcon(
							res.activityInfo.applicationInfo);
					co.appIntent = new Intent(intent);

					co.appIntent
							.setComponent(new ComponentName(
									res.activityInfo.packageName,
									res.activityInfo.name));

					cropOptions.add(co);
				}

				CropOptionAdapter adapter = new CropOptionAdapter(
						getApplicationContext(), cropOptions);

				AlertDialog.Builder builder = new AlertDialog.Builder(this);
				builder.setTitle("Choose Crop App");
				builder.setAdapter(adapter,
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog, int item) {
								startActivityForResult(
										cropOptions.get(item).appIntent,
										CROP_FROM_CAMERA);
							}
						});

				builder.setOnCancelListener(new DialogInterface.OnCancelListener() {
					@Override
					public void onCancel(DialogInterface dialog) {

						if (mImageCaptureUri[index] != null) {
							getContentResolver().delete(
									mImageCaptureUri[index], null, null);
							mImageCaptureUri[index] = null;
						}
					}
				});

				AlertDialog alert = builder.create();

				alert.show();
			}
		}
	}













