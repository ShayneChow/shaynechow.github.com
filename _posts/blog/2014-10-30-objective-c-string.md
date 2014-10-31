---
layout: post
title: Objective-C基础：字符串
description: iOS开发入门系列 之 Objective-C基础知识。Objective-C 中字符串类型 NSString、NSMutableString 的使用方法总结。
category: blog
---

    
## 一、NSString 内容不变，固定字符串
###1、创建常量字符串。

	NSString *astring = @"This is a String!";

###2、创建空字符串，给予赋值。

	NSString *astring = [[NSString alloc] init];
	astring = @"This is a String!";
	[astring release];
	NSLog(@"astring:%@",astring);

###3、在以上方法中，提升速度:initWithString方法

	NSString *astring = [[NSString alloc] initWithString:@"This is a String!"];
	NSLog(@"astring:%@",astring);
	[astring release];

###4、用标准c创建字符串:initWithCString方法

	char *Cstring = "This is a String!";
	NSString *astring = [[NSString alloc] initWithCString:Cstring];
	NSLog(@"astring:%@",astring);
	[astring release];

###5、创建格式化字符串:占位符（由一个%加一个字符组成）

	int i = 1;
	int j = 2;
	NSString *astring = [[NSString alloc] initWithString:[NSString stringWithFormat:@"%d.This is %i string!",i,j]];
	NSLog(@"astring:%@",astring);
	[astring release];

###6、创建临时字符串

	NSString *astring;
	astring = [NSString stringWithCString:"This is a temporary string"];
	NSLog(@"astring:%@",astring);

###7、获取长度

	NSUInteger uLen1 = [astring length];
	NSLog(@"uLen = %lu", uLen1);

###8、获取指定索引处的字符

	NSUInteger index = 0;
	unichar c = [sFormat characterAtIndex:index];
	NSLog(@"The character at index %u of \"%@\" is \'%c\'", index, sFormat, c);
	
###9、从文件读取字符串:initWithContentsOfFile方法

	NSString *path = @"astring.text";
	NSString *astring = [[NSString alloc] initWithContentsOfFile:path];
	NSLog(@"astring:%@",astring);
	[astring release];
	
###10、写字符串到文件:writeToFile方法

	NSString *astring = [[NSString alloc] initWithString:@"This is a String!"];
	NSLog(@"astring:%@",astring);
	NSString *path = @"astring.text";
	[astring writeToFile: path atomically: YES];
	[astring release];

###11、比较两个字符串

	//用C比较:strcmp函数
	char string1[] = "string!";
	char string2[] = "string!";
	if(strcmp(string1, string2) = = 0)
	{
	    NSLog(@"1");
	}
	
	//isEqualToString方法
	NSString *astring01 = @"This is a String!";
	NSString *astring02 = @"This is a String!";
	BOOL result = [astring01 isEqualToString:astring02];
	NSLog(@"result:%d",result);
	
	//compare方法(comparer返回的三种值，升序返回-1，相等返回0，降序返回1)
	NSString *astring01 = @"This is a String!";
	NSString *astring02 = @"This is a String!";
	BOOL result = [astring01 compare:astring02] = = NSOrderedSame;
	NSLog(@"result:%d",result);
	
	//NSOrderedSame判断两者内容是否相同
	NSString *astring01 = @"This is a String!";
	NSString *astring02 = @"this is a String!";
	BOOL result = [astring01 compare:astring02] = = NSOrderedAscending;
	NSLog(@"result:%d",result);
	
	//NSOrderedAscending判断两对象值的大小(按字母顺序进行比较，astring02大于astring01为真)
	NSString *astring01 = @"this is a String!";
	NSString *astring02 = @"This is a String!";
	BOOL result = [astring01 compare:astring02] = = NSOrderedDescending;
	NSLog(@"result:%d",result);
	
	//NSOrderedDescending判断两对象值的大小(按字母顺序进行比较，astring02小于astring01为真)
	//不考虑大小写比较字符串1
	NSString *astring01 = @"this is a String!";
	NSString *astring02 = @"This is a String!";
	BOOL result = [astring01 caseInsensitiveCompare:astring02] = = NSOrderedSame;
	NSLog(@"result:%d",result);
	
	//NSOrderedDescending判断两对象值的大小(按字母顺序进行比较，astring02小于astring01为真)
	//不考虑大小写比较字符串2
	NSString *astring01 = @"this is a String!";
	NSString *astring02 = @"This is a String!";
	BOOL result = [astring01 compare:astring02
	                         options:NSCaseInsensitiveSearch | NSNumericSearch] = = NSOrderedSame;
	NSLog(@"result:%d",result);
	
	//NSCaseInsensitiveSearch:不区分大小写比较 NSLiteralSearch:进行完全比较，区分大小写 	NSNumericSearch:比较字符串的字符个数，而不是字符值。

