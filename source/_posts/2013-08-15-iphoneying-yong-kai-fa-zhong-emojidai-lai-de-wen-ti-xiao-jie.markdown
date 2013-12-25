---
layout: post
title: "iPhone应用开发中emoji带来的问题小结"
date: 2013-08-15 20:36:32 +0800
comments: true
categories: iOS
---
如果是iPhone自带字体，emoji和文字的高度是不同的，有些细节的地方会造成布局出现瑕疵，我之前的做法是在

	- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string

方法里对输入内容进行判断，采取屏蔽emoji输入的方式，具体屏蔽办法请参见： [https://gist.github.com/cihancimen/4146056](https://gist.github.com/cihancimen/4146056)

但iOS7出来以后，通过测试发现7中修改和增加了一些emojide的编码。上述办法对于一些emoji的屏蔽失效。解决办法是找到合适的第三方字体包。同时，需要考虑iOS和PHP之前通信以及和其他平台通信时产生的问题。于是找到如下文章。

[http://wpceo.com/supporting-emoji-on-php-mysql/](http://wpceo.com/supporting-emoji-on-php-mysql/)