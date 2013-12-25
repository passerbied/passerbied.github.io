---
layout: post
title: "[iOS开发]关于导入带有数据的SQLite文件"
date: 2012-12-18 18:23:37 +0800
comments: true
categories: iOS
---
DB文件需要引入到项目中的`Supporting Files`文件夹下。

注意：直接拖拽的形式引入时，需要勾选`Add to tagets`选项，同时根据情况注意勾选上面的`Copy`选项。

模拟器调试情况下，DB文件的存放路径如下：
`当前用户/Library/Application Support/iPhone Simulator/版本号(6.0)/Applications/你的app名所在文件夹/Documents`

