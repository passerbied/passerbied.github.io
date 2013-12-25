---
layout: post
title: "AndroidManifest.xml部分属性说明"
date: 2012-04-12 19:44:38 +0800
comments: true
categories: Android
---
- android:allowTaskReparenting

    用法`<application android:allowTaskReparenting="true/false"></application>`
    是否允许Activity更换从属的任务，比如从短信息任务 切换到浏览器任务。用来标记Activity能否从启动的Task移动到有着affinity的Task（当这个Task进入到前台时）—— “true”，表示能移动，“false”，表示它必须呆在启动时呆在的那个Task里。

    如果这个特性没有被设定，设定到`<application>`元素上的`allowTaskReparenting`特性的值会应用到`Activity`上。默认值为“false”。

    一般来说，当Activity启动后，它就与启动它的Task关联，并且在那里耗尽它的整个生命周期。当当前的Task不再显示时，你可以使用这个特性来 强制Activity移动到有着`affinity`的Task中。典型用法是：把一个应用程序的Activity移到另一个应用程序的主Task中。

    例如，如果e－mail中包含一个web页的链接，点击它就会启动一个Activity来显示这个页面。这个Activity是由Browser应用程序 定义的，但是，现在它作为`e－mail Task`的一部分。如果它重新宿主到`Browser Task`里，当Browser下一次进入到前台时，它就能被看见，并且，当`e－mail Task`再次进入前台时，就看不到它了。

    Actvity的`affinity`是由`taskAffinity`特性定义的。Task的`affinity`是通过读取根Activity的`affinity` 决定。因此，根据定义，根Activity总是位于相同`affinity`的Task里。由于启动模式为“singleTask”和 “singleInstance”的Activity只能位于Task的底部，因此，重新宿主只能限于“standard”和“singleTop”模 式。

- android:alwaysRetainTaskState

    用法`<activity android:alwaysRetainTaskState="true/false"></activity>`
    用来标记Activity所在的Task的状态是否总是由系统来保持——“true”，表示总是；“false”，表示在某种情形下允许系统恢复Task 到它的初始化状态。默认值是“false”。这个特性只针对Task的根Activity有意义；对其它Activity来说，忽略之。
    一般来说，特定的情形如当用户从主画面重新选择这个Task时，系统会对这个Task进行清理（从stack中删除位于根Activity之上的所有Activivity）。典型的情况，当用户有一段时间没有访问这个Task时也会这么做，例如30分钟。
    然而，当这个特性设为“true”时，用户总是能回到这个Task的最新状态，无论他们是如何启动的。这非常有用，例如，像Browser应用程序，这里有很多的状态（例如多个打开的Tab），用户不想丢失这些状态。

- android:clearTaskOnLanunch

     用法`<activity android:clearTaskOnLanunch=”true/false”></activity>`
     用来标记是否从Task中清除所有的Activity，除了根Activity外（每当从主画面重新启动时）——“true”，表示总是清除至它的 根 Activity，“false”表示不。默认值是“false”。这个特性只对启动一个新的Task的Activity（根Activity）有意义； 对Task中其它的Activity忽略。
     当这个值为“true”，每次用户重新启动这个Task时，都会进入到它的根Activity中，不管这个Task最后在做些什么，也不管用户是使 用 BACK还是HOME离开的。当这个值为“false”时，可能会在一些情形下（参考alwaysRetainTaskState特性）清除Task的 Activity，但不总是。
     假设，某人从主画面启动了Activity P，并从那里迁移至Activity Q。接下来用户按下HOME，然后返回Activity P。一般，用户可能见到的是Activity Q，因为它是P的Task中最后工作的内容。然而，如果P设定这个特性为“true”，当用户按下HOME并使这个Task再次进入前台时，其上的所有的 Activity(在这里是Q)都将被清除。因此，当返回到这个Task时，用户只能看到P。
    如果这个特性和`allowTaskReparenting`都设定为“true”，那些能重新宿主的Activity会移动到共享`affinity`的Task中；剩下的Activity都将被抛弃，如上所述。

- android:configChanges

    当配置list发生修改时， 是否调用 `onConfigurationChanged()` 方法 比如 "locale|navigation|orientation".
    举例：在按下power键时，直接在应用程序里onPause处理就好。当app是竖屏时，这样处理没问题。但是当app是横屏时，按下power 键，app会强制回到竖屏，并且会重新调用activity的`onCreate`。很多时候这不是预期结果，所以就会用到 `android:configChanges`。

    在配置文件里设置`android:configChanges＝”keyboardHidden|orientation”`，这样在屏幕方向改变的时候就 不会重新调用activity的`onCreate()`方法，而是调用`onConfigurationChanged()`,然后在activity里面重在 下。

- android:enabled
    
    activity 是否可以被实例化
    用法：`<activity android:enabled="true"></activity>`

- android:excludeFromRecents

    是否可被显示在最近打开的activity列表里
    用法：`<activity android:excludeFromRecents="true"></activity>`

- android:exported

    是否允许activity被其它程序调用

- android:finishOnTaskLaunch

    用来标记当用户再次启动它的Task（在主画面选择这个Task）时已经存在的Activity实例是否要关闭（结束）——“true”，表示应该关闭，“false”表示不关闭。默认值是“false”。
    如果这个特性和`allowTaskReparenting`都设定为“true”，这个特性胜出。Activity的`affinity`忽略。这个 Activity不会重新宿主，但是会销毁。

- android.icon
  
    用法：`<application android:icon="@drawable/xx_icon"></application>`,加在应用程序图标

- android:label
  
    定义了每个title栏的名称，但是优先级低于activity中的`setTitle()`