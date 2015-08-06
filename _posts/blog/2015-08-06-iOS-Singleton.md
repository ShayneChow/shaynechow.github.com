---
layout:	post
title: 	iOS单例模式
category: blog
description: iOS的几种设计模式中，代理、分类都很常见，今天要说的单例模式也有广泛的应用。
---

## 什么是单例模式

单例模式的意思就是只有一个实例。单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。这个类称为单例类。

单例模式的要点有三个：一是某个类只能有一个实例；二是它必须自行创建这个实例；三是它必须自行向整个系统提供这个实例。

## 单例模式的作用

- 可以保证在运行程序过程中，一个类只有一个实例，而且该实例易于供外界访问；
- 方便控制实例个数，节约系统资源。

## 如何使用单例

如何创建并使用单例。通常使用单例作为工具类，以下我们将创建一个工具类来实现单例模式。

### 非单例情况

创建一个继承于NSObject的Tools类，并使用不同方法创建该类的实例，来观察是否为同一个实例对象，以此来判断该类是否为单例。

```
// Tools.h

#import <Foundation/Foundation.h>

@interface Tools : NSObject

@end

```

```
// Tools.m

#import "Tools.h"

@implementation Tools

@end

```

以上创建一个Tools类，在应用程序中调用该类创建实例：

```
#import "ViewController.h"
#import "Tools.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    Tools *t1 = [[Tools alloc] init];
    
    Tools *t2 = [Tools new]; // alloc init
    
    NSLog(@"t1: %p  t2: %p", t1, t2); // 打印t1 t2的内存地址
}
@end
```

打印结果是两个实例的内存地址不同，说明这里不是单例模式。（很显然，怎么可能啥都不干就是单例呢。）

那么如何创建单例呢？

### ARC单例

- 一般情况下, 如果一个类是单例, 那么都会提供一个类方法用于快速创建单例对象；
- 而且这个类方法的名称是有一定的规则: share + 类名称 / default + 类名称 / 类名称开头。

首先提供一个类方法，让程序能快速创建单例对象

```
// Tools.h

#import <Foundation/Foundation.h>

@interface Tools : NSObject

+ (instancetype)shareTools;

@end
```

```
// Tools.m

#import "Tools.h"

@implementation Tools

+ (instancetype)shareTools {
    return [[self alloc] init];
}

@end
```

根据单例模式的要求，以任何方式创建此类的实例对象都应该只生成同一个对象，所以以上方法需要改进。

```
// Tool.m (部分代码省去)

+ (instancetype)shareTools {
    return [[self alloc] init];
}

static Tools *_instance;

+ (instancetype)allocWithZone:(struct _NSZone *)zone {
    // 由于alloc方法内部会调用allocWithZone: 所以我们只需要保证在该方法只创建一个对象即可
    /*
     * 如果考虑到多线程情况下，即有可能存在多个线程判断(_instance == nil)并进入创建方法，导致重复创建实例对象，破坏单例。
    if (_instance == nil) {
        NSLog(@"创建一个实例对象");
        _instance = [super allocWithZone:zone];
    }
     */
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        NSLog(@"创建一个实例对象");
        _instance = [super allocWithZone:zone];
    });
    return _instance;
}
```

那么到这里就大功告成了么，其实并没有。

我们在实际编写程序时，可能不止使用以上方法创建对象，我们可能还会用到拷贝，那么这时候就又不对了。那我们该如何搞定拷贝这个问题呢？

这里只需要在单例类中实现两种拷贝方法的单例化即可。

```
// 在Tools.m中添加下面实例方法

- (id)copyWithZone:(NSZone *)zone {
    // 因为copy方法必须通过实例对象调用, 所以可以直接返回_instance
    //    return [[self class] allocWithZone:zone];
    return _instance;
}

- (id)mutableCopyWithZone:(NSZone *)zone {
    //    return [[self class] allocWithZone:zone];
    return _instance;
}
```

以上即完成一个单例。

### MRC单例模式

前面所提及的都是现在默认的ARC，在早期的MRC时代，单例是有所不同的，接下来看看MRC单例模式是如何实现的。

```
// Tools.m

/*
 * 此处即ARC实现部分代码，省略未写。
 */

// 在ARC基础上添加以下方法即可实现MRC单例
#pragma mark - MRC
- (oneway void)release {
}

- (instancetype)retain {
    return _instance;
}

- (NSUInteger)retainCount {
    return MAXFLOAT;
}

- (void)dealloc {
    [super dealloc];
}
```

在程序中创建和调用单例对象遵循MRC方法。

## 奇技淫巧

创建单例是不是有一些繁琐，如果在一个项目中使用多个单例类的话，要重复写一些代码多次，为什么我们不把它们单独拎出来呢？

所以我们可以使用宏定义的方式来实现重复代码，使创建单例从此变得简单。

这里我们定义一个`Singleton.h`来实现这个宏定义。

```
#define interfaceSingleton(name)  + (instancetype)share##name

#if __has_feature(objc_arc)
// 如果是ARC
#define implementationSingle(name)  + (instancetype)share##name { \
    return [[self alloc] init]; \
} \
static id _instance; \
+ (instancetype)allocWithZone:(struct _NSZone *)zone { \
    static dispatch_once_t onceToken; \
    dispatch_once(&onceToken, ^{ \
        _instance = [super allocWithZone:zone]; \
    }); \
    return _instance; \
} \
- (id)copyWithZone:(NSZone *)zone { \
    return _instance; \
} \
- (id)mutableCopyWithZone:(NSZone *)zone { \
    return _instance; \
}
#else
// 如果不是ARC
#define implementationSingleton(name)  + (instancetype)share##name { \
return [[self alloc] init]; \
} \
static id _instance; \
+ (instancetype)allocWithZone:(struct _NSZone *)zone { \
static dispatch_once_t onceToken; \
dispatch_once(&onceToken, ^{ \
_instance = [super allocWithZone:zone]; \
}); \
return _instance; \
} \
- (id)copyWithZone:(NSZone *)zone { \
return _instance; \
} \
- (id)mutableCopyWithZone:(NSZone *)zone { \
return _instance; \
}\
- (oneway void)release { \
} \
- (instancetype)retain { \
    return _instance; \
} \
- (NSUInteger)retainCount { \
    return MAXFLOAT; \
}
#endif
```

使用实例：

```
// Tools.h

#import <Foundation/Foundation.h>
#import "Singleton.h"

@interface Tools : NSObject
	//+ (instancetype)shareTools;
	interfaceSingleton(Tools);
@end
```

```
// Tools.m

#import "Tools.h"

@implementation Tools

	implementationSingleton(Tools)

@end
```

从此以后创建单例就只需要以上简单的2步了，不过记得将`Singleton.h`导入到项目中哦。
