---
layout: post
title:Objective-C基础：协议（protocal）
description:iOS开发入门系列之Objective-C协议
category: blog
---

## Objective-C语言中协议的用途
在Objective-C中，协议`protocal`有什么样的用途呢？`protocal`类似Java语言中的接口`interface`。

## 创建协议文件
在所属工程项目下创建protocal头文件

	//Animal.h
	
    #import <Foundation/Foundation.h>

	@protocol Animal <NSObject>
	-(NSString*)getSex;	
	-(NSString*)getKind;
	@end
	
## 创建类，调用协议
创建好协议之后，再创建一个调用这个协议的类

	//Dog.h
	
	#import <Foundation/Foundation.h>
	#import "Animal.h"

	@interface Dog : NSObject<Animal>
	-(NSString*)getSex;
	-(NSString*)getKind;
	@end
	
## Dog类的实现
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

## 最后在主程序中调用类及协议
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

## 运行程序后，在console中会得到相应结果
在Xcode的console中获得的结果是

	2014-10-17 15:12:47.325 LearnProtocal[2151:458763] M
	2014-10-17 15:12:47.325 LearnProtocal[2151:458763] This is a Dog
