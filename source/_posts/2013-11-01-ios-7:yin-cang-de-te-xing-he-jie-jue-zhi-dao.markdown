---
layout: post
title: "iOS 7：隐藏的特性和解决之道"
date: 2013-11-01 14:14:51 +0800
comments: true
categories: iOS
---

<p>当 iOS7 刚发布的时候，全世界的苹果开发人员都立马尝试着去编译他们的app，接着再花上数月的时间来修复任何出现的故障，甚至重建这个app。这样的结果，使得人们根本无暇去探究 iOS7 所带来的新思潮。一些明显而细微的更新，比如说<code>[NSArray firstObject]</code>，这个方法可追溯到 iOS4 时代，现在被提为公有API，除此之外，还有很多隐藏的特性等着我们去挖掘。</p>
<h3>平滑淡入淡出动画</h3>
<p>我这里要讨论的并非新的弹性动画APIs 或者 UIDynamics，而是一些更细微的东西。CALayer增加了两个新方法：<code>allowsGroupOpacity</code>和<code>allowsEdgeAntialiasing</code>。现在，组不透明度(group opacity)不再是什么新鲜的东西了。iOS会多次使用存在于 Info.plist 中的键UIViewGroupOpacity并可在应用程序范围内启用或禁用它。对于大多数apps而言，这（译注：启用）并非所期望的，因为它会降低整体性能。在 iOS7 中，用 SDK7 所链接的程序，这项属性默认是启用的。当它被启用时，一些动画将会变得不流畅，它也可以在layer层上被控制。</p>
<p>一个有趣的细节，如果<code>allowsGroupOpacity</code>启用的话，_UIBackdropView（在UIToolbar或者UIPopoverView中的背景视图）不能对其模糊进行动画处理，所以当你做一个alpha转换时，你可能会临时禁用这项属性。因为这会降低动画体验，你可以回退到旧的方式然后在动画期间临时启用<code>shouldRasterize</code>。别忘了设置适当的<code>rasterizationScale</code>，否则在retina的设备上这些视图会成锯齿状。</p>
<p>如果你想要复制的 Safari 显示所有选项卡时的动画，那么边缘抗锯齿属性将变得非常有用。</p>
<h3>阻塞动画</h3>
<p>一个小但非常有用的新方法<code>[UIView performWithoutAnimation:]</code>。它是一个简单的封装，先检查动画当前是否启用，然后禁止动画，执行块语句，最后重新启用动画。一个需要说明的地方是，它并不会阻塞基于 CoreAnimation 的动画。因此，不用急于将你的方法调用从：</p>
<pre class="brush: c; gutter: true">[CATransaction begin];
[CATransaction setDisableActions:YES];
view.frame = CGRectMake(...);
[CATransaction commit];
</pre>
<p>替换为：</p>
<pre class="brush: c; gutter: true">[UIView performWithoutAnimation:^{
    view.frame = CGRectMake(...);
}];
</pre>
<p>但是，绝大多数情况下这样也能工作的很好，只要你不直接处理CALayers。</p>
<p>iOS7 中，我有很多代码路径（主要是 UITableViewCells）需要额外的保护，防止意外的动画，例如，如果一个弹窗的大小调整了，那么同时显示中的表视图将因为高度的变化而加载新的cell。我通常的做法是将整个 layoutSubviews 的代码包扎到一个动画块中：</p>
<pre class="brush: actionscript3; gutter: true">- (void)layoutSubviews 
{
    // Otherwise the popover animation could leak into our cells on iOS 7 legacy mode.
    [UIView performWithoutAnimation:^{
        [super layoutSubviews];
        _renderView.frame = self.bounds;
    }];
}
</pre>
<h3>处理长表视图</h3>
<p>UITableView 非常快速高效，除非你开始使用<code>tableView:heightForRowAtIndexPath:</code>，它会开始为你表中任意元素调用此方法，即便没有可视对象，就比如其内在的UIScrollView只是去获取正确的<code>contentSize</code>。此前有一些变通方法，但都不好用。iOS7 中，苹果公司终于承认这一问题，并添加<code>tableView:estimatedHeightForRowAtIndexPath:</code>，这个方法延迟了实际滚动时间成本的大部分。如果你不知道一个cell的大小，返回<code>UITableViewAutomaticDimension</code>即可。</p>
<p>对于节头/尾（section headers/footers），现在也有类似的API了。</p>
<h3>UISearchDisplayController</h3>
<p>苹果的 search controller 使用了新的技巧来简化移动 search bar 到 navigation bar 的过程。启用&nbsp;<code>displaysSearchBarInNavigationBar&nbsp;</code>就可以了（除非你还要用到 scope bar，我只能说你真不幸）。我倒是很喜欢这么做，但比较遗憾的是，iOS7 上的 UISearchDisplayController 貌似被摧残的比较严重，尤其是iPad。苹果公司看上去像是没时间处理这个问题的样子（原文：Apple seems to have run out of time），对于显示的搜索结果并不会隐藏实际的表视图。在 iOS7 之前，这并没有问题，但是现在 searchResultsTableView 有一个透明的背景色，使它看上去相当糟糕。作为一种变通方法，你可以设置不透明色或者取道于<a href="http://petersteinberger.com/blog/2013/fixing-uisearchdisplaycontroller-on-ios-7/">富于技巧的手段</a>来获得你所期望的。关于这个控件会出现各种各样的结果，当使用<code>displaysSearchBarInNavigationBar</code>时甚至不会展示搜索表视图。</p>
<p>你的结果可能有所不同，但我是使用了一些手段来让<code>displaysSearchBarInNavigationBar</code>工作的：</p>
<pre class="brush: actionscript3; gutter: true">- (void)restoreOriginalTableView 
{
    if (PSPDFIsUIKitFlatMode() &amp;&amp; self.originalTableView) {
        self.view = self.originalTableView;
    }
}

