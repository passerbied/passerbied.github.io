---
layout: post
title: "UITableView隐藏多余的分割线"
date: 2013-03-07 15:18:45 +0800
comments: true
categories: iOS
---

	- (void)setExtraCellLineHidden: (UITableView *)tableView
	{
   	 	UIView *view = [UIView new];
    	view.backgroundColor = [UIColor clearColor];
    	[tableView setTableFooterView:view];
	}


当tableview的dataSource为空时，也就是没有数据可显示时，该方法无效，只能在numberOfRowsInsection函数，通过判断dataSouce的数据个数，如果为零可以将tableview的
separatorStyle设置为UITableViewCellSeparatorStyleNone去掉分割线，然后在大于零时将其设置为
UITableViewCellSeparatorStyleSingleLine


顺便，如果想仿造TableView的分割线默认样式，做个单条的分割线的话，
可以自己创建一个高度为1.25,颜色值为0xe7e7e7的UIImageview来伪装一下。