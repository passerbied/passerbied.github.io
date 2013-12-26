---
layout: post
title: "hide StatusBar on iOS"
date: 2013-12-25 18:49:05 +0800
comments: true
categories: iOS
---

在`Info.plist`中添加如下两行键值对:

 1. `Status bar is initially hidden`（值设置成`YES`）
 
 2. `View controller-based status bar appearance`（值设置成`NO`）