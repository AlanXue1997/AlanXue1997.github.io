---
title:  Hello World
date:   2016-03-06 20:02
---

<script src="{{ site.baseurl }}/assets/prism.js" ></script>


## 前言

Hello World 已经几乎成了一种信仰。新学一门语言，如果不打出一个 Hello World，就会觉得少了点什么。拿起一本教材，如果书里没有Hello World，就会不自觉地鄙视作者。

这两天想了解一下github，发现用它可以搭建博客，于是就试了一下。

这是第一次用git，第一次用jekyll，第一次用markdown。尝试了各种各样的教程，经历了将近十次的失败，总算是整出了一个简陋的界面。最起码能够打出 Hello World 了。

由于这次的 Hello World 完成的异常艰难，我开始回忆之前各种各样的 Hello World 的难度，不经意间，就开始思考之前学习编程的道路。

<div class="divider"></div>

## 初中
初中有信息学的竞赛培训，想参加这个培训需要先通过一个考试，那次考的是数学，没记错的话得了87分。当时定的是成绩达到90或以上才可以参加培训，但由于我上的子弟班考的都很烂，老师一想，总不能一个名额都不给吧，于是就勉强让我加入了。

当时是每周五下午放学去上课，至今还清楚的记得第一节课老师在黑板上写的不明觉厉的代码：

```pascal
program name(input,output);
const
  pi = 3.14;
VAR
  r:integer;
begin
  readln(r);
  writeln(pi*r*r);
end.
```

第一节课老师就是从上至下的讲解了这一段代码。虽然听的很认真，但第一节课确实没有理解到底什么是常量，什么是变量。课后老师留的作业是（在纸上）写一个程序，输入长和宽，输出矩形的面积。

听完第一节课，可以说我就已经深深的迷上了编程。就利用第一节课学的那些一知半解的东西，我天天在演草纸上写自己能想出来的代码，计算矩形面积，计算正方形面积，计算直角三角形面积等等，总之当时会算的面积，体积肯定都给写了一遍。

不过那毕竟只是照猫画虎，还是纸上谈兵，第一节课老师并没有提到double，float这些单词，所以我很可能写出了一些不能运行的代码。

虽说那么喜欢编程，但我只学了一学期，就不去上课了，要说原因，还挺可笑的。首先是在第一学期快期末的时候，无意间听见老师说过：学这个肯定不耽误学习，要是耽误学习，我肯定就不让你来了……结果，那次期末我就退步了二百多名。其实老师说的没有那么认真，但我当真了。尤其是第二学期开学时候，前面说过我们班只有我一个人参加培训，而学生负责人又忘记通知我了，我就确信老师是不让我去了，也就没有问。直到初二下学期的时候，庐老师有一次在计算机课上闲聊，说咱们班原来貌似也有一个学的，学的还不错呢，不过不知道为啥不来了。我当时听见他说这话，心中是崩溃的，卧槽不是你不让我来的？！！！

当时想着，那就算了吧，高中还有机会，毕竟也有人是从高中才开始学的嘛。

<div class="divider"></div>

## 高中

高一的时候，有一本数学书讲了编程，我一看，BASIC语言，上网一查貌似还挺神奇，可以直接有窗体，意思不就是终于不是只有黑窗口了？！于是赶紧去学了学，趁寒假把书上所有的例子都用Visual Basic给做了出来。

但Basic语言总觉得写得不顺手，我就想，有没有跟VB那样直接由窗口，但是用Pascal写的？当然有，我找到了Delphi，但实在用不好，基本上就是勉强写出Hello World后，就没打开过它。

其实高中一开始，我就一直在等段老师通知，问问谁有兴趣参加竞赛的培训，就这样等了一学期→\_→。第二学期我一想，初中就因为自己一直等，错过了机会，这次不能等了，得主动去问。一问才知道，人家只在奥班通知了→\_→，都学了一学期了。然而我初中的水平仅仅是学到了for循环。然而我还是想试试。

说到这就要感谢一下胡老师了，因为我是后来加入的，不在一个机房，胡老师加了不少班。最终我也终于学完了竞赛需要用到的Pascal知识。

在高一升高二的暑假，我在网上自学了C语言，看的是郝斌老师的教程。郝斌老师教的好不好我不太确定，反正我是听的很明白，用的很舒服。

后来得省一之后，就比较浪了，发现人家高手好多都写C啊，于是突然有一天，我也开始用C做题，发现偶尔还能参杂点C++的东西，甚是好用，于是就又学了一些C++

记不清是在高二还是高三的时候，想算点什么东西，发现计算器精度不够，然而不论是C还是Pascal，写高精度也太费劲了吧？！于是我就上我查，发现了一个可以当计算器用的神器：Python[ˈpaɪθɑ:n]（写音标是因为我整个高中时代都念错了-_-!）

第一次写Python，感觉真是爽翻了，`2**1000`信手拈来，这不就是一个超级好用的计算器？于是我并没有去学Python的语法究竟是什么，只是拿它当计算器用了用。

<div class="divider"></div>

## 大学

大一上学期学的是C语言，而且还学的很简单。在大一年度项目里写Unity里的脚本，C#和JS傻傻分不清楚，各种不明觉厉完全就是为了做项目强行去写，不太理解自己写的是什么。

下学期开始学java了，才发现我虽然会点C++，但并没有理解什么是面向对象，现在还在学java中→_→

前两天觉得，每次打java都要在cmd里`javac xxx.java`然后再`java xxx`,太费劲了（其实就是懒癌晚期），于是我想到了批处理.bat，都是套路，上网查查呗，然后大家都说批处理神马的太过时了，还不如写个Python脚本。那我就听大家建议呗，看看Python怎么写，整了一下午，总算是整出来了一个暂时满足我要求的：

```python
import sys
import os
name = ""
comp = True
runn = True
for i in range(1, len(sys.argv)):
    if(sys.argv[i][0] == '-'):
        if(sys.argv[i] == '-c'):
            comp = False
        elif(sys.argv[i] == '-r'):
            runn = False
        else:
            print('错误，非法的命令：', sys.argv[i])
            exit();
    elif(name == ""):
        name = sys.argv[i]
    else:
        print('错误，出现多个文件名：', sys.argv[i])
        exit()
        
if(name == ""):
    name = input('请输入文件名：')

if(comp):
    if(not(os.path.exists(name + '.java'))):
        print('错误，未找到', name+'.java')
        exit()
    if(os.system('javac ' + name + '.java') == 0):
        print('编译成功')
    else:
        print('编译失败')
        exit()

if(runn):
    if(not(os.path.exists(name + '.class'))):
        print('错误，未找到', name+'.class')
    print('运行结果：')
    os.system('java ' + name)

```

<div class="divider"></div>

## Hello World

最后，写上至今学的各式各样的Hello World吧，毕竟信仰。

#### Pascal

```pascal
program HelloWorld(input,output);
begin
  writeln('Hello World');
end.
```

#### C

```c
#include<stdio.h> 

int main()
{
  printf("Hello World\n");
  return 0;
}
```

#### C++

```cpp
#include<cstdio>
#include<iostream>
using namespace std;

int main(void)
{
  cout<<"Hello World"<<endl;
  return 0;
}

```

#### Python 3

```python
print("Hello World")
```

#### Java

```java
import java.util.*;

public class HelloWorld{
	public static void main(String[] args){
		System.out.println("Hello World");
	}
}
```

<div class="divider"></div>