- (UITableView *)tableView 
{
    return self.originalTableView ?: [super tableView];
}

- (void)searchDisplayController:(UISearchDisplayController *)controller 
  didShowSearchResultsTableView:(UITableView *)tableView 
{
    // HACK: iOS 7 requires a cruel workaround to show the search table view.
    if (PSPDFIsUIKitFlatMode()) {
        if (!self.originalTableView) self.originalTableView = self.tableView;
        self.view = controller.searchResultsTableView;
        controller.searchResultsTableView.contentInset = UIEdgeInsetsZero; // Remove 64 pixel gap
    }
}

- (void)searchDisplayController:(UISearchDisplayController *)controller 
  didHideSearchResultsTableView:(UITableView *)tableView 
{
    [self restoreOriginalTableView];
}
</pre>
<p>这里，别忘了在<code>viewWillDisappear</code>中调用<code>restoreOriginalTableView</code>，否则会发送crash。</p>
<p>记住这是唯一的解决办法；可能有不少激进的方法不替换视图本身，但这个问题确实应该由苹果公司来修复。（TODO: RADAR!）</p>
<h3>分页</h3>
<p>UIWebView 使用了新的技巧来自动分页带<code>paginationMode</code>的网站。有一大堆与此功能相关的新属性：</p>
<pre class="brush: actionscript3; gutter: true">@property (nonatomic) UIWebPaginationMode paginationMode NS_AVAILABLE_IOS(7_0);
@property (nonatomic) UIWebPaginationBreakingMode paginationBreakingMode NS_AVAILABLE_IOS(7_0);
@property (nonatomic) CGFloat pageLength NS_AVAILABLE_IOS(7_0);
@property (nonatomic) CGFloat gapBetweenPages NS_AVAILABLE_IOS(7_0);
@property (nonatomic, readonly) NSUInteger pageCount NS_AVAILABLE_IOS(7_0);
</pre>
<p>现在而言，虽然这可能并非对于大多数网站都有用，但它肯定是生成简单的电子书阅读器或显示文本的一种更好的方式。加点乐子的话，请尝试将它设置为<code>UIWebPaginationModeBottomToTop</code>。</p>
<h3>会飞的 Popovers</h3>
<p>想知道为什么你的popovers疯了一样到处乱飞？在<code>UIPopoverControllerDelegate</code>协议中有一个新的代理方法使你能控制它：</p>
<pre class="brush: actionscript3; gutter: true">-     (void)popoverController:(UIPopoverController *)popoverController
  willRepositionPopoverToRect:(inout CGRect *)rect 
                       inView:(inout UIView **)view
