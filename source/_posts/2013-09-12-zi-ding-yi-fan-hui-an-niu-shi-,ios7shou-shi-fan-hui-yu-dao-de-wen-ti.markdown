---
layout: post
title: "自定义返回按钮时，iOS7手势返回遇到的问题"
date: 2013-09-12 23:07:27 +0800
comments: true
categories: iOS
---
从iOS7的Beta版开始，就着手做兼容工作，到Beta4的時候，应用已经基本兼容，只是偶然发现，iOS样式的手势返回，也就是用`interactivePopGestureRecognizer`返回的时候,出现下面一些列问题。各方调研，无果，史无前例（废话，刚出来的7，上哪有例去--#）。

* 我的应用是自定义的返回按钮图标(默认返回按钮样式不会出现问题3)，为了保险，写了这句代码`[self.navigationItem setHidesBackButton:YES]`。
由于自定义返回按钮，所以iOS7自带返回手势无效。在需要的页面加上`navigationController.interactivePopGestureRecognizer.delegate = self`
返回手势好用了。

* 于是出现了第二个问题。
在一级视图中，iOS样式返回的手势滑动一下，然后进入二级视图，发现，画面卡住了，按Home键转入后台，再返回应用，发现并没有Crash掉，而是直接跳到了二级视图里，运行正常了，大家知道push和pop的原理是用进栈出栈完成的，可能因为在一级视图中滑动那一下，影响了视图在栈中的位置。
好，先解决一下这个问题，一级视图中一定要加入`self.navigationController.interactivePopGestureRecognizer.enabled = NO;`，先把iOS7手势返回屏蔽掉，到二级视图再用`self.navigationController.interactivePopGestureRecognizer.enabled = YES`打开。就Ok了。

* 好，第三个问题相继出现（其实是跟第二个一起出来的）。
手势返回拖动一半，放手，`navigationBar`上会出现三个小蓝点，而且位置不规律，可以肯定这个不是项目代码或者图片搞出来的东西，一定是SDK自己蹦出來的。
后台尝试发现`UIBarButtonItem`的`title`如果是nil的话，就会有这个问题。
解决方案：把`[self.navigationItem setHidesBackButton:YES];`去掉，然後把假装成返回按钮的`UIBarButtonItem`的`title`设置成`@""`。


* 大功告成。可见设计要是不按苹果规范来的话，就会遇到各种坑啊。