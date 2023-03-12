---
layout:     article
title:      Mac OS：C++ IDE & OpenCV2安装配置
date:       2019-05-13
author:     LELE
header-img: img/post-bg-miui6.jpg
catalog: true
tags:
- opencv2
- clion
- macos
- c++
key: 2019-05-13-Mac-OS-C++IDE-OpenCV2
---

因为最近有一个作业需要跑c++程序，但是最近都用python比较多， mac上还没有配置过c++的环境，所以需要先配置一下IDE之类的。

<!--more-->

## 1. 安装IDE——CLion

win系统下支持C++的IDE还是挺丰富的，甚至有挺多轻量级的选择，但是对mac上的不是很了解，简单挑选了一些最后选定了CLion作为跑实验的IDE，实际使用体验也还不错，可能因为也是JetBrains的软件，很多使用习惯和PyCharm还挺像的，所以上手也比较容易。学生还可以用学生邮箱注册免费账号。

## 2. 安装Cmake

## 3. 安装OpenCV2

在mac上安装OpenCV其实还是很简单的，借助homebrew工具只要一行命令即可；但是由于需要的依赖库比较多，所以安装时间会比较长。

在安装的时候尤其需要注意OpenCV的**版本问题**，用homebrew搜索功能可以看到目前提供了三个版本供下载：

```
brew search opencv

==> **Formulae**

opencv                  opencv@2                   opencv@3
```

当前时间下，如果直接安装opencv默认为OpenCV3版本，也是当前的主流版本，后面两个分别代表OpenCV2和OpenCV3版本：

```
brew install opencv
```

于是一开始我就很傻地直接用命令安装了，可是比较坑的地方是，作业里这次使用的是OpenCV2版本的代码，里面有一些库无法兼容，且修改起来较为麻烦，于是只能卸载重装，可是重装之后出现了一些问题。

首先uninstall opencv之后，又install opencv@2之后，系统提示：

```
opencv@2 is keg-only, which means it was not symlinked into /usr/local,

because this is an alternate version of another formula.


If you need to have opencv@2 first in your PATH run:

  echo 'export PATH="/usr/local/opt/opencv@2/bin:$PATH"' >> ~/.bash_profile


For compilers to find opencv@2 you may need to set:

  export LDFLAGS="-L/usr/local/opt/opencv@2/lib"

  export CPPFLAGS="-I/usr/local/opt/opencv@2/include"


For pkg-config to find opencv@2 you may need to set:

  export PKG_CONFIG_PATH="/usr/local/opt/opencv@2/lib/pkgconfig"
```

其实没有太弄清楚出现这个具体是出于什么原因，因为存在两种可能：

1. 之前安装的opencv3卸载之后环境没有清理干净，于是opencv2无法按照默认的路径配置环境；
2. 当前主流的OpenCV版本是3，也就是说OpenCV2已经out-of-date了，所以不再按照默认路径配置环境。

但不管原因是什么，还是要手动配置一下环境的，不然PATH和compilers这些都没法找到OpenCV2，打开~/.bash_profile，在里面添加上面所提到的一系列export语句即可，（我是考虑到近期用不到OpenCV3的版本，所以不是很怕版本冲突，把所有相关的都绑定在OpenCV2上了。）**但是要说明的是，不知道这个步骤是否有效帮助了最后程序的运行，即有可能不这样配置也没问题。**

可是这样配置环境之后，程序依然无法运行，后来去重新检查了CMakeLists.txt文件，发现里面显示报错：

```
Could not find module FindOpenCV.cmake or a configuration file for package
  OpenCV.

  Adjust CMAKE_MODULE_PATH to find FindOpenCV.cmake or set OpenCV_DIR to the
  directory containing a CMake configuration file for OpenCV.  The file will
  have one of the following names:

    OpenCVConfig.cmake
    opencv-config.cmake
```

在[stackoverflow上面搜到了相关的问题](https://stackoverflow.com/questions/8711109/could-not-find-module-findopencv-cmake-error-in-configuration-process)，里面提供了挺多的方案，其实本质上就是说它没法按照缺省的设置找到OpenCV的相关文件'，需要手动配置一下，所以首先需要在自己的电脑上找到相关文件的路径位置：

```
find / -name "OpenCVConfig.cmake"

<path_of_opencv>
```

然后把OpenCV_DIR的值设置为找到的包含那个文件的那个路径，我采取的方案是在CMakeLists.txt前面添加语句：

```
set (OpenCV_DIR /home/cmake/opencv/compiled) #change the path to match your complied directory of opencv
```

能够有效解决问题，sof上还提供了一些别的设置方法，可以供参考：

```
export OpenCV_DIR=<path_of_opencv>
```

## 4. CLion配置OpenCV环境

在CLion中的CMakeLists.txt中配置如下：

```
cmake_minimum_required(VERSION 3.9)
project(untitled1)

set(CMAKE_CXX_STANDARD 11)

#find_library(OpenCV)
find_package(OpenCV)

include_directories(${OpenCV_INCLUDE_DIRS})

add_executable(untitled1 main.cpp)
target_link_libraries(untitled1 ${OpenCV_LIBS})
```

可以像[这篇简书的文章](https://www.jianshu.com/p/b705d9eee23d)中写一个简单的main.cpp进行验证：


```C++
#include <iostream>
#include <opencv2/opencv.hpp>

using namespace std;
using namespace cv;

int main() {
    Mat srcImage = imread("your_img_path.jpg");
    if (!srcImage.data) {
        std::cout << "Image not loaded";
        return -1;
    }
    imshow("[img]", srcImage);
    waitKey(0);
    return 0;
}
```

## 参考资料
[stackoverflow上关于无法找到opencv相关文件的问答](https://stackoverflow.com/questions/8711109/could-not-find-module-findopencv-cmake-error-in-configuration-process)

[Mac CLion配置OpenCV环境](https://www.jianshu.com/p/b705d9eee23d)