</pre>
<p>当popover锚点是指向一个UIBarButtonItem时，UIPopoverController会有一些动作，但如果你让它在一个view或者rect中显示，你可能就需要实现此方法并正常返回。一个花费了我相当长的时间来验证的问题&mdash;&mdash;如果你通过改变<code>preferredContentSize</code>来动态调整你的popovers，那么这个方法就特别要求得以实现。苹果公司现在对改变popovers大小的请求更严格，如果没有预留足够的空间，popover将会到处移动。</p>
<h3>键盘支持</h3>
<p>苹果公司不只为我们提供了<a href="https://developer.apple.com/library/ios/documentation/ServicesDiscovery/Conceptual/GameControll">全新的framework用于游戏控制器</a>，它也给了我们这些键盘爱好者一些提示！你会发现新定义的公用键像&nbsp;<code>UIKeyInputEscape</code>&nbsp;或&nbsp;<code>UIKeyInputUpArrow</code>，可以使用所有新的&nbsp;<a href="https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIKeyCommand_class/Reference/Reference.html#//apple_ref/occ/instp/UIKeyCommand/input"><code>UIKeyCommand</code></a>&nbsp;类截查。在 iOS7 之前，只能通过一些<a href="http://petersteinberger.com/blog/2013/adding-keyboard-shortcuts-to-uialertview/">难以言表的手段来处理键盘命令</a>，现在，就让我们操起蓝牙键盘试试看我们能用这个做什么！</p>
<p>开始之前，你需要对责任者链有个了解。你的 UIApplication 继承自 UIResponder，UIView 和 UIViewController 也是如此。如果你处理过 UIMenuItem 并且没有使用<a href="https://github.com/steipete/PSMenuItem">我的基于块的包装</a>的话，那么你已经了解了这些。事件先被发送到最上层的响应者，然后一级级往下传递直到 UIApplication 。为了捕获按键命令，你需要告诉系统你关心哪些键命令（而不是全捕获）。为了完成这个，你需要重写<code>keyCommands</code>这个新属性：</p>
<pre class="brush: actionscript3; gutter: true">- (NSArray *)keyCommands 
{
    return @[[UIKeyCommand keyCommandWithInput:@"f"
                                 modifierFlags:UIKeyModifierCommand  
                                        action:@selector(searchKeyPressed:)]];
}

