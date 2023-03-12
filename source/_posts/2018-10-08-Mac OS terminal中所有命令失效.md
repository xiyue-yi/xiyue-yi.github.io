---
layout:     article
title:      Mac OS terminal中所有命令失效
date:       2018-10-08
author:     LELE
header-img: img/post-bg-miui6.jpg
catalog: true
tags:
- 一日一记
- MacOS 
Key: 2018-10-08-mac-os-terminal-command
---

今天在修改系统变量的时候遇到了键盘大小写切换失败的问题，百度结果大部分建议重启系统，当时比较着急就直接退出了vim关机，没有检查.bash_profile是否修改完备；重新开机之后发现使用很多命令的结果都是Command not FOUND,包括了sudo、vim这些常见命令。百度之后发现相关解决方式并不多，看到了一篇CSDN博客给出了相应的解决办法，在这里记录一下。

<!--more-->


## 解决步骤
#### 第一步，在终端中输入

    	export PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/X11R6/bin

#### 第二步，进入home目录

        cd ~/

#### 第三步，创建bash_profile执行命令

        touch .bash_profile

#### 第四步，打开并编辑bash_profile

        open .bash_profile

#### 第五步，这样会打开一个记事本，显示之前配置过的path，修改记事本或者不修改，并保存文件退出

#### 第六步，运行使修改后的bash_profile生效

        source .bash_profile

## 说明
在解决这个问题的过程中发现，如果修改后的.bash_profile仍然存在问题，则terminal中的各种指令会继续无效，这时需要重复以上步骤直到.bash_profile中的所有问题都被修正为止。



## 参考资料
[Mac的控制台命令无法使用command not found](https://blog.csdn.net/weichuang_1/article/details/47679465)		