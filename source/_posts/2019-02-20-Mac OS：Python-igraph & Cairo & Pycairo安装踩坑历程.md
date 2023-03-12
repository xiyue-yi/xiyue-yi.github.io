---
layout:     article
title: Mac OS：Python-igraph & Cairo & Pycairo安装踩坑历程
date:       2019-02-20
author:     LELE
header-img: img/post-bg-miui6.jpg
catalog: true
tags:
- igraph
- cairo
- pycairo
- 可视化
- 社区发现
- python
Key: 2019-02-20-macos-python-igraph-cairo-pycairo
---

## 在Mac OS X上使用igraph相关库作图可视化

昨天突然收到小伙伴作业里的一个需求，需要把作业里的一个图可视化，小伙伴提示说可以借助python里的igraph这个库实现，于是就开始尝试了一下。
<!--more-->

这个图应该是社区发现里比较经典的，大概形式如下图，使用的是[这个博客](https://blog.csdn.net/liuhuan323/article/details/78936781)中提供的代码。不过在安装的过程中踩了一些坑，也没有找到太多汉化的教程，于是希望把这个过程记录一下。 这个过程仅基于Mac OS系统，其它系统应该也可以在相关网站找到教程。

![Image](./img/dolphin.png)

### 踩坑过程

这一部分说**错误**的安装方式，可以直接跳到**正确安装**的步骤查看。

### 1. 安装python-igraph
python-igraph库的安装很简单，直接使用pip命令

   ```
   pip install python-igraph
   ```



### 2. 安装Cairo

要实现可视化，还需要借助Cairo库。在igraph的官方网站上（https://igraph.org/python/doc/tutorial/install.html#installing-igraph）给出了基于Mac OS的画图方法<u>Graph plotting in *igraph* on Mac OS X</u>，上面提到可以在Cairo的主页上找到安装方法，并说没有提供Mac OS的预编译安装软件，但可以借助MacPorts和Fink进行安装：

   ### 基于MacPorts

   ```
   # 安装
   sudo port install cairo
   # 升级
   sudo port upgrade cairo
   ```

   但是因为电脑上没有安装MacPorts，所以首先还要进行MacPorts的安装，直接在MacPorts主页上（https://www.macports.org/install.php）根据系统版本下载安装包，和一般软件的安装过程一样使用Mac OS X Package Installer，这样会自动安装到默认路径。在安装的过程中可能会卡顿比较长的时间，虽然只有十几M但是安装了可能有一两个小时之久。（一开始以为安装包有问题还重启了一次，其实没有必要等着就好）

   安装完之后尝试使用port指令安装cairo，但是出现了报错：

   ```
   Error: Port cairo not found
   ```

   搜索了一下发现很多是环境变量设置的问题，说要修改~/.bash_profile文件，添加相关路径，但是修改之后发现也没有用仍然报错，后来就随便试了一下更新了一下port：

   ```
   sudo port -v selfupdate
   ```

   更新完之后居然就好了，然后就正常安装，安装cairo的时候还需要先安装一系列依赖，反正又更了很长时间：

   ```
   The following dependencies will be installed: 
   
    bzip2
   
    db48
   
    expat
   
    fontconfig
   
    freetype
   
    gdbm
   
    gettext
   
    glib2
   
    icu
   
    libedit
   
    libffi
   
    libiconv
   
    libpixman
   
    libpng
   
    libxml2
   
    ncurses
   
    openssl
   
    ossp-uuid
   
    pcre
   
    perl5.26
   
    python27
   
    python2_select
   
    python_select
   
    readline
   
    sqlite3
   
    xorg-libX11
   
    xorg-libXau
   
    xorg-libXdmcp
   
    xorg-libXext
   
    xorg-libpthread-stubs
   
    xorg-libxcb
   
    xorg-xcb-proto
   
    xorg-xcb-util
   
    xorg-xorgproto
   
    xrender
   
    xz
   
    zlib
   ```

   刚发现这里还是python27。。。无奈，就这样以为终于装好了Cairo。

   

   #### 基于Fink

   ```
   sudo apt-get install cairo
   ```

   因为也没有安装fink，所以没有尝试这个方法。

   

### 3. 安装Pycairo 
到这里以为装完了Cairo，但是因为需要在python里调用，所以还需要Cairo的python绑定Pycairo。


   根据igraph主页上的建议，可以在PyCairo的主页上（https://www.cairographics.org/pycairo/）找到安装方法，也没有提供预编译好的安装包，所以想用tar包的方式安装，下载了文件pycairo-1.18.0.tar.gz，解压之后里面有一个setup.py文件，但是不知道具体的安装参数。。。make啥的也不行。。。后来不知道在哪看的可以直接通过easy_install安装，

   ```
   easy_install pycairo
   ```

   这个的过程其实还是自动下载.tar.gz包，然后安装，但是不出意外地又报错了：

   ```
   Searching for pycairo
   
   Reading https://pypi.org/simple/pycairo/
   
   Downloading https://files.pythonhosted.org/packages/a6/54/23d6cf3e8d8f1eb30e0e58f171b6f62b2ea75c024935492373639a1a08e4/pycairo-1.18.0.tar.gz#sha256=abd42a4c9c2069febb4c38fe74bfc4b4a9d3a89fea3bc2e4ba7baff7a20f783f
   
   Best match: pycairo 1.18.0
   
   Processing pycairo-1.18.0.tar.gz
   
   Writing /var/folders/lh/14hpyxpn0ps32ltr2b8cz8w80000gn/T/easy_install-w_x2q5nz/pycairo-1.18.0/setup.cfg
   
   Running pycairo-1.18.0/setup.py -q bdist_egg --dist-dir /var/folders/lh/14hpyxpn0ps32ltr2b8cz8w80000gn/T/easy_install-w_x2q5nz/pycairo-1.18.0/egg-dist-tmp-rz1_9p9j
   
   no previously-included directories found matching 'docs/_build'
   
   warning: no files found matching 'README' under directory 'tests'
   
   warning: no files found matching 'README' under directory 'examples'
   
   error: Setup script exited with 'pkg-config' not found.
   
   Command ['pkg-config', '--print-errors', '--exists', 'cairo >= 1.13.1']
   ```

   但是在这里面可以看到执行setup.py的相关参数，所以又去自己下载的包里直接执行了一次，结果还是一样的报错：

   ```
   no previously-included directories found matching 'docs/_build'
   
   warning: no files found matching 'README' under directory 'tests'
   
   warning: no files found matching 'README' under directory 'examples'
   
   'pkg-config' not found.
   
   Command ['pkg-config', '--print-errors', '--exists', 'cairo >= 1.13.1']
   ```

   大概应该是缺少了pkg-config这个依赖？

   后来又看到可以直接用pip命令安装pycairo，但是不出意外地又出错了（心态崩溃），

```
pip install pycairo

...
'pkg-config' not found
...
Failed building wheel for pycairo
...
```

   似乎还是在说缺少pkg-config，于是又用homebrew装这个包：

```
brew install pkg-config
```

   然后再尝试安装cairo，结果又报错了：

```
No package 'cairo' found
```

   说找不到cairo…然后几乎就放弃了这种方法。

   上面的过程基本上是基于igraph官方网站的建议，因为后面pycairo的安装没有说的很详细，到最后也没有成功，所以就感觉踩了一路坑。大概就是cairo主页上的安装方式不太靠谱，得使用pycairo上的安装方式。



后来直接在pycairo的主页上（https://pycairo.readthedocs.io/en/latest/getting_started.html）找到了特别简单的方法。。。如下。。。



## 正确步骤

### 1. 安装python-igraph

直接用pip命令安装

```
pip install python-igraph
```

安装后可以在终端里import测试一下

```python
>>> import igraph.test
>>> igraph.test.run_tests()
```

也可以查看一下安装的版本

```python
>>> import igraph
>>> print igraph.__version
```



### 2. 安装Cairo

要实现可视化，还需要借助Cairo库。

利用Homebrew在Mac OS上进行安装：

```
brew install cairo pkg-config
```

其它平台的安装参见https://pycairo.readthedocs.io/en/latest/getting_started.html



### 3. 安装Pycairo

除了Cairo库之外，因为需要在python里调用，所以还需要Cairo的python绑定Pycairo。

实际上现在只需要pip命令即可，这里的最新版本为pycairo-1.18.0

```
pip3 install pycairo
```



这样就可以正常利用igraph和Cairo库画图啦，igraph主页（https://igraph.org/python/doc/tutorial/tutorial.html）提供了一些教程供参考。



## 参考资料
[igraph主页安装建议](https://igraph.org/python/doc/tutorial/install.html#installing-igraph)

[MacPorts主页port安装方法](https://www.macports.org/install.php)

[cairo主页上的pycairo安装建议](https://www.cairographics.org/pycairo/)

[pycairo主页的安装建议](https://pycairo.readthedocs.io/en/latest/getting_started.html)

[作图代码来源](https://blog.csdn.net/liuhuan323/article/details/78936781)

[igraph主页作图教程](https://igraph.org/python/doc/tutorial/tutorial.html)
