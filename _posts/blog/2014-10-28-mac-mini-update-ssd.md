---
layout: post
title: Mac mini 升级 SSD 全纪录
description: Mac mini 的硬件配置相对其他Mac设备来说略显寒酸，此次将介绍一下最近升级mini硬盘的经历，主要涉及2013年3月后生成的机器拆机过程。
category: blog
---

升级前的准备
----------

### 待升级硬件

【[金士顿(Kingston)V300 120G SATA3 固态硬盘](http://item.jd.com/779351.html)】（附上我的真实购买地址，非广告哦）

### 工具

【6# 8# 六口螺丝刀】【U型撬棒】

### 系统盘

【下载系统镜像】【16G U盘】

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

>愿望总是美好的，现实却是残酷的。备份约50G的内容花了整整一天，并且这不是重点，重点是最后恢复系统时需要通过网络安装一个系统引导镜像（类似windows下的PE），你懂我的意思吗，我是说这个过程你需要盯着屏幕上那个地球转3个多钟头（我怎么会告诉你期间你还会遇到转了几个钟头后眼看成功在即突然服务器连接错误的情况呢？），作为一个意志坚定的大好青年，我坚持下来了，我成功的看到了从 time machine 恢复系统的选项，毫不犹豫兴奋地选择了它，恢复开始了，我满是期待，而且已经做好了再等待若干小时的心理准备。
>
>万万没想到，恢复失败了，文件读取出错。仅存的意志终究还是被无情地击溃了，于是乎有了前面制作启动盘的那一幕。

Step 2：拆机升级硬件
------------------

本文实例机器是2013年3月后生产机器，与2011及2012版机器略有不同，会更复杂一些。因为2013年3月前生产的机器硬盘默认使用lower位（拆掉WiF罩后即可拆除，不用拆除主板，更简单一些），之后生产的默认使用upper位（硬盘在最下面，需要拆除所有机构）。苹果此举我猜是为了更便于机主添加第二块硬盘。

至于Mac mini的2个硬盘位的区别，此处不多做介绍，读者可自行搜索。以下开始介绍upper位的拆机过程。

###2.1：打开后盖
按图中指示，将后盖从实心点旋转至空心点即可取下后盖（无需动刀）。
![mac-mini](/images/macminiupdate/mac01.png)

取下后盖后的内部结构概览：
![mac-mini](/images/macminiupdate/mac02.png)

###2.2：拆下风扇及内存条
整个拆除过程内存条是最简单的，（常规方式）拨开弹片即可拔出。
![mac-mini](/images/macminiupdate/mac03.png)

###2.3：拆除WiFi罩及风扇倒流罩
这时注意WiFi罩下与主板连接的（按压式）接口，小心剥离！
![mac-mini](/images/macminiupdate/mac04.png)

拆除后概览：
![mac-mini](/images/macminiupdate/mac05.png)

###2.4：拆除主板
这里将使用到工具里的U型撬棒，去掉左下角螺丝一枚以及连接主板的线缆（图中表示）后，向外把主板撬出（稍微用力一点不要紧）。
![mac-mini](/images/macminiupdate/mac06.png)

连接主板主要接口：
![mac-mini](/images/macminiupdate/mac07.png)

拆除后橄榄：
![mac-mini](/images/macminiupdate/mac08.png)

###2.5：拆除电源组件
注意标识处的卡扣，将其取下后，逆时针旋转电源线接头90°，向外拔出电源组件。
![mac-mini](/images/macminiupdate/mac09.png)

拔出后如下图：
![mac-mini](/images/macminiupdate/mac-power.png)

###2.6：卸下硬盘架并更换SSD
如下图整个机身仅剩硬盘架，去除最后一颗固定螺丝，拿出硬盘架即可。
![mac-mini](/images/macminiupdate/mac10.png)

硬盘架与硬盘之间有4个固定螺丝，拆除后更换上新的SSD。
![mac-mini](/images/macminiupdate/mac11.png)

注意更换硬盘线（此处为upper型硬盘线）
![mac-mini](/images/macminiupdate/mac12.png)

新旧硬盘合照，左边为淘汰下来500G的原装HDD
![mac-mini](/images/macminiupdate/mac13.png)

###简直就是工艺品
最后附上一张机箱图，你肯定没见过这么精致的机箱，这么小的机箱中整合了整套desktop，还如此精致，这个星球上只此一家了。
![mac-mini](/images/macminiupdate/mac14.png)

<strong>哦，对了。至于怎么安装回去，只要有心的就不用问了，倒着来一遍以上步骤就完事了。</strong>

Step 3：安装或还原系统
--------------------

正如前文所介绍的，还原这条路不是很容易走，此处就不再多说明了。来谈谈Mac系统安装吧（利用U盘安装）。

###选择启动方式

>其实Mac系统安装有什么好谈的呢，比windows简单多了，只要知道开机时选择启动方式即可：
>>1、开机啥都不选，从硬盘启动（此处我们使用的是不带系统的全新SSD，显而易见会报错给你）；

>>2、按住option键，从U盘或移动硬盘启动（我们需要选择的）；

>>3、按住 cmd + R 从网络启动（理所当然你得连接上网络，恢复系统也是这个选项，下载过程就是个坑）。

###磁盘工具格式化SSD

进入启动项后，进入实用工具选项，选择磁盘工具，将SSD格式化为Mac的文件格式。（Mac文件系统跟Windows不同，不用分区。）

###傻瓜式安装

SSD准备好后，关掉工具即可。再选择磁盘开始安装系统，按要求下一步就好了，真的没啥好说的，总之就是简单。