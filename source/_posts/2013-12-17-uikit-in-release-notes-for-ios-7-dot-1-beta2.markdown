---
layout: post
title: "UIKit in Release Notes for iOS 7.1 beta2"
date: 2013-12-17 12:17:22 +0800
comments: true
categories: 
---
Known Issues

- If a `UITextField` or a `UILabel` that is baseline aligned with constraints has attributes that change after the constraints have been added, the layout may be incorrect. The exception to this is `-setFont:` on `UILabel`, which should work as expected.

		Workaround: Avoid making changes in UITextField or UILabel after adding baseline-alignment constraints. If you must make changes, you should remove the constraints and then reapply them afterward. Note that this is a performance hit, so don’t do it unless it is necessary.

- The `backIndicatorTransitionMask` from a storyboard or a xib will not be interpreted correctly at runtime.
		
		Workaround: Set the backIndicatorTransitionMask in code.