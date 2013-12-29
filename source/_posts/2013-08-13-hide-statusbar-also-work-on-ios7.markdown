---
layout: post
title: "hide statusBar (also work on ios7)"
date: 2013-08-13 20:40:06 +0800
comments: true
categories: iOS
---
* 代码方式隐藏

	好处是灵活，随时隐藏随时显示

		//viewDidload

    	if ([self respondsToSelector:@selector(setNeedsStatusBarAppearanceUpdate)]) {

        	// iOS 7

        	[self prefersStatusBarHidden];

        	[self performSelector:@selector(setNeedsStatusBarAppearanceUpdate)];

    	} else {

        	// iOS 6

        	[[UIApplication sharedApplication] setStatusBarHidden:YES withAnimation:UIStatusBarAnimationSlide];

    	}

		// Add this Method

		- (BOOL)prefersStatusBarHidden
		{
    		return YES;
		}
		
* 在配置文件中设置
	
	如果整个应用都不想显示状态栏，那么这个更方便一些
	
	在`Info.plist`中添加如下两行键值对:
	1. `Status bar is initially hidden`（值设置成`YES`）
 
	2. `View controller-based status bar appearance`（值设置成`NO`）