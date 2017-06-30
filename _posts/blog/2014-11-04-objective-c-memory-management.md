---
layout: post
title: Objective-C基础：内存管理
description: iOS开发入门系列 之 Objective-C基础知识。Objective-C 中的内存管理。
category: blog
---

内存管理的概念
------------

### 什么是内存管理

<ul>
	<li>内存管理是关于如何管理对象生命周期的编程原则</li>
	<li>中的内存管理只针对OC中的对象,所有的对象都继承NSObject</li>
	<li>基本数据类型不需要管理内存（int, long, double, float, char, struct...）</li>
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
</ol>

![引用计数加减](/images/ocneicun/nei01.png)

> <ul><li>1、调用retain方法，计数+1，调用release方法，计数-1；</li>
> <li>2、当引用计数为0时，说明没有“人”使用此对象，此对象就会被系统销毁。</li></ul>

### alloc、retain、release

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


如何持有对象所有权
----------------

### 对象所有权

1、当一个所有者(owner,本身是一个OC对象)做了`alloc`,`retain`,`[mutable]copy`动作,它就拥有了一个对象的所 有权(ownership)；

2、使用`release`,`autorelease`方法，释放对象所有权。

### 黄金法则

如果对一个对象使用了alloc,[mutable]copy,retain, 那么你必须使用相应的release或者autorelease释放。

### 如何持有对象

set方法持有对象所有权

```
-(void)setDog:(Dog *)dog
{
	if(_dog != dog){
		[_dog release];
		_dog = [dog retain];
	}
}
```

自定义初始化方法持有对象所有权

```
-(id)initWithDog:(Dog *)dog
{
	self = [super init];
	if(self != nil){
		_dog = [dog retain];
	}
	return self;
}
```

###dealloc方法

1、当对象的引用计数为0时，系统调用此对象的dealloc方法；

2、应该在dealloc方法中，释放他持有的对象。

```
-(void)dealloc
{
	//释放对狗的所有权
	[_dog release];

	//释放对汽车的所有权
	[_car release];

	//调⽤用[super dealloc]是为了释放⽗父类持有的对象所有权
	[super dealloc];
}
```


@property的使用
--------------

一个类有如下属性,需要对所有的属性定义set和get方法,怎么定义?

```
@interface User : NSObject {
	int userId; 			//唯⼀一标⽰示id 
	NSString *_userName; 	//⽤用户名称 
	NSString *_password; 	//密码 
	NSString *_email; 		//邮箱 
	NSString *_birthday; 	//⽣生⽇日 
	NSString *_address; 	//地址
}
```

set 和 get 方法：

```
//设置器
- (void)setUserName:(NSString *)name {
    if (_userName != name) {
        [_userName release];
        _userName = [name retain];
	} 
}

//访问器
- (NSString *)userName {
    return _userName;
}
```

<strong>@property 可以为一个属性自动生成set和get方法,不需要我们手动编写。</strong>

	@property(nonatomic, retain, readwrite)NSString *userName;

1、原子性：
<ul>
	<li>atomic: 多线程环境下,存在线程保护,默认</li>
	<li>nonatomic:多线程环境下,不存在线程保护</li>
</ul>

2、赋值：
<ul>
	<li>assign:直接赋值,默认</li>
	<li>retain: 保留对象</li>
	<li>copy: 拷贝对象</li>
</ul>

3、读写性：
<ul>
	<li>readwrite:生成getter、setter方法,默认</li>
	<li>readonly: 只生成getter方法</li>
</ul>

### assign、retain、copy 的不同实现
#### assign 的实现
```
//设置器
- (void)setUserName:(NSString *)name {
	_userName = name;
}
```

#### retain 的实现
```
//设置器
- (void)setUserName:(NSString *)name {
	if(_userName != name){
		[userName release];
		_userName = [name retain];
	}
}
```

#### retain 的实现
```
//设置器
- (void)setUserName:(NSString *)name {
	if(_userName != name){
		[userName release];
		_userName = [name copy];
	}
}
```
@property 为一个属性自动生成set和get方法，我们也可以重新实现set和get方法,优先调用自己实现的set和get方法。

自动释放池
---------

只需创建一个pool对象，然后对象将自动放置到该对象中。在程序末尾我们只需要释放pool就可以释放所有对象。

```
NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
NSString *string = [NSString stringWithString:@"Pool-managed string"];
...
[pool release];
```

### 自管理内存
使用alloc或new创建的对象，需要自己手动管理内存。

```
NSString * string = [[NSString alloc] initWithString:@"Self-managed string"];	//retain = 1
...
[string release];	//retain = 0; 需要自己调用release方法
```
