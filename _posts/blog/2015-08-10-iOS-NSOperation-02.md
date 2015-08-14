---
layout:	post
title: 	iOS多线程 -- NSOperation（二）
category: blog
description: 继上文初步介绍了NSOperation多线程技术之后，我们再来利用多图片下载实例说明NSOperation如何实现多线程。
---

## 多图片下载思路

本文将利用一个列表加载网络图片来展示一列App的下载量的示例来演示多线程下载多图片的应用。

本文以下示例中涉及UITableView的性能优化，多线程异步下载，线程阻塞等问题，最后会介绍如何使用第三方框架快速优雅地解决以上多线程下载网络图片的问题。

## 基础UI搭建

### storyboard搭建基本界面

使用storyboard拖拽出一个`UITableVIewController`，选择系统自带subtitle的cell样式即可。接着倒入提前准备好的`apps.plist`资源文件（用于模型内容展示），之后开始创建`UITableVIewController`控制器并关联IB，并开始搭建App模型。

```
// ZXApp.h

#import <Foundation/Foundation.h>

@interface ZXApp : NSObject

/** 图标 */
@property (nonatomic, copy) NSString *icon;
/** 名称 */
@property (nonatomic, copy) NSString *name;
/** 下载数 */
@property (nonatomic, copy) NSString *download;

- (instancetype)initWithDict:(NSDictionary *)dict;
+ (instancetype)appWithDict:(NSDictionary *)dict;

@end
```

```
// ZXApp.m

#import "ZXApp.h"

@implementation ZXApp

- (instancetype)initWithDict:(NSDictionary *)dict {
    if (self = [super init]) {
        [self setValuesForKeysWithDictionary:dict];
    }
    return self;
}

+ (instancetype)appWithDict:(NSDictionary *)dict {
    return [[self alloc] initWithDict:dict];
}

@end
```
完成UITableViewController的基本设置，注意此时需要在storyboard中设置好cell的identifierID与数据源方法中设置的ID保持一致。	

```
// ZXTableViewController.m

#import "ZXTableViewController.h"
#import "ZXApp.h"

@interface ZXTableViewController ()
/** 需要展示的数据 */
@property (nonatomic, strong) NSArray *apps;
@end

@implementation ZXTableViewController

#pragma mark - lazy
- (NSArray *)apps {
    if (!_apps) {
        NSString *path = [[NSBundle mainBundle] pathForResource:@"apps.plist" ofType:nil];
        NSArray *tempArr = [NSArray arrayWithContentsOfFile:path];
        NSMutableArray *models = [NSMutableArray arrayWithCapacity:tempArr.count];
        for (NSDictionary *dict in tempArr) {
            ZXApp *app = [ZXApp appWithDict:dict];
            [models addObject:app];
        }
        _apps = [models copy];
    }
    return _apps;
}

#pragma mark - Table view data source
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.apps.count;
}


- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%@", [NSThread currentThread]);
    // 1.创建cell
    static NSString *identifier = @"app";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:identifier];
    // 2.设置数据
    ZXApp *app = self.apps[indexPath.row];
    cell.textLabel.text = app.name;
    cell.detailTextLabel.text = app.download;
    
    // 下载图片
    /*
     存在的问题:
     1.图片在主线程中下载, 阻塞主线程
     2.重复下载, 浪费资源
     */
    NSURL *url = [NSURL URLWithString:app.icon];
    NSData *data = [NSData dataWithContentsOfURL:url];
    UIImage *image = [UIImage imageWithData:data];
    cell.imageView.image = image;
    
    // 3.返回cell
    return cell;
}

@end
```

## 使用NSOperation实现

要完成网络图片下载，使用NSOperation实现时会遇到以下两个问题：

> - 重复下载
> - 线程阻塞

下面就依次解决这2个问题。

### 重复下载问题

用户在来回拖动TableView的时候，基于目前的程序每次出现cell都会去下载一次对应的图片，这就造成了程序的性能损耗。

那么怎么解决这个问题呢？这里就需要用到缓存了。

针对此程序有3种获取图片的方式：

>1.直接下载
>2.内存缓存
>3.沙盒缓存

直接下载和内存缓存我们都很好理解，那么沙盒是个什么鬼呢？一般很少接触到，这里关于沙盒的详细问题就不做过多介绍，直接祭出神器，一个基于NSString的扩展分类专门用来处理沙盒存储路径的，以后需用到沙盒存储的地方可以直接拿来使用。

```
// NSString+ZX.h

#import <Foundation/Foundation.h>

@interface NSString (ZX)

/**
 *  生成缓存目录全路径
 */
- (instancetype)cacheDir;
/**
 *  生成文档目录全路径
 */
- (instancetype)docDir;
/**
 *  生成临时目录全路径
 */
- (instancetype)tmpDir;
@end
```

```
// NSString+ZX.m

#import "NSString+ZX.h"

@implementation NSString (ZX)

- (instancetype)cacheDir {
    NSString *dir = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
    return [dir stringByAppendingPathComponent:[self lastPathComponent]];
}

- (instancetype)docDir {
    NSString *dir = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) lastObject];
    return [dir stringByAppendingPathComponent:[self lastPathComponent]];
}

- (instancetype)tmpDir {
    NSString *dir = NSTemporaryDirectory();
    return [dir stringByAppendingPathComponent:[self lastPathComponent]];
}
@end
```

在TableView视图控制器中导入头文件，创建缓存属性，然后在cell的加载中加入缓存方法

```
// ZXTableViewController.m 除以下方法外其他省略未写（未改动）

#import "NSString+ZX.h"
// 懒加载图片缓存属性
- (NSMutableDictionary *)imageCaches {
    if (!_imageCaches) {
        _imageCaches = [NSMutableDictionary dictionary];
    }
    return _imageCaches;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    NSLog(@"%@", [NSThread currentThread]);
    // 1.创建cell
    static NSString *identifier = @"app";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:identifier];
    // 2.设置数据
    ZXApp *app = self.apps[indexPath.row];
    cell.textLabel.text = app.name;
    cell.detailTextLabel.text = app.download;
    
    // 下载图片
    /*
     存在的问题:
     1.图片在主线程中下载, 阻塞主线程
     2.重复下载, 浪费资源
     */
    
    // 1.从字典冲获取需要展示图片
    UIImage *image =  self.imageCaches[app.icon];
    if (image == nil) {
        //        NSLog(@"下载图片");
        
        // 2.判断沙盒缓存中有没有
        NSData *data = [NSData dataWithContentsOfFile:[app.icon cacheDir]];
        if (data == nil) {
            NSLog(@"下载图片");
            // 需要下载
            NSURL *url = [NSURL URLWithString:app.icon];
            data = [NSData dataWithContentsOfURL:url];
            UIImage *image = [UIImage imageWithData:data];
            
            // 缓存下载好的数据到内存中
            self.imageCaches[app.icon] = image;
            
            // 缓存到沙盒
            [data writeToFile:[app.icon cacheDir] atomically:YES];
            
            // 更新UI
            cell.imageView.image = image;
        }else
        {
            NSLog(@"从沙盒加载图片");
            // 根据沙盒缓存创建图片
            UIImage *image = [UIImage imageWithData:data];
            
            // 进行内存缓存
            self.imageCaches[app.icon] = image;
            
            // 更新UI
            cell.imageView.image = image;
            
        }
        
    }else
    {
        NSLog(@"使用内存缓存");
        // 更新UI
        cell.imageView.image = image;
    }
    
    // 3.返回cell
    return cell;
}
```

自此即可解决重复下载问题了。

### 线程阻塞问题

## 使用第三方框架实现 -- SDWebImage