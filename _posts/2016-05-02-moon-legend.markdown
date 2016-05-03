---
title:  转椅子——魔兽争霸之月纹传说
date:   2016-05-02 21:47
---

## 背景

前些天老葛玩魔兽争霸的时候，被一个叫做转椅子的游戏给拦住了去路。但是看见这一关，第一反应，我去，这不活生生一道OI题，作为一个曾经的OIer，我瞬间来了敲代码的欲望。

<div class="divider"></div>

## 规则

如下图，椅子杂乱的摆放着，现在你每次可以选择一个椅子，使其顺（逆）时针转90°，同时其上、下、左、右的椅子（如果存在）也会顺（逆）时针旋转90°。

现在要求使用如上规则，将所有椅子转回原位（都向上）。

<img src="{{ site.baseurl }}/assets/moon_lengend1.png" />

但既然都编程了，就不要之解决这一个特定的问题啦，我将题目修改为：

在x*y的方格上，每个格子里有一把椅子，方向是杂乱的，规则同上，输出如何将所有椅子转回原位（都向上）

<div class="divider"></div>

## 寻找

对着题目想了许久，并没有什么思路，这种题搜索看起来不靠谱，动态规划？然而这种十字形状的规则实在找不到规律。

印象中自己曾经做过一道与之类似的题，也是上下左右，于是开始百度……

