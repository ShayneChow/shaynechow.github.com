---
layout:	post
title: 	iOS多线程 -- NSOperation（一）
category: blog
description: 多线程的应用在现在移动开发中十分常见和重要，本文主要介绍iOS中NSOperation多线程技术。
---

## NSOperation的作用

配合使用`NSOperation`和`NSOperationQueue`也能实现多线程编程

### NSOperation和NSOperationQueue实现多线程的具体步骤

- 先将需要执行的操作封装到一个`NSOperation`对象中；
- 然后将`NSOperation`对象添加到`NSOperationQueue`中；
- 系统会自动将`NSOperationQueue`中的`NSOperation`取出来；
- 将取出的`NSOperation`封装的操作放到一条新线程中执行。


## NSOperation的具体使用

### NSOperation的子类

`NSOperation`是个抽象类，并不具备封装操作的能力，必须使用它的子类。

使用`NSOperation`子类的方式有3种：

- `NSInvocationOperation`；
- `NSBlockOperation`；
- 自定义子类继承`NSOperation`，实现内部相应的方法。

### NSInvocationOperation


```
- (void)invocation {
    // 注意: 父类不具备封装操作的能力
    //    NSOperation *op = [[NSOperation alloc] init];
    
    // 1.封装任务
    NSInvocationOperation *op1 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run) object:nil];
    // 2.要想执行任务必须调用start
    [op1 start];
    
    NSInvocationOperation *op2 = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(run2) object:nil];
    [op2 start];
}

- (void)run {
    NSLog(@"%@", [NSThread currentThread]);
}

- (void)run2 {
    NSLog(@"%@", [NSThread currentThread]);
}
```
输出结果如下：

```
<NSThread: 0x7fa2c1e15700>{number = 1, name = main}
<NSThread: 0x7fa2c1e15700>{number = 1, name = main}
```

### NSBlockOperation


```
- (void)blockOperation {
    //1. 封装任务
    NSBlockOperation *op1 = [NSBlockOperation blockOperationWithBlock:^{
        // 主线程
        NSLog(@"1---%@", [NSThread currentThread]);
    }];
    
    // 2.追加其它任务
    // 注意: 在没有队列的情况下, 如果给BlockOperation追加其它任务, 那么其它任务会在子线程中执行
    [op1 addExecutionBlock:^{
        NSLog(@"2---%@", [NSThread currentThread]);
    }];
    [op1 addExecutionBlock:^{
        NSLog(@"3---%@", [NSThread currentThread]);
    }];
    
    // 3.启动任务
    [op1 start];
}
```
输出结果如下：

```
3---<NSThread: 0x7f8d6049dc30>{number = 2, name = (null)}
2---<NSThread: 0x7f8d6071bec0>{number = 3, name = (null)}
1---<NSThread: 0x7f8d60508df0>{number = 1, name = main}
```

### NSOperationQueue

#### NSOperationQueue的作用

- `NSOperation`可以调用`start`方法来执行任务，但默认是同步执行的；
- 如果将`NSOperation`添加到`NSOperationQueue`（操作队列）中，系统会自动异步执行`NSOperation`中的操作。

```
// 添加操作到NSOperationQueue中的方法
- (void)addOperation:(NSOperation *)op;
- (void)addOperationWithBlock:(void (^)(void))block;
```
下面使用NSOperationQueue实现多线程操作：

首先创建一个继承自NSOperation的类`ZXOperation`	

```
// ZXOperation.h

#import <Foundation/Foundation.h>

@interface ZXOperation : NSOperation

@end
```

```
// ZXOperation.m

#import "ZXOperation.h"

@implementation ZXOperation

/*
 只要将任务添加到队列中, 那么队列在执行自定义任务的时候
 就会自动调用main方法
 */
- (void)main {
    NSLog(@"%s, %@", __func__, [NSThread currentThread]);
}

@end
```

```
// ViewController.m

#import "ZXOperation.h"

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {
    // 1.创建队列
    NSOperationQueue *queue = [[NSOperationQueue alloc] init];
    // 2.创建任务
    // 自定义任务的好处: 提高代码的复用性
    ZXOperation *op1 = [[ZXOperation alloc] init];
    ZXOperation *op2 = [[ZXOperation alloc] init];
    
    // 3.添加任务到队列
    [queue addOperation:op1];
    [queue addOperation:op2];
}
```
输出结果如下：

```
-[ZXOperation main], <NSThread: 0x7f90b9f46ce0>{number = 2, name = (null)}
-[ZXOperation main], <NSThread: 0x7f90b9e0ece0>{number = 3, name = (null)}
```

## NSOperation的其他用法

### 操作依赖

- `NSOperation`之间可以设置依赖来保证执行顺序

```
// 比如一定要让操作A执行完后，才能执行操作B，可以这么写
[operationB addDependency:operationA]; // 操作B依赖于操作A
```

- 可以在不同`queue`的`NSOperation`之间创建依赖关系

### 操作监听

可以监听一个操作的执行完毕

```
// 操作监听的相关方法
- (void (^)(void))completionBlock;
- (void)setCompletionBlock:(void (^)(void))block;
```

### 自定义NSOperation

自定义`NSOperation`将利用多图片下载的实例来说明，将单独开博。