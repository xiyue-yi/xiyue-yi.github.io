---
layout:     article
title: Python里的Unicode编码问题 Again
date:       2018-03-09
author:     LELE
header-img: img/post-bg-miui6.jpg
catalog: true
tags: Python
key: 2018-03-09-python-unicode
---

Python里的编码问题一开始碰到其实是在一年前，当时想要爬取网上的内容，但是爬下来的东西很多时候会出现乱码；但是当时时间比较紧张，没有仔细再去理解里面的原因；这几天跑代码的时候又遇到了，所以就仔细查了一些资料。
<!--more-->
    
相关的Encode和Decode问题描述维基百科可见。

## 常见错误
### 第一类错误：decode相关

    	"\x81".decode("utf-8")
    	Traceback (most recent call last):
    	  File "<stdin>", line 1, in <module>
    	  File "encodings/utf_8.py", line 16, in decode
    	UnicodeDecodeError: 'utf8' codec can't decode byte 0x81 in position 0: unexpected code byte

### 错误原因：
这种错误通常发生在以某种编码("ascii")解码一个str类型的字符串时.因为编码映射仅仅只能支持一部分str类型的字符串到unicode字符串, 一个非法的str类型的序列将会导致编码decode()失败。

在这里，str字符串\x81就不能转化为unicode对应的字符串。

### 第二类错误：encode相关

		>>> "\xd0\x91".encode("utf-8")
		Traceback (most recent call last):
		  File "<stdin>", line 1, in <module>
		UnicodeDecodeError: 'ascii' codec can't decode byte 0xd0 in position 0: ordinal not in range(128)

### 错误原因：
自相矛盾的是，UnicodeDecodeError也可能发生在编码__encoding__时。 
原因是编码函数encode()通常情况下需要一个unicode类型的字符串作为参数。但是实际传过来的是一个str类型的参数。encode()函数将这个参数向上转换"up-convert"为unicode类型，然后再将转化为他们自己的编码。这也会出现这样的向上转换"up-convertion"的时候，系统默认选择一个ascii解码器, 解码器中没有这个str类型的unicode编码。因此这是在一个编码器encoder中出现解码失败的情况。

在这个过程中发生了两件事情。首先\xd0\x91是python默认的str类型的字符串，而编码encode需要一个unicode类型的字符串，所以在编码encode之前，先转化为unicode,而执行的是"\xd0\x91".decode("ascii"), 所以会出现上面的错误。(之所以是ascii是因为这是系统默认的编码方式，且是所有编码方式交换的中介

## str和unicode
str和unicode都是basestring的子类

### str和unicode的转换方式
str  ->  decode('the_coding_of_str')  ->  unicode

unicode  ->  encode('the_coding_you_want')  ->  str

### str
声明方式

		s = '中文'
		s = u'中文'.encode('utf-8')
		
		>>> type('中文')

### unicode
声明方式

		s = u'中文'
		s = '中文'.decode('utf-8')
		s = unicode('中文','utf-8')
		
		>>> type(u'中文')

#### 判断是否为unicode/str的方法

		>>> isinstance(u'中文',unicode)
		True
		>>> isinstance('中文',unicode)
		False
		
		>>> isinstance('中文',str)
		True
		>>> isinstance(u'中文',unicode)
		False

#### IDE和控制台

		>>> print u'中文'.encode('gbk')
		>>> print u'中文'.encode('utf-8')

IDE和控制台报错，原因是print时，编码和IDE自身编码不一致，输出时将编码转换成一致的就可以了。

#### 参考资料
[PYTHON-进阶-编码处理小结](http://wklken.me/posts/2013/08/31/python-extra-coding-intro.html)

[Python2中的编码错误](https://www.zybuluo.com/zwenqiang/note/21851)
		