首先发现了知乎上的一篇文章：[《点灯游戏：迟到8年的美丽，用数学绘制的“二维码”！》](https://zhuanlan.zhihu.com/p/19820908)，这篇文章写的挺有意思，但真正我想要的还得是立面提到的一篇文章（不知道是不是论文）：["Lights Out Puzzle." From _MathWorld_](http://mathworld.wolfram.com/LightsOutPuzzle.html)

至此，终于找到了这个名叫**Lights Out Puzzle**的游戏

<div class="divider"></div>

## 解决

看这个解决，请先看["Lights Out Puzzle." From _MathWorld_](http://mathworld.wolfram.com/LightsOutPuzzle.html)

文章中将问题最终表示成在mod 2的前提下求解线性方程组。

其中灯的状态有两种可能，1代表亮，0代表灭，在应用规则A[i,j]时，格子L[x,y]的状态更新为(L[x,y]+A[i,j][x,y]) mod 2，（最后的线性方程组每个A都写在一行中，L写在一列中）

现在考虑转椅子，可以将规则（暂时）简化为只允许顺时针旋转，这并不影响解决问题。

椅子的状态有四种可能，规定0代表上(↑)，1代表右(→)，2代表下(↓)，3代表左(←)，规则A与点灯问题完全一致。于是仿照点灯问题，在应用规则A[i,j]时，格子L[x,y]的状态的状态更新为(L[x,y]+A[i,j][x,y] mod 4。这样就可以得到一个需要在mod 4的前提下求解的线性方程组。

_例子_

> <center>
> ← ↓ ↑<br>
> → ← ↓
> </center>

L = 

> <center>
> ｜３２０｜<br>
> ｜１３２｜
> </center>

六个A分别是

> <center>
> ｜１１０｜　｜１１１｜　｜０１１｜<br>
> ｜１００｜　｜０１０｜　｜００１｜<br>
> <br>
> ｜１００｜　｜０１０｜　｜００１｜<br>
> ｜１１０｜　｜１１１｜　｜０１１｜
> </center>

于是得到线性方程组

> <center>
> ｜１１０１００｜｜x11｜　｜３｜<br>
> ｜１１１０１０｜｜x12｜　｜２｜<br>
> ｜０１１００１｜｜x13｜＝｜０｜<br>
> ｜１００１１０｜｜x21｜　｜１｜<br>
> ｜０１０１１１｜｜x22｜　｜３｜<br>
> ｜００１０１１｜｜x23｜　｜２｜
> </center>

解得X =

> <center>
> ｜１３１３１０｜'
> </center>

即，答案为

> <center>
> ｜１３１｜<br>
> ｜３１０｜
> </center>

意思是将L0 =

> <center>
> ↑ ↑ ↑<br>
> ↑ ↑ ↑
> </center>

这个状态的六个位置分别顺时针旋转90°，270°，90°，270°，90°，0°即可得到L

游戏中的要求是想反的，从L到L0，只需将顺时针改为逆时针即可。至此，问题就解决了^_^

<div class="divider"></div>

## 编程

最近在学Python的基础知识，就用Python实现了上述过程。

A矩阵的计算方法是在A[i,j]的点中，如果与(i,j)的[曼哈顿距离(Taxicab geometry)](https://en.wikipedia.org/wiki/Taxicab_geometry)小于等于1的点的值为1，否则为0。

求解线性方程组用的是[高斯消元法(**Gaussian elimination**)](https://en.wikipedia.org/wiki/Gaussian_elimination)，但只是理解了思想，所以写的应该是很不标准的(￣▽￣)"

需要提一下的是，由于我们是在mod 4的条件下求整数解，所以在往下减的时候，不能粗暴的直接相除然后按比例减，那样会出现分数。我用了lcm（最小公倍数）来算，具体看程序（但后来发现完全没有必要嘛，直接a行乘b[i]，b行乘a[i]就好啦）。

接下来是源码(Python3.4.3)

```python
def gcd(x, y):
    return x if y == 0 else gcd(y,x%y)

def lcm(x, y):
    return x*y//gcd(x,y)

#to create matrix A[x,y]
def rule(width, height, x, y):
    return [1 if abs(x-i)+abs(y-j) <= 1 else 0 for j in range(height) for i in range(width)]

def gauss(a):
    for i in range(len(a)):
        if a[i][i] == 0:
            for j in range(i+1, len(a)):
                if a[j][i] != 0:
                    k = a[i]
                    a[i] = a[j]
                    a[j] = k
                    break
        if a[i][i] == 0:
            continue
        for j in range(i+1, len(a)):
            if a[j][i] == 0:
                continue
            m = lcm(a[i][i], a[j][i])
            n = m // a[j][i]
            m = m // a[i][i]
            a[j] = [(a[j][x]*n-a[i][x]*m)%4 for x in range(len(a[i]))]
    for i in range(len(a)):
        a[i] = a[i][-2::-1] + [a[i][-1]]
    a = a[::-1]
    ans = []
    for i in range(len(a)):
        x = cal(a[i][i], a[i][-1])
        if x == 4:
            return []
        ans.append(x)
        for j in range(i+1, len(a)):
            a[j][-1] = (a[j][-1] - x*a[j][i]) % 4
    return ans[::-1]

#a not standard method to calculate x[i]
def cal(x, y):
    for i in range(4):
        if i*x % 4 == y:
            return i
    return 4

x = int(input('Input the width:'))
y = int(input('Input the height:'))

a = []
for j in range(y):
    for i in range(x):
        a.append(rule(x, y, i, j))

for j in range(y):
    st = input(('Input the No.%d line(divided by space):' % (j+1)))
    i = 0
    for n in st.split(' '):
        a[j*x+i].append(int(n))
        i += 1

ans = gauss(a)
if(len(ans) == 0):
    print('No answer!')
else:
    for j in range(y):
        for i in range(x):
            print('%2d ' % ans[j*x+i], end='')
        print()

```

<div class="divider"></div>

## 其他

通过点灯游戏再一次看到了百度百科和wikipedia的差距，可以自己感受一下（一个上来就暴力求解，另一个上来就跟你侃数学）（不算简介）。

[**点灯游戏**-百度百科](http://baike.baidu.com/link?url=J-veA0Pgtva_nwhq_A9PIlea62Dykm2PfimDstR_R-FmsRHztgxaXmWsE7-Fl4xvXUBZUkHWbUOVgideYvg6ZK)

[**Lights Out(game)** from _Wikipedia_](https://en.wikipedia.org/wiki/Lights_Out_%28game%29)

<div class="divider"></div>