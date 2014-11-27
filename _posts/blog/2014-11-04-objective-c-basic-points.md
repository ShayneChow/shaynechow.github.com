---
layout: post
title: Objective-C基础：小知识点汇总
description: iOS开发入门系列 之 Objective-C基础知识。Objective-C 中离散的基础知识总结，随时更新…
category: blog
---

小知识点，不断总结更新中…
----------------------
本文知识点均来自网络，由于来源零散，故未标明出处，还请见谅，版权为原作者所有。

### import 和 include 的区别

在 Objective-C 中，`#import` 被当成 `#include` 指令的改良版本来使用。除此之外，`#import`确定一个文件只能被导入一次，这使你在递归包含中不会出现问题。

使用哪一个还是由你来决定。一般来说，在导入 Objective-C 头文件的时候使用 `#import`，包含 C 头文件时使用 `#include`。

### 适配iPhone 6,iPhone 6 Plus必须做的一件事

以前的旧项目必须用Xcode 5或者Xcode 6重新创建工程，然后适配 iPhone 6,iPhone 6 Plus的UI尺寸，

上传就会提示

```
You must provide a screenshot for 4.7-inch Retina display, because your app binary supports 4.7-inch Retina displays.
You must provide a screenshot for 5.5-inch Retina display, because your app binary supports 5.5-inch Retina displays.
```

然后iTunes Connect后台加入这两者的截图，这样才能最终适配iPhone 6, iPhone6 Plus, App store中才会出现：此App 以针对iPhone 6,iPhone 6 Plus优化！，则截图就会显示iPhone 6的截图！

不然，如果旧项目只是修改UI直接上传，不会出现此App 以针对iPhone 6,iPhone 6 Plus优化！也不会显示iPhone 6的截图！ 

### “收藏”功能的实现原理

有的软件有本地收藏，就是不登录就可以收藏的，用userdefaults，或者sqlite，需要登录才能收藏的肯定是上传到服务器由服务器保存 。

###iOS界面设计

`[[UIScreen mainScreen] bounds]` 就是屏幕尺寸

###10个迷惑新手的Cocoa&Objective-c开发问题
[传送门](http://lianxu.me/2012/11/10-cocoa-objc-newbie-problems/)