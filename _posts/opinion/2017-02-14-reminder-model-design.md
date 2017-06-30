---
layout: post
title: 提醒事件模型设计
category: opinion
description: 基于一个类似任务列表的项目，需要实现一套提醒事件功能，参考了市面上多款日程管理以及闹钟软件，觉得没有一个特别切合需求的流程，于是着手自主设计一套提示事件的模型。
---

## 一、背景

基于一个类似任务列表的项目，需要实现一套提醒事件功能，参考了市面上多款日程管理以及闹钟软件，觉得没有一个特别切合需求的流程，于是着手自主设计一套提示事件的模型。

## 二、产品目标

本产品目的在于快速清晰地完成提醒事件的设置，并兼容普通的闹钟模式与复杂的日程式事件提醒。闹钟模式重点是在于事件的重复响应（例如：每天8:30，起床），日程模式重点则在于引入了日历（例如：2017年2月5日 9:00，新年开业会议），市面上对这两种模式的实践已经很多，不乏优秀的产品，但是如果将两种结合到一起，实现一个复杂的机制呢？例如：每个月1号做当月工作计划会议，每个月25号准备汇报材料，每周一10:00小组例会。在上述场景中日期与时间的周期重复问题应该如何才能很好实践呢，这就是本产品要实现的目标。

## 三、产品概述

本产品在设计初是作为一个项目的分支模块，在解除耦合以后我将它独立设计成一个系统，可以轻松移植到其他需要的项目，甚至稍加润色既是一个完整的提醒APP。为达成本产品的设计点，我将其作为一项事件，事件由内容与规则构成。

## 四、模型设计

针对前文定义的概念，提醒模块/系统可以设置为一个事件模型，用户触发一个提醒事件需要完成2步设置：事件内容+事件规则，也就是说：事件=内容+规则。

内容即为提醒事件需要对用户输出的提示消息，规则是指提醒事件遵从的时间设定规则。这样构成的是：1.提醒什么信息，2.什么时候提醒。其中规则是本系统的核心以及难点。

规则包含两个部分：1.提醒时刻；2.提醒周期。时刻：提醒事件当天触发的时间点；周期：提醒事件触发的日期+提醒事件的重复周期。

规则 = 时刻 +  周期

周期 = 重复规则 + 日期

重复规则：重复/不重复；
日期：年、月、日、周X。
![Markdown](http://p1.bpimg.com/586931/c13a3c1066ceebe9.png)

以更易理解的方式表达则是如下结构
![Markdown](http://p1.bpimg.com/586931/e254dcf767fa1e51.png)

根据以上模型以及结构设计，我将其具象化为如下模块：
![Markdown](http://p1.bpimg.com/586931/ed5a1f5662bf03b1.png)

以上图示展示的是未设置规则时默认显示的当前时间（2017年2月14日 16:39），接下来在功能设计部分我会解析如何使用以上模型进行复杂提醒的设置。

## 五、功能设计

作为标榜功能完善的提醒体系，涵盖绝大多数提醒事件需求是必须完成的，所以以下针对几种常规提醒模式进行实践。

### 1、闹钟模式

闹钟模式，又可以称为重复模式，侧重点是重复提醒。例如：Apple时钟、火箭闹钟。
实践：设置工作日7:30起床闹钟，休息日9:00起床闹钟，每周五下午2:00小组会议。

#### a.工作日7:30起床闹钟
![Markdown](http://p1.bpimg.com/586931/c941201748aa2fe1.png)

#### b.休息日9:00起床闹钟
![Markdown](http://p1.bpimg.com/586931/0c253db5f61d13b7.png)

#### c.每周五下午2:00小组会议
![Markdown](http://p1.bpimg.com/586931/46ea690de9926cc8.png)

>> 注意：是否发觉我在此处将日期中的年月日去掉了，因为在这种模式下通常不涉及到日期的操作，所以可以去掉这个模块，对实践无影响。另外也说明本产品的灵活性。

操作方式：
> 1.点击时刻模块，选择目标时刻；
> 2.选择（重复）周期，如果不选择，则不设置重复提醒，仅提醒一次（当天此时刻，若当天此时刻已过则设置为下一天此时刻），若选择星期中的一项或多项则在每周的周几重复提醒，下面“工作日”、“休息日”、“每天”为常用快捷操作按钮，此三项为互斥操作，单选或根据星期的选择自动设置。

### 2、日程模式

日程模式，通常使用在日程管理系统或者代办事务系统或者做纪念日管理，一般是对一个特殊的日期设置单次提醒。提醒模式为：xxxx年xx月xx日，xx点xx分。

#### 实践：设置提醒2017年2月17日，23:59，准备迎接周六。
![Markdown](http://p1.bpimg.com/586931/6227725c80b3ba8f.png)

#### 实践：设置提醒每月15日，18:30，发工资。
![Markdown](http://p1.bpimg.com/586931/dc700371088af979.png)

>> 注意：在日期模块，没有用到星期可以去掉，不过鉴于日历通常保持星期便于用户观察应予保留，日历的展示形式可自行定义，此处我设计的是上下可切换月，左右滑动可移动到前后一周的日期。（14日为当前日期）

操作方式：
	1.选择时刻；
	2.选择目标日期；
	3.决定是否选择每月重复。

### 3、复合模式

复合模式，顾名思义，是上面2种模式的结合。其实在上一种模式中，将规则稍加更改则可以实现复合模式。（每月15日提醒发工资事件，其实已经属于复合模式了）

#### 实践：每月15日提醒（本次及以下均省去了时刻）
![Markdown](http://p1.bpimg.com/586931/8da3177037e8cc73.png)
#### 实践：2月15日、17日提醒
![Markdown](http://p1.bpimg.com/586931/ecfaca4af4235cbe.png)

#### 实践：每周三、四提醒
![Markdown](http://p1.bpimg.com/586931/110f5b9383d4e1b9.png)
#### 实践：休息日提醒
![Markdown](http://p1.bpimg.com/586931/48fbd73cea752575.png)

>> 注意：周期设置的优先级。星期>日期（周期为快捷选项）

>> 操作：前2种模式结合。

### 4.应用实践

在此我提供一个完整的应用场景模块设计

#### a.事件列表与设置界面分离
![Markdown](http://p1.bpimg.com/586931/0652dab87f7a1ee7.png)

#### b.事件列表与设置界面集成
![Markdown](http://p1.bpimg.com/586931/59136f5824cc9440.png)

注意：事件列表的相关设计与操作（如下图）
![Markdown](http://p1.bpimg.com/586931/4dd18a87e3a05ee8.png)

## 六、总结

以上即为本产品的模型设计，本产品旨在提供一套快捷完善的提醒事件系统。在以上实践中模块的灵活运用可以发挥出多种效果，基本能涵盖绝大部分提醒事件的需求。

本产品灵活多变并且简单好用，根据以上结构模型，配合优秀的UI设计会是一款不错的应用模块。
