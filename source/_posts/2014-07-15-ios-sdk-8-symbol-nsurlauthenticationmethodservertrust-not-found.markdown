---
layout: post
title: " ios sdk 8 Symbol _NSURLAuthenticationMethodServerTrust not found"
date: 2014-07-15 10:27:58 +0800
comments: true
categories: 
---
今天把做到收尾部分的新项目放到Xcode6上用iOS8SDK运行，出现``ios sdk 8 Symbol _NSURLAuthenticationMethodServerTrust not found``错误。

解决方法分享一下：

由于用到的AFNetworking是用官方推进安装方式CocoaPods方式安装的。那么遇到这个问题就可以在pods项目下的``Targets Support Files``→``Pods``下面找到Pods.xcconfig文件，在里面找到``OTHER_CFLAGS``关键字里面的``-framework Foundation``，把``-framework Foundation``剪切到``-framework CFNetwork``的前面，保存，Clean and re-build/run

OK， 希望对你有用。

这个办法来自[https://github.com/AFNetworking/AFNetworking/issues/2109](https://github.com/AFNetworking/AFNetworking/issues/2109)