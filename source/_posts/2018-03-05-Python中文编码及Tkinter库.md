---
layout:     article
title: Python中文编码及Tkinter库
date:       2018-03-05
author:     LELE
header-img: img/post-bg-miui6.jpg
catalog: true
tags:
- Python
- Tkinter 
Key: Python-encoding-Tkinter
---

# Python编码问题

    SyntaxError: Non-ASCII character '\xe5' in file ABC.py on line 24, but no encoding declared; see http://python.org/dev/peps/pep-         0263/   for details
<!--more-->



### 出错原因：

Python默认是以ASCII作为编码方式的，如果在自己的Python源码中包含了中文（或者其他非英语系的语言），则会出现编码错误。

### 解决办法：
在文件开头加入下面代码

    # -*- coding: UTF-8 -*- 

默认的python文件是采用ascii编码的，在头部加入# -*- coding: utf-8 -*-   则指定文件的编码格式是utf-8，那么就是说文件内你可以用中文或其他的文字了。



# Tkinter库引用问题

<!--more-->
Tkinter是python内置的GUI图形库，支持多个操作系统，使用Tcl语言开发；

我们编写的Python代码会调用内置的Tkinter，Tkinter封装了访问Tk的接口；

Tk会调用操作系统提供的本地GUI接口，完成最终的GUI
    
Tkinter 是 Python 的标准 GUI 库。Python 使用 Tkinter 可以快速的创建 GUI 应用程序。
由于 Tkinter 是内置到 python 的安装包中、只要安装好 Python 之后就能 import Tkinter 库、而且 IDLE 也是用 Tkinter 编写而成、对于简单的图形界面 Tkinter 还是能应付自如。

#### 注意：
Python3.x 版本使用的库名为 tkinter,即首写字母 T 为小写。

```python
import tkinter
```

即在Python2.x版本中，使用Tkinter；在Python3.x版本中，使用tkinter.
