---
layout: post
title: "hide statusBar (also work on ios7)"
date: 2013-08-13 20:40:06 +0800
comments: true
categories: iOS
---

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