---
layout: post
title: "Google API 方式制作饼状图"
date: 2012-12-11 11:00:48 +0800
comments: true
categories: Android
---
最简单的一种方式，Android上面使用`WebView`控件加载即可，以下是`GoogleAPI`相关内容。

PS：需要添加网络访问权限。

URL:
[http://chart.apis.google.com/chart?cht=p3&chs=250x100&chd=t:60,40&chl=Hello%7CWorld](http://chart.apis.google.com/chart?cht=p3&chs=250x100&chd=t:60,40&chl=Hello%7CWorld)



####Components

[https://chart.apis.google.com/chart?](https://chart.apis.google.com/chart?)

This is the base URL for all chart requests. (However, seeImproving Performance on Pages with Many Charts below for an optional variation for pages with multiple charts.)cht=p3The chart type: here, a 3D pie chart.chs=250x100The chart size (width xheight), in pixels. See the maximum valueshere.chd=t:60,40The chart data. This data is in simple text format, but there areother formats.chl=Hello|WorldTheslice labels.&Parameters are separated by an ampersand.Note: When embedding an URL in HTML, for example as thesrc attribute of an <img> tag, you should replace the& between parameters with the characters&amp; This includes when you generate page HTML with PHP or some other language. However, when typing a URL in the browser, or whencalling a URL in code, for example when fetching a URL in PHP or Perl, you should use the& mark.

