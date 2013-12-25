---
layout: post
title: "iOS获取当前系统语言"
date: 2013-05-18 15:40:14 +0800
comments: true
categories: iOS
---

添加如下代码：

	NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults]; 
	NSArray *languages = [defaults objectForKey:@"AppleLanguages"]; 
	NSString *currentLanguage = [languages objectAtIndex:0];

取得设置好的语言。。日语是ja，英文是en，其他。。就设置以后log出来看看吧！