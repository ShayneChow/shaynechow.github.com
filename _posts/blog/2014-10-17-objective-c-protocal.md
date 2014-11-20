---
layout: post
title: Objective-C基础：协议（protocol）
description: iOS开发入门系列 之 Objective-C基础知识。
category: blog
---

Objective-C语言中的协议
---------------------

### Objective-C语言中协议的用途
在Objective-C中，协议`protocol`有什么样的用途呢？`protocol`类似Java语言中的接口`interface`。
协议实际上是一组方法列表，并不依赖于特定的类。使用协议可以是不同的类共享相同的消息。

### 创建协议文件
在所属工程项目下创建protocol头文件

	//Animal.h
	
    #import <Foundation/Foundation.h>

	@protocol Animal <NSObject>
	-(NSString*)getSex;	
	-(NSString*)getKind;
	@end
	
### 创建类，调用协议
创建好协议之后，再创建一个调用这个协议的类

	//Dog.h
	
	#import <Foundation/Foundation.h>
	#import "Animal.h"

	@interface Dog : NSObject<Animal>
	-(NSString*)getSex;
	-(NSString*)getKind;
	@end
	
### Dog类的实现
上一步创建调用Animal协议的`Dog.h`后，接着完成Dog类的实现，创建`Dog.m`

	//Dog.m
	
	#import "Dog.h"

	@implementation Dog

	-(NSString*)getKind{
   		return @"Dog";
	}

	-(NSString*)getSex{
    	return @"M";
	}

	@end

### 最后在主程序中调用类及协议
在main程序中初始化Dog类的实例对象，并调用方法，实现协议。
注意文件的目录结构：`main.m`文件位于`Supporting Files`目录下。

	//main.m
	
	#import <UIKit/UIKit.h>
	#import "AppDelegate.h"
	#import "Dog.h"

	int main(int argc, char * argv[])
	{

	    Dog *d = [[Dog alloc] init];
	    NSLog(@"%@",[d getSex]);
	    NSLog(@"This is a %@",[d getKind]);
	    
	    
	    @autoreleasepool {
	        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
	    }
	}

### 运行程序后，在console中会得到相应结果
在Xcode的console中获得的结果是

	2014-10-17 15:12:47.325 LearnProtocol[2151:458763] M
	2014-10-17 15:12:47.325 LearnProtocol[2151:458763] This is a Dog

### 最终目录结构示意
本文中出现文件的相对路径示意，未出现文件省略（可参考Xcode自动生成目录结构）。

	   	|-- LearnProtocal	//工程名
	   	|-- Supporting Files
		|   `-- main.m
	   	|-- Animal.h
	   	|-- Dog.h
	   	`-- Dog.m
    	
利用协议实现委托
--------------

###将A的事情委托给B来做

<ul>
	<li>1. 在A中声明协议，即要委托出去的事情（方法）</li>
	<li>2. A中声明一个委托对象，即要委托给谁去办，用`id<协议名字>`委托对象名</li>
	<li>3. 比如想让B做A的事情，那么B的 interface 中要宣布自己遵循A中制定的那个协议</li>
	<li>4. A调用委托执行那件事情去.即`[delegate fun]`</li>
</ul>
