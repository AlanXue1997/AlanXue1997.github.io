---
layout: article
title: Alan的Python笔记
mathjax: true
tags: Python 笔记
key: 2018-07-23-alan-Python
published: true
---

这是个笔记，并非教程。
{:.warning}

# 前言

这里将记录个人在写Python的过程中遇到的各种问题，以及解决方法。如果大家发现哪里写错了，过时了，或有别的任何问题，欢迎在下方评论或发邮件告知。

<!--more-->

# 环境

## 安装包

### 安装```.whl```文件

```
pip install xxx.whl
```

# 文件

## 路径相关

### 获取所有txt文件

```python
import glob, os
os.chdir(".")
for file in glob.glob("*.txt"):
    print(file)
```

详情：[Find all files in a directory with extension .txt in Python](https://stackoverflow.com/questions/3964681/find-all-files-in-a-directory-with-extension-txt-in-python)

# NumPy

## 环境

### ImportError: numpy.core.multiarray failed to import

将NumPy的版本从3降到了2，之后```import cv2```就出现了这个错误。

解决方法：

```
pip install -U numpy
```

来源：[ImportError: numpy.core.multiarray failed to import](https://stackoverflow.com/questions/20518632/importerror-numpy-core-multiarray-failed-to-import)

## ndrray

### 赋值慢

*for loop* is **always** slower than you think!!!

在写一个处理Kinect v2的数据的代码的时候，写出了这样的代码

```python
frame = np.zeros(424, 512), dtype=np.uint8)
mask = depth * (mask > 10)
for i in range(424):
    for j in range(512):
		frame[x[i,j],z[i,j]] = mask[i,j]
```

这次的程序是实时的，按说这个循环也就$$10^6$$，应该能在一帧（大约0.03~0.05秒）内搞定的，但没想到，实际用时达到了接近一秒，直接导致了程序不可用。

最终，我发现，NumPy里其实也是可以像MATLAB一样，直接拿矩阵当index来操作的，也就是直接这样写

```python
frame[x,z] = mask
```

问题解决之后，感觉这是个很蠢的问题，其主要原因还是自己对NumPy，以及Python的不熟悉。

来源：[numpy 2D array assignment with 2D value and indices arrays](https://stackoverflow.com/questions/29562822/numpy-2d-array-assignment-with-2d-value-and-indices-arrays)

### 合并

append

需要复制，耗费大量内存和时间

concatenate

例子

```python
import numpy as np
a = np.loadtxt('bruno silveira.txt')
b = np.loadtxt('domarys.txt')
print(a.shape)
print(b.shape)
c = np.concatenate((a,b), axis=0)
print(c.shape)
```

输出

```
(6, 31)
(5, 31)
(11, 31)
```

# Open-CV

## 切换版本

首先在这个[网站](https://www.lfd.uci.edu/~gohlke/pythonlibs/#opencv)找到想要的版本，下载```whl```文件，之后执行```pip install xxx.whl```即可。

# scikit-learn

## SVM

```python
import numpy as np
X = np.array([[-1, -1], [-2, -1], [1, 1], [2, 1]])
y = np.array([1, 1, 2, 2])
from sklearn.svm import SVC
clf = SVC()
clf.fit(X, y) 

print(clf.predict([[-0.8, -1]]))
```

# requests

