---
layout: post
title: Objective-C基础：内存管理
description: iOS开发入门系列 之 Objective-C基础知识。Objective-C 中的内存管理。
category: blog
---

内存管理的概念
------------

###什么是内存管理

<ul>
	<li>内存管理是关于如何管理对象生命周期的编程原则</li>
	<li>中的内存管理只针对OC中的对象,所有的对象都继承NSObject</li>
	<li>基本数据类型不需要管理内存（`int, long, double, float, char, struct...`）</li>
	<li>当一个对象没有人再使用,该对象应该从内存中销毁掉</li>
</ul>


引用计数
-------

### 什么是引用计数

<ul>
	<li>所有OC的对象都有一个计数器, 这个计数器我们称为引用计数</li>
	<li>引用计数表示有几个“人”在使用当前对象</li>
	<li>每个对象都有一个retainCount引用计数,表示当前对象被引用的数量</li>
	<li>使引用计数加减</li>
</ul>

![引用计数加减](/images/ocneicun/nei01.png)

>1、调用retain方法，计数+1，调用release方法，计数-1；
>
>2、当引用计数为0时，说明没有“人”使用此对象，此对象就会被系统销毁。

###alloc、retain、release

<ul>
	<li>alloc用来创建对象,创建完成后,引用计数为1,只调用一次。</li>
	<li>retain使引用计数+1,可以调用多次。</li>
	<li>release使引用计数-1,可以调用多次。</li>
	<li>当引用计数为0时,对象会被系统从内存中销毁,销毁之前,会 自动调用此对象的dealloc方法。</li>
</ul>

###retain、release示例

```
Person *person = [[Person alloc] init]; 	//计数为1
[person retain];	//计数为2
[person retain];	//计数为3
[person release];	//计数为2
[person release];	//计数为1 
[person release];	//计数为0,对象从内存中销毁掉,调⽤用dealloc⽅方法
```

###为什么使用引用计数

如何持有对象所有权
----------------

自动释放池
---------

@property的使用
--------------

