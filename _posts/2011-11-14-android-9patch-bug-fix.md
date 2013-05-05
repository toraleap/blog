---
layout: post
title: Android SDK 9-patch 图像失效的解决方案
category: default
---

昨天将 Android SDK 升级到 ADT15 后，发现工程里的 9-patch 图像全部失效了，惨不忍睹，一番 Google 搜索后在这里找到了解决方案：

<http://codetrips.blogspot.com/2010/12/android-draw-9-patch-seems-broken-too.html>

原文墙外，简单搬运翻译如下： 
点击链接进入下载 [Swing Desktop](http://download.java.net/javadesktop/swinglabs/releases/0.8/)，然后把压缩包里的 swinglabs-0.8.0.jar 文件放到 [SDK安装位置]/tools/lib，重启 eclipse，在菜单里选择 Project –> Clean 即可。