###12、改变字符串的大小写

	NSString *string1 = @"A String";
	NSString *string2 = @"String";
	NSLog(@"string1:%@",[string1 uppercaseString]);//大写
	NSLog(@"string2:%@",[string2 lowercaseString]);//小写
	NSLog(@"string2:%@",[string2 capitalizedString]);//首字母大小
	
###13、在串中搜索子串

	NSString *string1 = @"This is a string";
	NSString *string2 = @"string";
	NSRange range = [string1 rangeOfString:string2];
	int location = range.location;
	int leight = range.length;
	NSString *astring = [[NSString alloc] initWithString:[NSString stringWithFormat:@"Location:%i,Leight:%i",location,	leight]];
	NSLog(@"astring:%@",astring);
	[astring release];

###14、抽取子串

	//-substringToIndex: 从字符串的开头一直截取到指定的位置，但不包括该位置的字符
	NSString *string1 = @"This is a string";
	NSString *string2 = [string1 substringToIndex:3];
	NSLog(@"string2:%@",string2);
	
	//-substringFromIndex: 以指定位置开始（包括指定位置的字符），并包括之后的全部字符
	NSString *string1 = @"This is a string";
	NSString *string2 = [string1 substringFromIndex:3];
	NSLog(@"string2:%@",string2);
	//-substringWithRange: //按照所给出的位置，长度，任意地从字符串中截取子串
	NSString *string1 = @"This is a string";
	NSString *string2 = [string1 substringWithRange:NSMakeRange(0, 4)];
	NSLog(@"string2:%@",string2);

###15、扩展路径

	NSString *Path = @"~/NSData.txt";
	NSString *absolutePath = [Path stringByExpandingTildeInPath];
	NSLog(@"absolutePath:%@",absolutePath);
	NSLog(@"Path:%@",[absolutePath stringByAbbreviatingWithTildeInPath]);

###16、文件扩展名

	NSString *Path = @"~/NSData.txt";
	NSLog(@"Extension:%@",[Path pathExtension]);

##二、NSMutableString 处理内容可变的字符串

###1、stringWithCapacity 给字符串分配容量:

	NSMutableString *String;
	String = [NSMutableString stringWithCapacity:40];

###2、在已有字符串后面添加字符

	//appendString: and appendFormat:
	NSMutableString *String1 = [[NSMutableString alloc] initWithString:@"This is a NSMutableString"];
	//[String1 appendString:@", I will be adding some character"];
	[String1 appendFormat:[NSString stringWithFormat:@", I will be adding some character"]];
	NSLog(@"String1:%@",String1);

###3、在已有字符串中按照所给出范围和长度删除字符

	//deleteCharactersInRange:
	NSMutableString *String1 = [[NSMutableString alloc] initWithString:@"This is a NSMutableString"];
	[String1 deleteCharactersInRange:NSMakeRange(0, 5)];
	NSLog(@"String1:%@",String1);

###4、在已有字符串后面在所指定的位置中插入给出的字符串

	//-insertString: atIndex:
	NSMutableString *String1 = [[NSMutableString alloc] initWithString:@"This is a NSMutableString"];
	[String1 insertString:@"Hi! " atIndex:0];
	NSLog(@"String1:%@",String1);
	
###5、将已有的空符串换成其它的字符串

	//-setString:
	NSMutableString *String1 = [[NSMutableString alloc] initWithString:@"This is a NSMutableString"];
	[String1 setString:@"Hello Word!"];
	NSLog(@"String1:%@",String1);
	
###6、按照所给出的范围，和字符串替换的原有的字符

	//-setString:
	NSMutableString *String1 = [[NSMutableString alloc] initWithString:@"This is a NSMutableString"];
	[String1 replaceCharactersInRange:NSMakeRange(0, 4) withString:@"That"];
	NSLog(@"String1:%@",String1);
	
###7、判断字符串内是否还包含别的字符串(前缀，后缀)
####7.1、检查字符串是否以另一个字符串开头
`- (BOOL) hasPrefix: (NSString *) aString;`

	NSString *String1 = @"NSStringInformation.txt";
	[String1 hasPrefix:@"NSString"] = = 1 ?NSLog(@"YES") : NSLog(@"NO");
	[String1 hasSuffix:@".txt"] = = 1 ?NSLog(@"YES") : NSLog(@"NO");

####7.2、查找字符串某处是否包含其它字符串 
`- (NSRange) rangeOfString: (NSString *) aString;` 这一点前面在串中搜索子串用到过;

<br/>

##参考文档

### [NSString 官方文档](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)
### [NSMutableString 官方文档](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/Reference/Reference.html#//apple_ref/occ/cl/NSMutableString)