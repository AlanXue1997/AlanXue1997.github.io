---
layout: article
title: 使用python控制LEGO-NXT（Win10）
mathjax: true
tags: NXT python 配环境
key: 2018-08-16-nxt-python
---

# 前言

LEGO NXT官方的编程语言是[NXT-G](https://en.wikipedia.org/wiki/Lego_Mindstorms_NXT#NXT-G)，这个语言是基于图形界面的，设计的非常适合不熟悉编程的人群使用，然而如果想编写一些较为复杂的程序，这门语言就显得及其繁杂了。因为这门语言相当于在画程序流程图，当分支变多了之后，可读性会非常差。

<!--more-->

其实除了NXT-G，还有非常多的语言可以给NXT进行编程，由于我最近用Python较多，因此选择尝试通过python来控制nxt。

Github上有一个[nxt-python](https://github.com/Eelviny/nxt-python)项目，已经八年之久了，不过作者并没有放弃，仍然在维护，本文接下来将记录我在Windows10上使用这个项目的配环境的过程。

# 安装NXT-Python

首先，注意到Github主页上作者的提示

>DISCLAIMER: The NXT-Python v3 implementation is incomplete. Development is not active due to lack of interest. Find the latest stable version for Python2 on the releases page. Pull requests and improvements are very welcome!

除了这里之外，作者也在不同的地方多次提到**目前master分支的代码不好用，甚至不可用**，因此强烈建议安装[releases](https://github.com/Eelviny/nxt-python/releases)里面的版本。

在写本文时，releases页面的最新版是```v2.2.2```，这个版本是针对```python 2.6```的，我使用的是```python 2.7```，可以正常使用，但```python 3```不可以。

下载并解压```v2.2.2```的压缩包，进入```nxt-python-2.2.2```文件夹，用python2运行

```
python setup.py install
```

接下来，打开一个python终端，尝试import

```python
import nxt
```

如果能够成功import，则说明安装成功。但根据[Installation Instructions](https://github.com/Eelviny/nxt-python/wiki/Installation)中的内容，我们还应该安装一个连接方式，有**USB连接**和**蓝牙连接**两种可选，我都尝试过，不过最终选择的是USB连接。

使用USB连接的话，需要安装[PyUSB](https://sourceforge.net/projects/pyusb/)，其安装方法与nxt-python是一样的。

注意：使用USB连接的时候**不能安装**[PyBluez](https://code.google.com/archive/p/pybluez/downloads)。
{:.warning}

# 测试

在```nxt-python-2.2.2```文件夹下有一个```examples```文件夹，里面有一些测试代码，nxt-python的作者建议随便挑一个尝试运行，我选择的是```spin.py```。

```python
#!/usr/bin/env python

import nxt.locator
from nxt.motor import *

def spin_around(b):
    m_left = Motor(b, PORT_B)
    m_left.turn(100, 360)
    m_right = Motor(b, PORT_C)
    m_right.turn(-100, 360)

b = nxt.locator.find_one_brick()
spin_around(b)
```

这个代码非常简单，运行时需要将NXT主机的B和C端口接上马达。如果这份代码能够运行成功，则说明nxt-python已经能够正常工作了！

# 问题处理

下面说一下遇到问题的处理方法

## MINDSTORMS NXT 2.0是否工作正常

[MINDSTORMS NXT 2.0](https://www.lego.com/en-us/mindstorms/downloads/nxt-software-download)是官方的编程工具，使用NXT-G语言，官方不支持中文。

不论是USB还是蓝牙，在检查其他问题之前，一定要保证该软件能够与NXT正常连接。

## 检查连接方式是否安装

正如前文提到的，使用USB连接需要安装[PyUSB](https://sourceforge.net/projects/pyusb/)，使用蓝牙连接需要安装[PyBluez](https://code.google.com/archive/p/pybluez/downloads)。使用USB连接的时候**不能**安装PyBluez。

## 驱动是否正确

不出意外的话，许多人都会碰到这个问题，PyUSB需要依赖libUSB驱动来运行（来源：[nxt-python error: usb.core.NoBackendError](https://stackoverflow.com/questions/41310102/nxt-python-error-usb-core-nobackenderror)），但NXT在windows10上默认的驱动是WinUSB（就是右下角能够弹出设备的那种）。

因此我们需要安装[libusb](https://github.com/libusb/libusb)，其官方[wiki](https://github.com/libusb/libusb/wiki/Windows)推荐使用[Zadig](http://zadig.akeo.ie/)安装，我也强烈建议使用此安装方式，非常方便，且改软件是不需要安装的，不会影响系统环境。

首先连接NXT，然后运行Zadig，选择```Options->List all devices```

![swagger-1](/assets/images/nxt-python-1.png)

接着，选中NXT设备，并将Driver换成libusb，单机Replace Driver即可，换完之后如图

![swagger-1](/assets/images/nxt-python-2.png)

此时右下角应该已经不能“弹出该设备”，但MINDSTORMS NXT 2.0仍然能正常工作。

至此，我的电脑中```spin.py```已经能够成功运行。