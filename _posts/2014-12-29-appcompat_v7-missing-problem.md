---
layout: post
category: Android
title: Fix the appcompat_v7 missing problem
tagline: by hombo
tags: [android]
---

在升级了最新版的android ADT后，发现每次建立一个工程都会多出一个appcompat_v7的工程，很是奇怪，今天不小心把这个工程删除了，然后自己的工程就各种报错误，仔细看看错误一般是关于资源的，这是因为一些文件中用到appcompat_v7的一些资源，下面来说明如何更正这些文件，从而摆脱appcompat_v7。

<!--more-->

####第一步
到工程的Properties中的Android项中，把Library中的关于appcompat_v7的引用remove掉。如下图：

![](http://i.stack.imgur.com/ncXPr.png)

然后再将appcompat_v7工程给删除掉，这时你的工程会出现一些R资源未定义的错误，接下来的三步可以纠正这个错误。

####第二步
打开你的工程的res/values/styles.xml文件，将其中的`AppBaseTheme`的`parent`由`Theme.AppCompat.Light` 改为`@android:style/Theme.Light`

####第三步
打开工程的res/values-v11/styles.xml文件，将其中的`AppBaseTheme`的`parent`由`Theme.AppCompat.Light`改为`@android:style/Theme.Holo.Light`

####第四步
打开工程的rest/values-v14/styles.xml文件，将`AppBaseTheme`的`parent`由`Theme.AppCompat.Light.DarkActionBar`改为`@android:style/Theme.Holo.Light.DarkActionBar`

这样，在完成了一起上四个步骤后，就可以删除appcompat_v7了，重新编译自己的工程，应该可以正常运行。









