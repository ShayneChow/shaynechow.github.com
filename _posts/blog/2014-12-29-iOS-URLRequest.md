---
layout: post
title: iOS中的网络请求
category: blog
description: iOS中常见的网络请求有同步GET， 同步POST， 异步GET， 异步POST。
---

对于移动客户端来说，网络的重要性不言而喻。常见的网络请求有同步GET， 同步POST， 异步GET， 异步POST。今天来看一下四种网络请求的实现方式。

### 同步GET

```
// 1.将网址初始化成一个OC字符串对象
NSString *urlStr = [NSString stringWithFormat:@"%@?query=%@®ion=%@&output=json&ak=6E823f587c95f0148c19993539b99295", kBusinessInfoURL, @"银行", @"济南"];
// 如果网址中存在中文,进行URLEncode
NSString *newUrlStr = [urlStr stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
// 2.构建网络URL对象, NSURL
NSURL *url = [NSURL URLWithString:newUrlStr];
// 3.创建网络请求
NSURLRequest *request = [NSURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:10];
// 创建同步链接
NSURLResponse *response = nil;
NSError *error = nil;
NSData *data = [NSURLConnection sendSynchronousRequest:request returningResponse:&response error:&error];
```

当创建好同步链接以后， 就可以采用相应的方法进行解析。下面创建异步连接也是一样的。

### 同步POST

```
// 1.根据网址初始化OC字符串对象
NSString *urlStr = [NSString stringWithFormat:@"%@", kVideoURL];
// 2.创建NSURL对象
NSURL *url = [NSURL URLWithString:urlStr];
// 3.创建请求
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
// 4.创建参数字符串对象
NSString *parmStr = @"method=album.channel.get&appKey=myKey&format=json&channel=t&pageNo=1&pageSize=10";
// 5.将字符串转为NSData对象
NSData *pramData = [parmStr dataUsingEncoding:NSUTF8StringEncoding];
// 6.设置请求体
[request setHTTPBody:pramData];
// 7.设置请求方式
[request setHTTPMethod:@"POST"];
 
// 创建同步链接
NSData *data = [NSURLConnection sendSynchronousRequest:request returningResponse:nil error:nil];
```

### 异步GET

```
NSString *urlStr = [NSString stringWithFormat:@"http://image.zcool.com.cn/56/13/1308200901454.jpg"];
NSString *newStr = [urlStr stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
NSURL *url = [NSURL URLWithString:newStr];
NSURLRequest *requst = [NSURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:10];
//异步链接(形式1,较少用)
[NSURLConnection sendAsynchronousRequest:requst queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
    self.imageView.image = [UIImage imageWithData:data];
    // 解析
    NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:data options:NSJSONReadingMutableContainers error:nil];
    NSLog(@"%@", dic);
}];
```

### 异步POST

	// POST请求
    NSString *urlString = [NSString stringWithFormat:@"%@",kVideoURL];
    //创建url对象
    NSURL *url = [NSURL URLWithString:urlString];
    //创建请求
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url cachePolicy:NSURLRequestReloadIgnoringLocalCacheData timeoutInterval:10];
    //创建参数字符串对象
    NSString *parmStr = [NSString stringWithFormat:@"method=album.channel.get&appKey=myKey&format=json&channel=t&pageNo=1&pageSize=10"];
    //将字符串转换为NSData对象
    NSData *data = [parmStr dataUsingEncoding:NSUTF8StringEncoding];
    [request setHTTPBody:data];
    [request setHTTPMethod:@"POST"];
    //创建异步连接（形式二）
    [NSURLConnection connectionWithRequest:request delegate:self];

一般的，当创建异步连接时， 很少用到第一种方式，经常使用的是代理方法。关于NSURLConnectionDataDelegate，我们经常使用的协议方法为一下几个：

```
// 服务器接收到请求时
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response
{
}
// 当收到服务器返回的数据时触发, 返回的可能是资源片段
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{
}
// 当服务器返回所有数据时触发, 数据返回完毕
- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
}
// 请求数据失败时触发
- (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error
{
    NSLog(@"%s", __FUNCTION__);
}
```

最后，分析一下这几种呢网络请求的区别。

GET请求和POST请求的区别：

1. GET请求的接口会包含参数部分，参数会作为网址的一部分，服务器地址与参数之间通过 ? 来间隔。POST请求会将服务器地址与参数分开，请求接口中只有服务器地址，而参数会作为请求的一部分，提交后台服务器。

2. GET请求参数会出现在接口中，不安全。而POST请求相对安全。

3.虽然GET请求和POST请求都可以用来请求和提交数据，但是一般的GET多用于从后台请求数据，POST多用于向后台提交数据。

同步和异步的区别：

同步链接：主线程去请求数据，当数据请求完毕之前，其他线程一律不响应，会造成程序就假死现象。

异步链接：会单独开一个线程去处理网络请求，主线程依然处于可交互状态,程序运行流畅。