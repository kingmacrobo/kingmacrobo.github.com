---
layout: post
category: Android
title: Volley duplicated post with slow request
tagline: by hombo
tags: [android]
---

最近做一个android的项目，遇到一个比较奇怪的问题：使用volley框架向服务器post几张图片，也就是图片的上传，当图片比较多或者比较大的时候，发现会触发好多次相同的post，现在记录一下问题的原因和解决方法。

<!--more-->

####出现多次post的原因
volley的post有两个超时时间，一个是retry的阈值时间t1，一个是post的超时时间timeout，当post的超时时间timeout大于retry的阈值时间时，就会产生这种想象，也就是在第一个post由于传输量比较大而没有完成之前，产生了retry，也就会产生额外的post。

####解决方法1
既然retry会造成这种错误，在传输量比较大的时候，我们干脆就不用retry，禁止掉retry的方式是设置retry的阈值为0，代码如下：

		postRequest.setRetryPolicy(new DefaultRetryPolicy(
				0, //设置retry的时间阈值
				DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
				DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
				
这种方式简单，但是禁止retry对功能上会产生一定的影响。

####解决方法2
修改retry的阈值时间，使它大于post的timeout时间，这种方式比较合理，一般的设置方法是将retry的阈值时间扩大到默认时间的k倍，k值视具体情况而定。代码如下：

		postRequest.setRetryPolicy(new DefaultRetryPolicy(
				DefaultRetryPolicy.DEFAULT_TIMEOUT_MS * k,
				DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
				DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));













