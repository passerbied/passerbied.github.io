---
layout: post
title: "iOS define for coding faster"
date: 2013-12-30 16:11:06 +0800
comments: true
categories: iOS
---

以下是我在开发中常用的可以提高开发效率的宏


 	/* fast make */
 
 	#define ccp(__X__,__Y__)             CGPointMake(__X__,__Y__)
 
 	#define ccs(__W__,__H__)             CGSizeMake(__W__,__H__)
 
 	#define ccr(__X__,__Y__,__W__,__H__) CGRectMake(__X__,__Y__,__W__,__H__)


 	/* fast creation */
 
 	#define IMG(name)       [UIImage imageNamed:(name)]
 
	#define IMG_VIEW(name)  [[UIImageView alloc] initWithImage:[UIImage imageNamed:(name)]]
 
	#define LocalStr(key)   NSLocalizedString(key,key)
 
	#define SFONT(size)     [UIFont systemFontOfSize:(size)]
 
 	#define FONT(s)         [UIFont fontWithName:@"Arial" size:(s)]
 
 	#define BFONT(size)     [UIFont boldSystemFontOfSize:(size)]
 
 	#define BUNDLE(name)    [[NSBundle mainBundle] pathForResource:(name)]
 
 	#define URL(url)        [NSURL URLWithString:url]
 	
 	
 	/* fast appending */
 	
 	#define ADD(__x__, __y__) [__x__ stringByAppendingString:__y__]


 	/* iOS_Version */
 
 	#define IS_IOS7 ((floor(NSFoundationVersionNumber) > NSFoundationVersionNumber_iOS_6_1))


 	/* Components size */
 	
 	#define SCREEN_WIDTH         [[UIScreen mainScreen] bounds].size.width
 
 	#define SCREEN_HEIGHT        [[UIScreen mainScreen] bounds].size.height
 
 	#define NAV_BAR_HEIGHT       44
 
 	#define NAV_BAR_HEIGHT_IOS7  64
 
 	#define TOOL_BAR_HEIGHT      44
 
 	#define TAB_BAR_HEIGHT       49
 
 	#define STATUS_BAR_HEIGHT    20


 	/* current language */
 
 	#define CN 1
 
 	#define ENG 2
 
 	#define JAN 3


 	/* color helper */
 
 	#define COLORRGBA(c,a) [UIColor colorWithRed:((c>>16)&0xFF)/255.0 \
                                       green:((c>>8)&0xFF)/255.0   \
                                        blue:(c&0xFF)/255.0        \
                                       alpha:a]
                                       
 	#define COLORRGB(c)    [UIColor colorWithRed:((c>>16)&0xFF)/255.0 \
                                       green:((c>>8)&0xFF)/255.0   \
                                        blue:(c&0xFF)/255.0        \
                                       alpha:1.0]

 	
 	/* log helper */
 
 	#define LOG_STR(str)     NSLog(@"%@", str)
 
 	#define LOG_INT(int)     NSLog(@"%d", int)
 
	#define LOG_FLOAT(float) NSLog(@"%f", float)
 
 	#define LOG_ERROR(exception,reason) NSLog(@"function:%s | line:%d | exception:%@ | reason:%@",__func__, __LINE__,exception,reason)

