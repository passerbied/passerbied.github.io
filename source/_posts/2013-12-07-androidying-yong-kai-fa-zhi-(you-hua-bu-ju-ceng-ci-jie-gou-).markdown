---
layout: post
title: "Android应用开发之（优化布局层次结构）"
date: 2012-02-07 14:00:58 +0800
comments: true
categories: Android
---


有一个常见的错误看法：只使用基础的布局结构会生产高效的布局。然而，每个您程序中的每个控件和布局都需要初始化、布局位置、和绘制。例如使用嵌套的LinearLayout可以导致很多的层次结构。更严重的是，如果在嵌套的LinearLayout中使用layout_weight参数将会导致更加严重的性能问题，因为里面的控件在计算大小的时候会计算两次。如果在[ListView](http://developer.android.com/reference/android/widget/ListView.html) 或者 [GridView](http://developer.android.com/reference/android/widget/GridView.html)中使用则会更加严重。

这节课程将会介绍如何使用 [Heirachy Viewer](http://developer.android.com/guide/developing/tools/hierarchy-viewer.html) 和[Layoutopt](http://developer.android.com/guide/developing/tools/layoutopt.html) 工具来检查和优化布局。

检查布局

在 Android SDK 中包含了一个href="http://developer.android.com/guide/developing/tools/hierarchy-viewer.html">Heirachy Viewer 工具，使用该工具可以在您的程序运行的时候分析程序的布局。使用该工具来发现布局中的瓶颈所在。

Hierarchy Viewer 让你选择其连接的设备或者模拟器中的一个运行中的程序，然后显示该界面的布局树形结构。在每个控件方块中的交通灯分别代表 计算尺寸(Measure)、布局子控件(Layout)、和绘制的性能，通过颜色来帮助您分析潜在的问题。

例如，下图显示了一个用于 [ListView](http://developer.android.com/reference/android/widget/ListView.html)的布局。该布局在左边显示一个小图标，在图标右边显示两行文本内容。当该布局会被多次使用(inflated)到的时候性能问题就比较明显， 比如这个例子 由于这个布局是在ListView中使用的， 所以每个ListView中的一项都会解析下该布局然后绘制出来。

<img src="http://m1.img.libdd.com/farm3/d/2012/0202/15/B69AD2A52C25B8A5892A0891C2C54659_B500_900_317_59.PNG"/>

hierarchyviewer 工具位于 <sdk>/tools/ 目录。打开后 Hierarchy Viewer 将显示一个可用设备的列表和正在运行的界面。点击 Load View Hierarchy 来查看选中的界面的布局分析结果。例如，下图显示了上面定义的ListView中每一项的布局结果。

<img src="http://m2.img.libdd.com/farm3/d/2012/0202/15/8FD77AA2EA1FBDF4D7D1DA76D3E940A7_B500_900_457_235.PNG"/>

查看渲染一个View的时间

上面左侧的图中，您可以看到有三层布局结构。在每个方块上面点击可以查看显示该控件所需要的时间。对于需要时间比较长的地方就是我们应该去重点优化的地方。

显示一个列表条目所需要的时间如下：

<img src="http://m2.img.libdd.com/farm3/d/2012/0202/15/96F675B3672137311CFAC7B280F4EA9B_B500_900_144_244.PNG"/>

Measure: 0.977ms

Layout: 0.167ms

Draw: 2.717ms

改进布局

上面的布局被一个嵌套的 [LinearLayout](http://developer.android.com/reference/android/widget/LinearLayout.html) 占用了过多的解析时间，如果把布局的嵌套层次降低可能会提高性能.使用 [RelativeLayout](http://developer.android.com/reference/android/widget/RelativeLayout.html) 可以实现这个功能。使用[RelativeLayout](http://developer.android.com/reference/android/widget/RelativeLayout.html)后可以看到现在布局由3层变为了2层，分析图如下

使用RelativeLayout后的层次结构

<img src="http://m2.img.libdd.com/farm3/d/2012/0202/15/11133BBC926776F3A1730AEA91818EA3_B500_900_376_293.PNG"/>

Figure 4. Layout hierarchy for the layout in figure 1,
using [RelativeLayout](http://developer.android.com/reference/android/widget/RelativeLayout.html).

现在显示一个列表条目所需要的时间如下：

Measure: 0.598ms

Layout: 0.110ms

Draw: 2.146ms

看起来是一个很微弱的提升，但是这个时间可以叠加多次，应为每个列表条目在显示的时候都有解析该布局。

当在[LinearLayout](http://developer.android.com/reference/android/widget/LinearLayout.html) 中使用 layout_weight 就又不一样了，这样会增加计算控件大小的时间。在使用layout_weight的时候您应该多考虑考虑。

使用 Layoutopt

在您的最终布局文件中运行下 [layoutopt](http://developer.android.com/tools/help/layoutopt.html) 工具来看看还有没有可以优化的空间是个很好的习惯。 Layoutopt 工具也位于SDK的 *tools/* 目录下，您可以用该工具优化单个文件也可以优化一个目录中的所有文件。

当您在单个文件中运行 layoutopt 后，该工具会打印出出现问题的行号和问题的描述，有些问题也会给出优化方案。例如

	$ layoutopt samples/
		samples/compound.xml
  			7:23 The root-level <FrameLayout/> can be replaced with <merge/>
  			11:21 This LinearLayout layout or its FrameLayout parent is useless
		samples/simple.xml
 			7:7 The root-level <FrameLayout/> can be replaced with <merge/>
当你修改了布局文件以后，可以再次运行一下 Hierarchy Viewer 来看看具体提升了多少性能。

from [Android Developer](http://androiddeveloper.diandian.com/post/2012-02-02/16147388)