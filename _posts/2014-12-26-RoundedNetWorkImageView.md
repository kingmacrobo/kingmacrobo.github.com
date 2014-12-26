---
layout: post
category: Android
title: RoundedNetworkImageView
tagline: by hombo
tags: [android]
---

将Volley的NetworkImageView的方形显示图片改为圆形显示，方法非常好，在此记录一下代码，方便自己和其他人今后使用！

<!--more-->

####将方形改成圆形的核心思想是重写ImageView的onDraw和getCroppedBitmap两个方法。

代码如下：

	//  RoundedNetworkImageView
	 
	import android.content.Context;
	import android.graphics.Bitmap;
	import android.graphics.BitmapShader;
	import android.graphics.Canvas;
	import android.graphics.Paint;
	import android.graphics.Shader;
	import android.graphics.drawable.BitmapDrawable;
	import android.graphics.drawable.Drawable;
	import android.util.AttributeSet;
	 
	import com.android.volley.toolbox.NetworkImageView;
	 
	 
	public class RoundedNetworkImageView extends NetworkImageView {
	    private int borderColor = 0xffffffff;
	    private int borderWidth = 10;
	 
	    public RoundedNetworkImageView(Context context) {
	        super(context);
	    }
	 
	    public RoundedNetworkImageView(Context context, AttributeSet attrs) {
	        super(context, attrs);
	    }
	 
	    public RoundedNetworkImageView(Context context, AttributeSet attrs, int defStyle) {
	        super(context, attrs, defStyle);
	    }
	 
	    public void setBorderColor(int resourceID) {
	        borderColor = resourceID;
	    }
	 
	    public void setBorderWidth(int border) {
	        borderWidth = border;
	    }
	 
	    public int getBorderWidth() {
	        return borderWidth;
	    }
	 
	    @Override
	    protected void onDraw(Canvas canvas) {
	        Drawable drawable = getDrawable();
	 
	        if (drawable == null || getWidth() == 0 || getHeight() == 0)
	            return;
	 
	        Bitmap b = ((BitmapDrawable) drawable).getBitmap();
	        if (b != null) {
	            Bitmap bitmap = b.copy(Bitmap.Config.ARGB_8888, true);
	 
	            int radius = (getWidth() < getHeight()) ? getWidth() / 2 : getHeight() / 2;
	 
	            Bitmap roundBitmap = getCroppedBitmap(bitmap, radius);
	            canvas.drawBitmap(roundBitmap, 0, 0, null);
	        }
	    }
	 
	    public Bitmap getCroppedBitmap(Bitmap bmp, int radius) {
	        Bitmap scaledBmp = Bitmap.createScaledBitmap(bmp, radius * 2, radius * 2, false);
	        Bitmap output = Bitmap.createBitmap(getWidth(), getHeight(), Bitmap.Config.ARGB_8888);
	        Canvas canvas = new Canvas(output);
	 
	        final Paint paint = new Paint();
	 
	        paint.setAntiAlias(true);
	        paint.setFilterBitmap(true);
	        paint.setDither(true);
	        canvas.drawARGB(0, 0, 0, 0);
	 
	        // Draws a circle to create the border
	        paint.setColor(borderColor);
	        canvas.drawCircle(getWidth() / 2, getHeight() / 2, radius, paint);
	 
	        // Draws the image subtracting the border width
	        BitmapShader s = new BitmapShader(scaledBmp, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);
	        paint.setShader(s);
	        canvas.drawCircle(getWidth() / 2, getHeight() / 2, radius - borderWidth - 0.5f, paint);
	 
	        return output;
	    }
	}






