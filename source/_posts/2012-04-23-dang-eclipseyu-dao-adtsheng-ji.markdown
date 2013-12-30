---
layout: post
title: "当Eclipse遇到ADT升级"
date: 2012-04-23 15:30:48 +0800
comments: true
categories: Android
---

* 问题一：
Eclipse3.7.2 + ADT18 一直出现找不到jar中class的问题。今天终于找到结果。由于新版本中把lib名称改称libs的缘故。

  解决方案：

  This issue occurs because the "lib" folder is renamed to "libs" now by Android People. And as soon as we place our external jars to this "libs" folder then it will automatically be added to the build path of the application.

  Remove the libraries from the standard Java build path :

  Right click on the project name > Properties > Java Build Path > tab Libraries > remove everything except the “Android X.X” (2.3.3 in my case) and the “Android Dependencies”

  Rename the libraries folder from “lib” to “libs”

  By doing that, all the libraries in the folder “libs” are found by the Android plugin and are added to the “Android Dependencies” item of the project

  Clean the project (not exactly needed)

  You are good to go !

  参考：
  
  1. [http://stackoverflow.com/questions/9923161/getting-classnotfound-error-after-updating-from-adt-16-to-adt-17](http://stackoverflow.com/questions/9923161/getting-classnotfound-error-after-updating-from-adt-16-to-adt-17)

  2. [http://android.foxykeep.com/dev/how-to-fix-the-classdefnotfounderror-with-adt-17](http://android.foxykeep.com/dev/how-to-fix-the-classdefnotfounderror-with-adt-17)

  3. [http://www.eoeandroid.com/thread-165493-1-1.html](http://www.eoeandroid.com/thread-165493-1-1.html)



* 问题二：
Eclipse每次run都重新开启一个新的emulator运行

  解决方案：
  
  在点击 `Run as` ⇒`Android Application`以后，选择弹出窗口左下方的 `use same device for furture launches`