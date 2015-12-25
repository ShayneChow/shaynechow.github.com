---
layout:	post
title: 	iOS网络 -- AFNetworking基本使用
category: blog
description: iOS网络最常用第三方框架介绍。
---

## AFNetworking的简介

AFNetworking是iOS / Mac OS 开发中常用的HTTP网络框架，在它之前ASI曾独霸江湖号称“HTTP终结者”，但是后者已经停止更新，使得更多开发者选择AFN作为首选网络开发框架。AFN相对于ASI效率要低，但是更为简单，这也是其流行的重要原因，在本文下面一部分，我们就来看看它到底有多简单。

## AFNetworking的基本使用

AFNetworking的get和post请求差别就是一处关键字的不同，其他一模一样，是不是超简单。

### 基于NSURLConnection的get请求

```
- (void)get {
    // 1.创建网络管理者
    // AFHTTPRequestOperationManager基于NSURLConnection
    AFHTTPRequestOperationManager *mgr = [AFHTTPRequestOperationManager manager];
    
    // 2.利用网络管理者发送get请求
    NSDictionary *parameters = @{
                                 @"username" : @"yourname",
                                 @"pwd" : @"yourpassword"
                                 };
    [mgr GET:@"http://120.25.226.186:32812/login" parameters:parameters success:^void(AFHTTPRequestOperation * operation, id responseObject) {
        NSLog(@"响应成功%@",parameters);
    } failure:^void(AFHTTPRequestOperation * operation, NSError * error) {
        NSLog(@"请求失败");
    }];

}
```

### 基于NSURLConnection的post请求

```
- (void)get {
    // 1.创建网络管理者
    // AFHTTPRequestOperationManager基于NSURLConnection
    AFHTTPRequestOperationManager *mgr = [AFHTTPRequestOperationManager manager];
    
    // 2.利用网络管理者发送get请求
    NSDictionary *parameters = @{
                                 @"username" : @"yourname",
                                 @"pwd" : @"yourpassword"
                                 };
    [mgr POST:@"http://120.25.226.186:32812/login" parameters:parameters success:^void(AFHTTPRequestOperation * operation, id responseObject) {
        NSLog(@"响应成功%@",parameters);
    } failure:^void(AFHTTPRequestOperation * operation, NSError * error) {
        NSLog(@"请求失败");
    }];

}
```

### 基于NSURLSession的get请求

```
- (void)get2 {
    // 1.创建网络管理者
    // AFHTTPSessionManager 基于NSURLSession
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    // 2.利用网络管理者发送get请求
    /*
     __nullable 可以为nil/ null
     __nonnull 代表不能为nil,null
     */
    NSDictionary *parameters = @{
                                 @"username" : @"yourname",
                                 @"pwd" : @"yourpassword"
                                 };
    [manager GET:@"http://120.25.226.186:32812/login" parameters:parameters success:^ void(NSURLSessionDataTask * task, id responseObject) {
        NSLog(@"请求成功 %@", [responseObject class]);
    } failure:^ void(NSURLSessionDataTask * operation, NSError * error) {
        NSLog(@"请求失败 %@", error);
    }];
}
```

### 基于NSURLSession的post请求

```
- (void)get2 {
    // 1.创建网络管理者
    // AFHTTPSessionManager 基于NSURLSession
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    // 2.利用网络管理者发送get请求
    /*
     __nullable 可以为nil/ null
     __nonnull 代表不能为nil,null
     */
    NSDictionary *parameters = @{
                                 @"username" : @"yourname",
                                 @"pwd" : @"yourpassword"
                                 };
    [manager POST:@"http://120.25.226.186:32812/login" parameters:parameters success:^ void(NSURLSessionDataTask * task, id responseObject) {
        NSLog(@"请求成功 %@", [responseObject class]);
    } failure:^ void(NSURLSessionDataTask * operation, NSError * error) {
        NSLog(@"请求失败 %@", error);
    }];
}
```

### 基于NSURLSession的下载操作

```
- (void)download {
    // 1.创建网络管理者
    // AFHTTPSessionManager 基于NSURLSession
    AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
    // 2.利用网络管理者下载数据
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://120.25.226.186:32812/resources/videos/minion_01.mp4"]];
    /*
     destination
     - targetPath: 系统给我们自动写入的文件路径
     - block的返回值, 要求返回一个URL, 返回的这个URL就是剪切的位置的路径
     completionHandler
     - url :destination返回的URL
     */
    /*
     @property int64_t totalUnitCount;  需要下载文件的总大小
     @property int64_t completedUnitCount; 当前已经下载的大小
     */
    NSProgress *progress = nil;
    NSURLSessionDownloadTask *task = [manager downloadTaskWithRequest:request progress:&progress destination:^ NSURL * (NSURL * targetPath, NSURLResponse * response) {
        NSLog(@"targetPath = %@", targetPath.absoluteString);
        
        NSString *path = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject];
        NSString *toPath = [path stringByAppendingPathComponent:response.suggestedFilename];
        NSLog(@"toPath = %@", toPath);
        return [NSURL fileURLWithPath:toPath];
        
    } completionHandler:^ void(NSURLResponse * response, NSURL * url, NSError * error) {
        //        NSLog(@"下载完毕 url = %@", url.absoluteString);
    }];
    
    // 给Progress添加监听 KVO
    [progress addObserver:self forKeyPath:@"completedUnitCount" options:NSKeyValueObservingOptionNew context:nil];
    
    // 3.启动任务
    [task resume];
    
    NSLog(@"%lld, %lld", progress.totalUnitCount, progress.completedUnitCount);
}

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(NSProgress *)progress change:(NSDictionary *)change context:(void *)context {
    //    NSLog(@"%lld, %lld", progress.totalUnitCount, progress.completedUnitCount);
    NSLog(@"%f", 1.0 * progress.completedUnitCount / progress.totalUnitCount);
}
```