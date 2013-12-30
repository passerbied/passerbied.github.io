---
layout: post
title: "Xperia TM GX UI适配遇到的小问题"
date: 2012-07-25 15:40:49 +0800
comments: true
categories: Android
---

Sony Xperia TM GX S0-04D 720*1280 和 S0-05D 均为软控制栏，最近在为这两款机器做适配时候遇到一些小问题，分享在这里。

 
* 问题1:

  现象：Sony上，从第二行LineaLayout开始往下窜一定距离。从其他机器和模拟器上均显示正常。

  原因：LinearLayout的Wrap_content属性在Sony和其它机器里的解析有所不同。

  改善：将高度设置成内容的实际高度dp值。
  

* 问题2:

  现象：4.0以后机种的SimpleOnGestureListener类手势问题。

  原因：onFiling方法触发频繁，导致更换背景的耗时操作使反映变卡。

  改善：更换背景耗时操作加入判断条件再触发。