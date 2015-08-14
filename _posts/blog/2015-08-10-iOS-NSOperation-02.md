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

### 重复下载问题

### 线程阻塞问题

## 使用第三方框架实现 -- SDWebImage