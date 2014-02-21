---
layout: post
title: "Xcode5.1 updated to  Beta5"
date: 2014-02-21 15:39:49 +0800
comments: true
categories: iOS
---
```Xcode5.1 Beta5```和```iOS7.1Beta5```出来有一周时间了，这次更新苹果把Xcode里的64编译变成了默认，而没有了选项，如果你的项目编译时出了问题，请检查一下编码是否支持64位。

下面有一篇介绍如何为64位编译应用的文章，分享出来，多少会了解一些为64位应用编译所需要的基本东西。
[iOS 7: 如何为iPhone 5S编译64位应用。 ](http://blog.csdn.net/keyboardota/article/details/11993883)

如果您还没有充分准备好为64位编译您的应用，那么请在Xcode5.1项目的Target中```Build Settings```->```Architectures```->```Valid Architectures```中的```arm64```删掉，只保留```armv7```和```armv7s```即可。但终究是要兼容64位的，所以，还是抓紧准备吧。