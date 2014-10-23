---
layout: post
title: Mac 升级 OS X Yosemite 开发环境的调整
description: Mac设备在升级到最新10.10系统后，产生了很多坑，需要耐心来填。这篇文将介绍一下我的填坑经验。
category: blog
---

第一步：升级系统
--------------
苹果机升级系统相对来说还是很简单的，基本0门槛咯，有个靠谱的网络就欧了。在Apple store上在线下载，若干小时后下载完成按照提示进行即可。

### Yosemite安装过程的说明
这个过程也几乎不用说明。

可能需要提醒的是，千万不要相信屏幕显示的剩余时间，这次，苹果重新定义了“一分钟”，剩余最后一分钟可能会是传统意义上的一小时。

当然，这个问题并不是所有机器都会出现，主要出现在程序猿群体，当你使用Homebrew管理软件和依赖时，恭喜你，说的就是你了，耐心等待吧。（千万不要以为最后机器卡死掉而做出一些不可思议情理之中的傻事，伤害自己伤害机器都挺划不来的。）

第二步：请为新系统点赞
-------------------
在经过漫长的等待之后，优胜美地终于千呼万唤始出来，全新的UI如此的惊艳，你会迫不及待地想打开各种新特性体验一番。

第三步：掉坑了，起来填吧
---------------------
毫无疑问，等你心满意足充满欣喜地体验完各种功能后，终究是要开始苦逼的生活的。

>喂，屌丝，你老板喊你回去敲代码了哦！

正当你准备在新系统上大干一番时，你发出一声惊呼:
>"艹，IDE打不开"

>接着又一惊:"靠，我的Apache"

>"哎呀，PHP"

>"shit，Homebrew"

>……

心都碎了有木有，想死的节奏。

好吧，哭过之后，坚强地开始填坑吧！

### Apache配置
Mac系统都自带Apache，升级后会还原到默认设置，所以需要重新配置，如果你在使用它的话。

在终端运行`$ sudo vi /etc/apache2/httpd.conf`，打开Apache的配置文件

你可以在此配置虚拟主机，启用PHP等。

```
//以启用PHP配置为例说明

#LoadModule php5_module libexec/apache2/libphp5.so   //默认配置为关闭

LoadModule php5_module libexec/apache2/libphp5.so    //去掉注释，开启PHP支持
```

但此处需要注意PHP的版本及文件路径，如果你的PHP是由Homebrew管理，并且升级到最新版（截止本文发布时最新正式版是 5.5.17，Yosemite自带PHP版本为 5.5.14），你需要将Apache配置文件做相应更改

```
LoadModule php5_module /usr/local/Cellar/php55/5.5.17/libexec/apache2/libphp5.so    //注意路径与版本
```

### Homebrew
从Yosemite的开发者预览版开始，就不断有人尝鲜，Homebrew不兼容问题一直困扰着人们，如今正式版推出带来怎么的变化呢？

修复Homebrew问题，大牛池建强的MacTalk提供的方法如下:

```
$ cd `brew --prefix`
$ mv Cellar /tmp
$ brew prune
$ rm -r `git ls-files`
$ rm -r Library/Homebrew Library/Aliases Library/Formula Library/Contributions
$ rm -rf .git
$ rm -rf ~/Library/Caches/Homebrew
```

然后重新安装 Homebrew :

```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew update
```

把备份的程序复制回来，更新所有程序：

```
$ mv /tmp/Cellar .
$ brew update
$ brew upgrade
```

不过实际上正式版发布后已经不需要重装这么麻烦了，似乎只需要最后两步：

```
$ brew update
$ brew upgrade
```

### PHP及相关扩展
如果你使用brew安装的PHP，那你需要从新配置开发环境。

在上一部分Apache配置里说明了启用PHP的配置，所以在这里注意更改环境变量，使用自己安装的PHP版本

```
$ export PATH="$(brew --prefix php55)/bin:$PATH"
```

### Java环境
Java再次被干掉，这也是很多IDE无法启动的原因。

Java 没了，所有 Java 相关的开发工具和中间件都不能用了，不过不用担心，这个问题最容易解决。

在终端输入`$ java -version`

会弹出一个"您需要安装JDK才能使用java命令的工具"的提示框，点击“更多信息”，进入苹果官方支持页面，按照提示下载JDK安装即可。

```
//安装完成后再次输入上面的命令会得到相应Java的版本信息

java version "1.6.0_65"
Java(TM) SE Runtime Environment (build 1.6.0_65-b14-466.1-11M4716)
Java HotSpot(TM) 64-Bit Server VM (build 20.65-b04-466.1, mixed mode)
```

### Ruby环境
系统升级后openssl配置失效，导致bundle异常，会抛出错误`undefined method `invoke_with_build_args' for nil:NilClass`

修复办法：
```
$ brew install openssl
$ brew link openssl

// 如果上述方式不能解决问题，可尝试下面方法

$ rvm pkg install openssl
$ rvm reinstall all --force
```

### 关于Xcode
做iOS开发的同学可能在升级后面临Xcode集成开发环境出现不可思议的问题的情况，我是怎么做的呢？

将现有的Xcode全部卸载，下载最新版（我使用的是6.1正式版）安装——反正我就这样干的。

## 结语
在煎熬了一番后终于填完了可能所有的坑，应该可以愉快地coding了，你可以来首欢乐的歌曲调整一下身心。

> 《Come and Get Your Love》-- Redbone

> <audio src="/media/comeandgetyourlove.mp3"  controls preload></audio>

<script src=“http://api.html5media.info/1.1.5/html5media.min.js”></script>