- (void)searchKeyPressed:(UIKeyCommand *)keyCommand 
{
    // Respond to the event
}
</pre>
<div class="RIL_IMG loaded" id="RIL_IMG_1"><img src="http://img.readitlater.com/i/ww4.sinaimg.cn/large/7cc829d3gw1eascrhdud2j20q80ugdl2/RS/w1280.jpg" /></div>
<p>现在可别太激动，需要注意的是，这个方法只在键盘可见时有效（比如有类似 UITextView 这样的对象作为第一响应者时）。对于全局热键，你仍然需要用上面的方法。除却那些，这个路径还是很优雅的。不要覆盖类似 cmd-V 系统的快捷键，它会被自动映射为粘贴功能。</p>
<p>还有一些新的预定义的响应行为如：</p>
<pre class="brush: actionscript3; gutter: true">- (void)increaseSize:(id)sender NS_AVAILABLE_IOS(7_0);
- (void)decreaseSize:(id)sender NS_AVAILABLE_IOS(7_0);
</pre>
<p>它们分别对应着 cmd+ 和 cmd- 命令，用来放大/缩小内容。</p>
<h3>匹配键盘背景</h3>
<p>苹果公司终于公开了&nbsp;<a href="https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIInputView_class/Reference/Reference.html">UIInputView</a>，其中提供了一种方式&mdash;&mdash;使用<code>UIInputViewStyleKeyboard</code>来匹配键盘样式。这使得你可以编写自定义的键盘或者带默认样式的默认键盘扩展（工具条）。这个类以前就<a href="https://github.com/nst/iOS-Runtime-Headers/commits/master/Frameworks/UIKit.framework/UIInputView.h">存在</a>了，不过现在我们终于可以绕过私有API的方式来使用它了。</p>
<p>如果 UIInputView 是一个 inputView 或者 inputAccessoryView 的根视图，它将只显示一个背景，否则它将是透明的。遗憾的是，这并不能让你实现一个未填充的分离态的键盘，但它仍然比用一个简单的 UIToolbar 要好。我还没看到苹果在何处使用这个新API，貌似它只作为一个 UIToolbar 使用在 Safari 上。</p>
<h3>了解你的网络</h3>
<p>虽然早在 iOS4 的时候，关于网络信息的大部分已经在 CTTelephony 暴露了，但它通常只用于特定场景并非十分有用。iOS7 中，苹果公司为其添加了一个方法，其中最有用的：<code>currentRadioAccessTechnology</code>。这个使你能知晓手机是处于较慢的GPRS还是高速的LTE或者介于其中。目前还没有方法得到连接速度（当然手机本身也无法获取这个），但是这足以用来优化一个下载管理器，让其在EDGE下不用尝试同时去下载6张图片了。</p>
<p>现在还没有<code>currentRadioAccessTechnology</code>的相关文档，因此存在一些不正规或者错误的用法。当你想要获取当前网络信号值，你应当注册一个<code>CTRadioAccessTechnologyDidChangeNotification</code>通知而不应该去轮询这个属性。为了获取这些通知，你需要使用<code>CTTelephonyNetworkInfo</code>的一个实例，注意不要在通知中创建 CTTelephonyNetworkInfo 的实例，否则会 crash。</p>
<p>在这个简单的例子中，我在block中捕获并持有了 telephonyInfo，大家可以忽略这个：</p>
<pre class="brush: actionscript3; gutter: true">CTTelephonyNetworkInfo *telephonyInfo = [CTTelephonyNetworkInfo new];
NSLog(@"Current Radio Access Technology: %@", telephonyInfo.currentRadioAccessTechnology);
[NSNotificationCenter.defaultCenter addObserverForName:CTRadioAccessTechnologyDidChangeNotification 
                                                object:nil 
                                                 queue:nil 
                                            usingBlock:^(NSNotification *note) 
{
    NSLog(@"New Radio Access Technology: %@", telephonyInfo.currentRadioAccessTechnology);
}];
</pre>
<p>当手机从edge环境到3G，log输出应该像这样：</p>
<p>iOS7Tests[612:60b] Current Radio Access Technology: CTRadioAccessTechnologyEdge<br />iOS7Tests[612:1803] New Radio Access Technology: (null)<br />iOS7Tests[612:1803] New Radio Access Technology: CTRadioAccessTechnologyHSDPA</p>
<p>苹果导出了所有字符串符号，因此可以很简单的比较和检测当前的网络信息。</p>
<h3>Core Foundation 和 Autorelease</h3>
<p>Core Foundation中出现了一个新的方法，它被用于私有调用已有数年时间：</p>
<div>
<pre>CFTypeRef CFAutorelease(CFTypeRef CF_RELEASES_ARGUMENT arg)</pre>
</div>
<p>它确实做了你所期望的事，让人费解的是苹果花了这么长时间才把它公开。ARC 下，大多数人在处理返回 Core Foundation 对象时是通过转换成对等的 NS 对象来完成的，如 NSDictionary,即便它只是一个 CFDictionaryRef 然后简单地&nbsp;<code>CFBridgingRelease()&nbsp;</code>。这样通常没问题，除非你返回的对等 NS 对象不可用时，如 CFBagRef。你要么使用 id，这样会失去类型安全性，要么你将你的方法重命名为 createMethod 并考虑所有的内存语义，最后使用 CFRelease。还有一些手段，比如<a href="http://favstar.fm/users/AndrePang/status/18099774996">这个</a>，用 non-ARC-file 标签然后编译，但终归得使用<code>CFAutorelease()</code>。另外：不要编写使用苹果公司命名空间的代码，所有这些自定义的 CF-宏将来都会被打破的。</p>
<h3>图片解压缩</h3>
<p>当通过 UIImage 展示一张图时，在显示之前需要解压缩（除非源已经像素缓存了）。对于 JPG/PNG 文件这会占用相当可观的时间并会造成卡顿。iOS6 以前，通常是创建一个位图上下文，然后在其中画图来解决。（<a href="https://github.com/AFNetworking/AFNetworking/blob/09658b352a496875c91cc33dd52c3f47b9369945/AFNetworking/AFURLResponseSerialization.m#L442-518">参见 AFNetworking 如何处理</a>）。</p>
<p>iOS7 开始，你可以使用<code>kCGImageSourceShouldCacheImmediately:</code>来强制图片在创建时立即解压缩：</p>
<pre class="brush: actionscript3; gutter: true">+ (UIImage *)decompressedImageWithData:(NSData *)data 
{
    CGImageSourceRef source = CGImageSourceCreateWithData((__bridge CFDataRef)data, NULL);
    CGImageRef cgImage = CGImageSourceCreateImageAtIndex(source, 0, (__bridge CFDictionaryRef)@{(id)kCGImageSourceShouldCacheImmediately: @YES});

    UIImage *image = [UIImage imageWithCGImage:cgImage];
    CGImageRelease(cgImage);
    CFRelease(source);
    return image;
}
</pre>
<p>当我刚发现这一点时确实很兴奋，但事实并非如此。在我的测试中，发现当开启了即时缓存后性能有明显的降低。要么这个方法是在主线程中调用的（不太可能），感觉上性能更糟，因为它在方法<code>copyImageBlockSetJPEG</code>中锁住了，而同时在主线程中在显示非加密的图片所致。在我的程序中，我在主线程中加载小的预览图，在后台线程中加载大型图，使用了<code>kCGImageSourceShouldCacheImmediately</code>后小小的解压缩阻塞了主线程，同时在后台处理大量开销昂贵的操作。</p>
<div class="RIL_IMG loaded" id="RIL_IMG_2"><img src="http://img.readitlater.com/i/ww2.sinaimg.cn/large/7cc829d3gw1eascrg8oyhj210s1k2n9j/RS/w1280.jpg" /></div>
<p>还有更多关于图片解压缩相关的却不是 iOS7 中的新东西，像<code>kCGImageSourceShouldCache</code>，它用来控制系统自动卸载解压缩的图片数据的能力。确保你将它设置为YES，否则所有的工作都将没有意义。有趣的是，苹果在64bit运行时的系统中将<code>kCGImageSourceShouldCache</code>的默认值从 NO 改为了 YES。</p>
<h3>盗版检查</h3>
<p>苹果添加了一个方式，通过 NSBunble 上的新方法<a href="https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/Reference/Reference.html#//apple_ref/occ/instm/NSBundle/appStoreReceiptURL"><code>appStoreReceiptURL</code></a>来评估Lion系统上 App Store 的收据，同时也将其移植到了 iOS 上。这使得你可以检查你的应用是在被合法购买或者已经被破解了。检查收据还有一个重要的原因，它包含了初始购买日期，这点对于把你的应用从付费模型迁移到免费+应用内付费方式很有帮助意义。你可以根据这个初始购买日期来决定额外内容对于你的用户是免费的还是收费的。</p>
<p>收据还允许你检查应用程序是否通过批量购买计划购买以及该许可证是否仍有效，有一个名为<code>SKReceiptPropertyIsVolumePurchase</code>的属性显示了该值。</p>
<p>当你调用<code>appStoreReceiptURL</code>时，你需要特别注意，因为在 iOS6 上，它还是一个私有API，你应该在用户代码中先调用<code>doesNotRecognizeSelector:</code>，在调用前检查运行（基础）版本。在开发期间，这个方法返回的 URL 不会是指向一个文件。你可能需要使用 StoreKit 的<a href="https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKReceiptRefreshRequest_ClassRef/SKReceiptRefreshRequest.html"><code>SKReceiptRefreshRequest</code></a>，这也是 iOS7 中的新东西，用它来下载证书。使用一个至少购买过一次的测试用户，否则它将没法工作：</p>
<pre class="brush: actionscript3; gutter: true">// Refresh the Receipt
SKReceiptRefreshRequest *request = [[SKReceiptRefreshRequest alloc] init];
[request setDelegate:self];
[request start];
</pre>
<p>验证收据需要大量的代码。你需要使用OpenSSL和内嵌的<a href="http://www.apple.com/certificateauthority/">苹果根证书</a>，并且你还要了解一些基本的东西像是证书、<a href="http://en.wikipedia.org/wiki/PKCS">PCKS容器</a>以及<a href="http://de.wikipedia.org/wiki/Abstract_Syntax_Notation_One">ASN.1</a>。这里有一些<a href="https://github.com/rmaddy/VerifyStoreReceiptiOS">样例</a>代码，但是你不应该让它这么简单&mdash;&mdash;别只是拷贝现有的验证方法，至少做点修改或者编写你自己的，你应该不希望一个普通的补丁程序就能在数秒内瓦解你的努力吧。</p>
<p>你绝对应该读读苹果的指南&mdash;&mdash;<a href="https://developer.apple.com/library/mac/releasenotes/General/ValidateAppStoreReceipt/index.html">验证 Mac App 商店收据</a>，这里面的大多数都适用于 iOS。苹果在&nbsp;<a href="https://developer.apple.com/wwdc/videos/">WWDC2013 的 Session308 &ldquo;Using Receipts to Protect Your Digital Sales&rdquo;</a>&nbsp;中详述了&ldquo;Grand Unified Receipt&rdquo;的变动。</p>
<h3>Comic Sans MS</h3>
<p>iOS7 中，终于迎回了 Comic Sans MS。现在，它以可下载的字体被添加到 iOS6 中，但当时的字体列表很少也不见得多么有趣。在 iOS7 中苹果添加了不少字体，包括&ldquo;famous&rdquo;，它和&nbsp;<a href="http://www.fontsquirrel.com/fonts/PT-Sans">PT Sans</a>&nbsp;或&nbsp;<a href="http://sixrevisions.com/graphics-design/comic-sans-the-font-everyone-loves-to-hate/">Comic Sans MS</a>&nbsp;有些类似。<code>kCTFontDownloadableAttribute</code>并没有在 iOS6 中声明，所以 iOS7 以前它并不真正可用，但苹果确是在 iOS6 的时候就已经做了私有声明了。</p>
<div class="RIL_IMG loaded" id="RIL_IMG_3"><img src="http://img.readitlater.com/i/ww3.sinaimg.cn/large/7cc829d3gw1eascrig015j20n00v2784/RS/w1280.jpg" /></div>
<p>字体列表是<a href="http://mesu.apple.com/assets/com_apple_MobileAsset_Font/com_apple_MobileAsset_Font.xml">动态变化</a>的，以后可能就会发生变动。苹果在&nbsp;<a href="http://support.apple.com/kb/HT5484">Tech Note HT5484</a>&nbsp;中罗列了一些可用的字体，但这个文档已经过时了，同时也不能反映 iOS7 的变化。</p>
<p>这里显示了你该如何获取一个用<code>CTFontDescriptorRef</code>标示可下载的字体数组：</p>
<pre class="brush: actionscript3; gutter: true">CFDictionary *descriptorOptions = @{(id)kCTFontDownloadableAttribute : @YES};
CTFontDescriptorRef descriptor = CTFontDescriptorCreateWithAttributes((CFDictionaryRef)descriptorOptions);
CFArrayRef fontDescriptors = CTFontDescriptorCreateMatchingFontDescriptors(descriptor, NULL);
</pre>
<p>系统不会检查字体是否已存在于磁盘上而将直接返回同样的列表。另外，这个方法可能会启用网络并造成阻塞，你不应该在主线程中使用它。</p>
<p>使用如下基于块的 API 来下载字体：</p>
<pre class="brush: actionscript3; gutter: true">bool CTFontDescriptorMatchFontDescriptorsWithProgressHandler(
         CFArrayRef                          descriptors,
         CFSetRef                            mandatoryAttributes,
         CTFontDescriptorProgressHandler     progressBlock)
