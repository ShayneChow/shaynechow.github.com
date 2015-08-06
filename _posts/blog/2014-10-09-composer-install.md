---
layout: post
title: Composer安装
category: blog
description: PHP yii2 框架中引入Composer工具是一个利好的消息，我们可以使用其管理所需依赖。
---

## 独立安装Composer教程
composer.json 声明PHP依赖的第三方扩展的文件

加载该文件需要安装composer, composer是PHP的一个类库依赖管理机制, 类似Ruby 的 Gem
运行composer需要 php 5.3以上版本, 并且确定PHP CURL 扩展可用

为了获取Composer，我们需要做两件事。第一个是安装Composer（前面说过了，这意味下载它到你的项目中）：


```
$ curl -sS https://getcomposer.org/installer | php
```

将composer设为全局 则只需安装一份composer 设为全局后也无需 php composer.phar 来执行
可直接 composer install 来安装包

```
$ sudo mv composer.phar /usr/local/bin/composer
```

这会检查一些PHP设置，然后下载composer.phar到你的工作目录中。
这个文件是Composer二进制文件。它是一个PHAR (PHP archive)，PHP的归档格式，也可以像其他命令一样在命令行上运行。

如果要使用composer 安装 composer.json 中包含的内容 命令如下

```
$ php composer.phar install OR $ composer install
```

更新依赖库文件使用:

```
$ php composer.phar update OR $ composer update
```

*关于composer使用的相关参考资料:*

* [php依赖库管理工具Composer入门](http://weizhifeng.net/manage-php-dependency-with-composer.html) *

* [PHP 开发者该知道的5个 Composer 小技巧](http://segmentfault.com/a/1190000000355928) * 


### Homebrew安装php5及composer for mac教程
* 使用Homebrew安装composer要求PHP也由Homebrew安装，一下安装PHP过程仅作参考，详细资料请自行搜索 *

首先更新下brew软件库

```
$ brew update   
  # 执行完 brew update 后以下两步会显示 Warning: Already tapped! 可以不执行。

$ brew tap homebrew/dupes
$ brew tap josegonzalez/homebrew-php
```

brew安装php5.5

```
$ sudo brew install php55-intl 
```

更多的安装选项可以通过 “brew options php55″ 查看。

指令执行完后，php 跟 php-fpm 就安装好了。

由于是重装php，之前系统预装的php还没卸载，因此在终端调用php时，还是以之前系统的php版本做解析，所以这里需要修改path，指定 php 的解析路径。在~/.bashrc（没有则创建）最后加入一行：

```
$ export PATH="$(brew --prefix php55)/bin:$PATH"
```

添加之后再执行一下source，使之生效:

```
$ source .bashrc
```

这里有个问题，MAC开机重启后，php又恢复到了自带的php版本

解决方法——调整一下path顺序：

```
$ ls -al /usr/local/bin/php
/usr/local/bin/php -> ../Cellar/php54/5.4.19/bin/php
```

把/usr/local/bin调整到第一位:

```
$ cat ~/.bash_profile 
export PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
```

再通过brew安装Composer

```
$ brew update  # 习惯性更新brew库 ：）
$ brew install josegonzalez/php/composer
```

在shell中输入：`$ composer` 验证

完成
