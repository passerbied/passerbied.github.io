---
layout: post
title: "文本自定义行高以后高度自适应的解决方法"
date: 2013-12-13 00:09:36 +0800
comments: true
categories: iOS
---

本文要说的是，*Frame高度*根据文本内容自适应，而非UITextView的滚动显示哦～（当然不是滚动显示，那属性就是UITextView继承过来直接用的）


* iOS7以前的最佳解决方案：

	自定义TextView继承UITextView并加入以下方法。*1.5em*可修改
		- (id)styleString {
    		return [[super styleString] stringByAppendingString:@"; line-height: 1.5em"];
		}
	然后在通过下面方法给frame赋值
	
		CGRect frame = _textView.frame;
		frame.size.height = _textView.contentSize.height;
		_textView.frame = frame;
		
	需要注意的是contentSize只在UITextView被**addSubview**以后才有效，所以要先把它加到画面上再，给它一个初始的frame，让它先得以计算出自己的contentSize。

* iOS7的最佳解决方案是：

	自定义TextView继承UITextView实现下面的init方法，给textView设定行高。

		- (id)initWithFrame:(CGRect)frame lineHeigth:(float)lineHeigth font:(UIFont *)font {
    	 
    	 	 self = [super initWithFrame:frame];
    	  	 if (self) {
        				  NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc] init];
	        
	        			  paragraphStyle.lineHeightMultiple = lineHeigth;
     	    			  paragraphStyle.maximumLineHeight = lineHeigth;
      	   			  	  paragraphStyle.minimumLineHeight = lineHeigth;
      	    		  	  NSDictionary *ats = 
      	    			  	         @{NSFontAttributeName:font,NSParagraphStyleAttributeName : paragraphStyle};
        	
        			  	  self.attributedText = [[NSAttributedString alloc] initWithString:@“ “ attributes:ats];
        			  	  self.lineHeight = lineHeigth;
    				    }
    	      return self;
		}

	然后通过以下方法计算出textView的实际高度，继而确定frame的height。
因为之前有人说UITextView默认的上下左右padding均为8px，也就是下面16的由来。 所以在计算时要考虑进去才能保证文本显示完整。

		+ (float) heightForTextView: (UITextView *)textView WithText: (NSString *) strText lineHeight:(float)height
		{
   		  float fPadding = 16.0;
  		  CGSize constraint = CGSizeMake(textView.contentSize.width - fPadding, CGFLOAT_MAX);
    
    	  CGSize size = [strText sizeWithFont:textView.font
    	  					constrainedToSize:constraint
    	                        lineBreakMode:UILineBreakModeWordWrap];
    
    	  float fHeight = size.height*height/textView.font.lineHeight + 16.0;
    	  return fHeight;
		}

	需要注意的是，本文讲的是自定义行高，所以，在下面方法里加上个lineHeight参与计算。

	其实从开发文档看，[NSMutableParagraphStyle](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/ApplicationKit/Classes/NSMutableParagraphStyle_Class/Reference/Reference.html) 在iOS6.0及以后都是有效的，但是我不知道为什么在iOS7上计算很精确，在iOS6上，结果却不令人满意。原因仍在调查中。

	不过，幸好有上面说的 <iOS7以前的最佳解决方案> ，要不然就要用<稍微差一点的解决方案>了，那么就顺带说一下<稍微差一点的解决方案>是什么吧^_^

* 稍微差一点的解决

	使用开源控件 > [TTTAttributedLabel](https://github.com/mattt/TTTAttributedLabel)
	
	这个控件之前由于性能问题一度被我遗弃过，后来版本更新以后，性能上来不少，但是仍然有些bug。我发现的bug就是当*字体小于12号，或者当行数过多，并且lineHeightMultiple>1*的时候，计算高度会存在偏差，行数越多偏差越大。
	
	那么也就是说，如果你一个文本不需要显示数百行，在此基础上对文本其他属性有追求，并且苦逼的要考虑兼容iOS7以下用户的话，可以使用 [TTTAttributedLabel](https://github.com/mattt/TTTAttributedLabel) ，因为这个控件在伴随有 [Text Kit](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/CustomTextProcessing/CustomTextProcessing.html) 的iOS7出来之前还是还是相当牛逼的，而更牛逼的 [Text Kit](https://developer.apple.com/library/ios/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/CustomTextProcessing/CustomTextProcessing.html) 让我们在文本显示领域看见了曙光！顺便奉劝一句，要是你的iOS7用户群达到95%以上，就果断抛弃那些抱着3年前的iPhone4而不敢升级系统的陈旧用户吧。