</pre>
<p>这个方法能操作网络并传递下载进度信息来调用你的<code>progressBlock</code>方法直到下载成功或者失败。参考苹果的&nbsp;<a href="http://test-0x01.logdown.com/de/DownloadFont/Listings/DownloadFont_ViewController_m.html">DownloadFont</a>&nbsp;样例看如何使用它。</p>
<p>有一些值得注意的地方，这里的字体只在当前程序周期内有效，下次运行将被重新载入内存。因为字体存放在共享空间中，你不能依赖于它们是否可用。很有可能也不能保证的说，系统会清理这个目录，或者你的程序被拷贝到新的设备环境中，而这时又没有这个字体存在，同时当前处于没有网络的环境中。在 Mac 或是模拟器上，你能根据<code>kCTFontURLAttribute</code>获得字体的绝对路径，加载速度也会提升，但是在 iOS 上是不可能的，因为这个目录在你程序之外，你需要再次调用<code>CTFontDescriptorMatchFontDescriptorsWithProgressHandler</code>。</p>
<p>你也可以注册新的<code>kCTFontManagerRegisteredFontsChangedNotification</code>通知来跟踪新字体在何时载入到了字体注册表中。你可以在&nbsp;<a href="https://developer.apple.com/wwdc/videos/">WWDC2013 的 Session223 &ldquo;Using Fonts with TextKit&rdquo;</a>中查找更多信息。</p>
<h3>这还不够？</h3>
<p>没关系，iOS7 的新东西远不止如此！<a href="http://nshipster.com/ios7/">了解一下 NSHipster</a>&nbsp;你将明白语音合成相关的东西，base64、NSURLComponents、NSProgress、bar codes、reading lists 以及 CIDetectorEyeBlink。还有很多我们没有涵盖到的，比如苹果&nbsp;<a href="https://developer.apple.com/library/ios/releasenotes/General/iOS70APIDiffs/index.html#//apple_ref/doc/uid/TP40013203">iOS7 的 API 变化</a>，<a href="https://developer.apple.com/library/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS7.html">iOS 指南的新东西</a>以及&nbsp;<a href="https://developer.apple.com/library/prerelease/mac/releasenotes/Foundation/RN-Foundation/index.html#//apple_ref/doc/uid/TP30000742">Foundation Release Notes</a>（这些都是服务于 OS X的，但是代码都是共享的，也同样适用于 iOS）。很多方法都还没形成文档，等着你来探究和 blog。</p>
<p></p>
<p>原文链接：&nbsp;<a href="http://www.objc.io/issue-5/iOS7-hidden-gems-and-workarounds.html" target="_blank">Peter Steinberger</a>&nbsp;翻译：&nbsp;<a href="http://blog.jobbole.com/">伯乐在线</a>&nbsp;-&nbsp;<a href="http://blog.jobbole.com/author/jobbole/">伯乐在线读者</a><br />译文链接：&nbsp;<a href="http://blog.jobbole.com/51761/">http://blog.jobbole.com/51761/</a><br />[&nbsp;转载必须在正文中标注并保留原文链接、译文链接和译者等信息。]</p>
