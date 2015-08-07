---
layout:	post
title: 	iOS多线程 -- NSOperation
category: blog
description: iOS的几种设计模式中，代理、分类都很常见，今天要说的单例模式也有广泛的应用。
---

## NSOperation的作用

配合使用NSOperation和NSOperationQueue也能实现多线程编程

### NSOperation和NSOperationQueue实现多线程的具体步骤

- 先将需要执行的操作封装到一个NSOperation对象中；
- 然后将NSOperation对象添加到NSOperationQueue中；
- 系统会自动将NSOperationQueue中的NSOperation取出来；
- 将取出的NSOperation封装的操作放到一条新线程中执行。


## NSOperation的具体使用

### NSOperation的子类

NSOperation是个抽象类，并不具备封装操作的能力，必须使用它的子类。

使用NSOperation子类的方式有3种：

- NSInvocationOperation；
- NSBlockOperation；
- 自定义子类继承NSOperation，实现内部相应的方法。

### NSInvocationOperation

### NSBlockOperation

### NSOperationQueue

#### NSOperationQueue的作用

- NSOperation可以调用start方法来执行任务，但默认是同步执行的；
- 如果将NSOperation添加到NSOperationQueue（操作队列）中，系统会自动异步执行NSOperation中的操作。

```
// 添加操作到NSOperationQueue中的方法
- (void)addOperation:(NSOperation *)op;
- (void)addOperationWithBlock:(void (^)(void))block;
```

## NSOperation的其他用法

### 操作依赖

- NSOperation之间可以设置依赖来保证执行顺序

```
// 比如一定要让操作A执行完后，才能执行操作B，可以这么写
[operationB addDependency:operationA]; // 操作B依赖于操作A
```

- 可以在不同queue的NSOperation之间创建依赖关系

### 操作监听

可以监听一个操作的执行完毕

```
// 操作监听的相关方法
- (void (^)(void))completionBlock;
- (void)setCompletionBlock:(void (^)(void))block;
```

### 自定义NSOperation

自定义NSOperation将利用多图片下载的实例来说明，将单独开博。