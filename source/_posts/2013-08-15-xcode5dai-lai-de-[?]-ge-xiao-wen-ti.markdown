---
layout: post
title: "Xcode5带来的一个小问题"
date: 2013-08-15 17:57:31 +0800
comments: true
categories: iOS
---

带有xib文件如果用Xcode5打开过，再用Xcode4.6打开，会出现如下错误：

`
Please use a newer version of Xcode. Consider changing the document's Development Target to preserve compatibility.
`

解决办法如下：

`
Open the Storyboard file on Xcode 5, And then: 1. Open file inspector 2. On "Interface Builder Documents" section change "Open with" to Xcode 4.6 (if it's 5.x). 3. Save, Close the project and open that with old Xcode.
`