---
layout: post
title: "项目协作中Xcode Search Paths问题"
date: 2014-03-12 12:23:11 +0800
comments: true
categories: iOS
---
Xcode引用库的时候，编译器会自动添加一个库文件的绝对路径进去，这种情况下，如果其他人从repo上pull下来编译项目就容易出问题，因为找不到之前那个人引用路径下存放的文件了嘛。

那么，引用库或使用CocoaPods的时候，要注意手动把Build Settings里面的`Framework Search Paths`,`Header Search Paths`,` Library Search Paths`中的绝对路径都删掉，改成相对路径，最简单的办法时，全部删掉，然后加入一个`../**`即可。

顺便，最近Xcode5.1升级导致诸多在架构编译层面的问题。其中一个就是Xcode5.1默认是64位编译器编译。

手动关闭的方法是：选中`Targets`—>`Build Settings`—>`Architectures`。双击`Architectures`，选择`other`，删除`$(ARCHS_STANDARD)`，然后增加`armv7`和`armv7s`，clean一下再编译。
官方不推荐将`Valid Architectures`的内容修改，所以最好使用上面的方法关闭64位编译。