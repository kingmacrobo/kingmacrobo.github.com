---
layout: post
category: Android
title: 在onCreate中获取控件尺寸的方法
tagline: by hombo
tags: [android]
---

我们知道在activity绘制完成之后，获取控件尺寸的方式就是调用getWitdh和getHeight，但是在onCreate中调用这两个方法，获取到的宽高不正确，为0，这是因为，在调用oncreate()方法时，界面处于不可见状态，内存加载组件还没有绘制出来，你是无法获取它的尺寸。那怎么在onCreate中获取控件的大小呢？有下面三个方法：

<!--more-->

####方法一

在onCreate中加入对控件的测量代码，代码如下：

	int w = View.MeasureSpec.makeMeasureSpec(0,View.MeasureSpec.UNSPECIFIED); 
	int h = View.MeasureSpec.makeMeasureSpec(0,View.MeasureSpec.UNSPECIFIED); 
	imageView.measure(w, h); 
	int height =imageView.getMeasuredHeight(); 
	int width =imageView.getMeasuredWidth(); 
	textView.append("\n"+height+","+width);
	
这中方法可以获取到空间大小，但是获取的值有时候不太准确。

####方法二

在onCreate中加入对整体布局的监听，代码如下：

	ViewTreeObserver vto2 = imageView.getViewTreeObserver();   
	vto2.addOnGlobalLayoutListener(new OnGlobalLayoutListener() { 
	    @Override   
	    public void onGlobalLayout() { 
	        imageView.getViewTreeObserver().removeGlobalOnLayoutListener(this);   
	        textView.append("\n\n"+imageView.getHeight()+","+imageView.getWidth()); 
	    }   
	});
	
这种方法和方法一一样，有时候获取到的大小不太准确。

####方法三（准确方法）

最准确获得控件大小的方式就是在onStart中加入绘制控件之前的监听，代码如下：

	ViewTreeObserver vto = imageView.getViewTreeObserver(); 
	vto.addOnPreDrawListener(new ViewTreeObserver.OnPreDrawListener() { 
	    public boolean onPreDraw() { 
	        int height = imageView.getMeasuredHeight(); 
	        int width = imageView.getMeasuredWidth(); 
	        textView.append("\n"+height+","+width); 
	        return true; 
	    } 
	});
	
这个方法，我是实验了好久，才找到的，这是目前最好的获取控件尺寸大小的方法了！













