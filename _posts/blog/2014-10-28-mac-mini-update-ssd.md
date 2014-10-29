---
layout: post
title: Mac mini 升级 SSD 全纪录
description: Mac mini 的硬件配置相对其他Mac设备来说略显寒酸，此次将介绍一下最近升级mini硬盘的经历，主要涉及2013年3月后生成的机器拆机过程。
category: blog
---

升级前的准备
----------

### 待升级硬件

### 工具

### 系统盘

Step 1：准备好系统盘
------------------

### Mac OS X 系统盘制作
其实上周苹果发布优胜美地正式版后就折腾过系统了，这次升级不得不重来一次，弱弱的感觉为毛不换个顺序呢？

#### 下载系统文件
有什么好说的呢，直接去 AppStore 下载呗，或者你认为靠谱的其他来源。

#### 制作启动U盘
常规动作，先把U盘格式化一下。然后强势插入… 是不是很爽！更爽的还在后面……

打开你的终端，输入下面命令开始制作启动盘

```
sudo /Applications/Install\ OS\ X\ Yosemite.app/Contents/Resources/createinstallmedia --volume /Volumes/untitled --applicationpath /Applications/Install\ OS\ X\ Yosemite.app --nointeraction

# 请特别注意一下，untitled 是你的u盘盘符，如果你有命名请自觉更改相应名称。
```

如果出现如下内容，则说明你成功了，Look，是不是爽到爆了，就这么简单！

```
Erasing Disk: 0%… 10%… 20%… 30%…100%…
>Copying installer files to disk…
Copy complete.
Making disk bootable…
Copying boot files…
>Copy complete.
>Done.
```

### 原硬盘数据备份
数据备份这种事情是强求不来的，有人喜欢拿移动硬盘或者U盘拷，有人偏爱云盘，苹果的深度用户往往会选择 time machine 这样高端大气上档次的方式。所以根据个人实际情况而定吧

最初我选择了高大上的 time machine 配合苹果自家的时间胶囊来备份整个系统及数据。

愿望总是美好的，现实却是残酷的。备份约50G的内容花了整整一天，并且这不是重点，重点是最后恢复系统时需要通过网络安装一个系统引导镜像（类似windows下的PE），你懂我的意思吗，我是说这个过程你需要盯着屏幕上那个地球转3个多钟头（我怎么会告诉你期间你还会遇到转了几个钟头后眼看成功在即突然服务器连接错误的情况呢？），作为一个意志坚定的大好青年，我坚持下来了，我成功的看到了从 time machine 恢复系统的选项，毫不犹豫兴奋地选择了它，恢复开始了，我满是期待，而且已经做好了再等待若干小时的心理准备。

万万没想到，恢复失败了，文件读取出错。仅存的意志终究还是被无情地击溃了，于是乎有了前面制作启动盘的那一幕。

Step 2：拆机升级硬件
------------------

![mac-mini](/images/macminiupdate/mac01.png)

![mac-mini](/images/macminiupdate/mac02.png)

![mac-mini](/images/macminiupdate/mac03.png)

![mac-mini](/images/macminiupdate/mac04.png)

![mac-mini](/images/macminiupdate/mac05.png)

![mac-mini](/images/macminiupdate/mac06.png)

![mac-mini](/images/macminiupdate/mac07.png)

![mac-mini](/images/macminiupdate/mac08.png)

![mac-mini](/images/macminiupdate/mac09.png)

![mac-mini](/images/macminiupdate/mac-power.png)

![mac-mini](/images/macminiupdate/mac10.png)

![mac-mini](/images/macminiupdate/mac11.png)

![mac-mini](/images/macminiupdate/mac12.png)

![mac-mini](/images/macminiupdate/mac13.png)

![mac-mini](/images/macminiupdate/mac14.png)

Step 3：安装或还原系统
--------------------