---
title:  藏于图片的信息
date:   2016-06-10 17:21
---

## 背景

上学期刚开学的时候，在软件工程导论课（网课，在[中国大学MOOC](http://www.icourse163.org/)上）上，[战德臣](http://www.cs.hit.edu.cn/?q=node/62)老师曾经讲过一个信息隐藏问题，可以利用人眼对颜色的微小变化不敏感这一特点，将信息隐藏在图片之中，从而掩人耳目。但我曾经尝试过用Ｃ、Pascal去处理图片，但那个难度是有的，不可能简单百度一下就去应用，所以就没有去尝试。

这几天正在学着使用MATLAB，发现它处理起图像是如此的轻松加愉快，于是就想拿战老师讲的这个信息隐藏的方法试试。

<div class="divider"></div>

## 方法

图片在MATLAB中存储为一个三维矩阵[height, width, 3([RGB](http://baike.baidu.com/link?url=iLhWJJu4SxxWPlKqg86uLqa9Gjs9_LgxJAje4CofevlOcTsC7LSNMmtnOn3GoWhvQyfWr9rFLYfEb2MiBaRxb_))]，数据类型是8位无符号整形（uint8），也就是0~255之间。

将信息存储在每个数的最后一位（二进制位），只有这样，颜色的变化才是人眼看不出来的。也就是说，在存储的时候，每个数相当于1位。因为每个像素都有R、G、B三个值，所以每个像素可以存储3位。

每一个字符占16位（若只有ASCII码，8位），为了方便写代码，用三的整数倍，18位（9位）表示一个字符，也就是说，每6个（3个）像素，表示一个字符。

具体方法如下（图片都是放大后的）：

例：要将字符'例'存入某图片的前6个像素

<img src="{{ site.baseurl }}/assets/pix1.png" />

在MATLAB中存储为

> <center>
> [092 217 205] [234 247 134] [255 181 161]<br>
> [184 255 184] [184 244 255] [095 217 205]
> </center>

排在一列

> <center>
> ０９２<br>
> ２１７<br>
> ２０５<br>
> ２３４<br>
> ２４７<br>
> １３４<br>
> ２５５<br>
> １８１<br>
> １６１<br>
> １８４<br>
> ２５５<br>
> １８４<br>
> １８４<br>
> ２４４<br>
> ２５５<br>
> ０９５<br>
> ２１７<br>
> ２０５
> </center>

二进制即

> <center>
> ０１０１１１００<br>
> １１０１１００１<br>
> １１００１１０１<br>
> １１１０１０１０<br>
> １１１１０１１１<br>
> １００００１１０<br>
> １１１１１１１１<br>
> １０１１０１０１<br>
> １０１００００１<br>
> １０１１１０００<br>
> １１１１１１１１<br>
> １０１１１０００<br>
> １０１１１０００<br>
> １１１１０１００<br>
> １１１１１１１１<br>
> ０１０１１１１１<br>
> １１０１１００１<br>
> １１００１１０１
> </center>

字符'例'在matlab中的编码（Unicode）是20363，二进制为100,1111,1000,1011，补位18位，即00,0100,1111,1000,1011，用这18个数依次替换上面那18个RGB值的最后一位，得到

> <center>
> ０１０１１１００<br>
> １１０１１０００<br>
> １１００１１００<br>
> １１１０１０１１<br>
> １１１１０１１０<br>
> １００００１１１<br>
> １１１１１１１１<br>
> １０１１０１０１<br>
> １０１００００１<br>
> １０１１１００１<br>
> １１１１１１１１<br>
> １０１１１０００<br>
> １０１１１０００<br>
> １１１１０１００<br>
> １１１１１１１１<br>
> ０１０１１１１０<br>
> １１０１１００１<br>
> １１００１１０１
> </center>

重新转化成RGB值，得到

> <center>
> [092 216 204] [235 246 134] [255 181 161]<br>
> [185 255 184] [184 244 255] [094 217 205]
> </center>

对应的图片为

<img src="{{ site.baseurl }}/assets/pix2.png" />

变换前后是看不出什么变化的。

<div class="divider"></div>

## 实现

实现和上面方法稍有不同的是，并没有直接替换最后一位，而是如果最后一位本来就是想存的数，则不处理；否则，将这个数减1（如果是0，变为1）。效果和上面的方法是一样的。

下面是用MATLAB的写的两个函数：

imageEncrypter，将st的内同存进im中

```matlab
function I = imageEncrypter(st, im)

array = [abs(st) 0];
n = length(array);

im = double(im);
x = size(im, 1); y = size(im, 2);

fprintf('This image can save %.0f chars at most.\n', x*y/6);
if n > x*y/6
  fprintf('Sorry, you can''t save %.0f chars in this image\n', n);
  I = im;
  return;
end

k = 1;
p = 5;
for i = 1:x
  for j = 1:y
    if bitxor(1&bitand(2^(3*p+2), array(k)), bitand(im(i, j, 1), 1))
      im(i, j, 1) = im(i, j, 1) - 1 + 2*(im(i, j, 1) == 0);
    end
    if bitxor(1&bitand(2^(3*p+1), array(k)), bitand(im(i, j, 2), 1))
      im(i, j, 2) = im(i, j, 2) - 1 + 2*(im(i, j, 2) == 0);
    end
    if bitxor(1&bitand(2^(3*p), array(k)), bitand(im(i, j, 3), 1))
      im(i, j, 3) = im(i, j, 3) - 1 + 2*(im(i, j, 3) == 0);
    end

    p = p-1;
    if p == -1
      p = 5;
      k = k + 1;
    end
    if k > n
      break;
    end
  end
  if k > n
    break;
  end
end

I = uint8(im);

end
```
imageDecrypter，将im中存的信息提取出来

```matlab
function st = imageDecrypter(im)

im = double(im);
x = size(im, 1); y = size(im, 2);

num = [];

p = 0;
k = 0;
for i = 1:x
  for j = 1:y
    k = k*2 + bitand(im(i, j, 1), 1);
    k = k*2 + bitand(im(i, j, 2), 1);
    k = k*2 + bitand(im(i, j, 3), 1);
    p = p + 1;
    if p == 6
      if k == 0
	k == -1;
	break;
      end
      p = 0;
      num = [num k];
      k = 0;
    end
  end
  if k == -1
    break;
  end
end

st = char(num);

end
```

另外，为了解决换行的问题，还写了一个函数getStringFromFile，可以读取整个txt

```matlab
function st = getStringFromFile(s)

f = fopen(s, 'r');

st = [];
s = fgets(f);

while s ~= -1
  st = [st char(13, 10)' s];
  s = fgets(f);
end

fclose(f);
end
```

<div class="divider"></div>

## 其他

这样，以后就可以藏各种文本信息了，就算藏到头像里，也没人能发现(●ˇ∀ˇ●)

但其实我并没有发现什么可存的(-__-)!

在开始的时候，我误以为添加过信息的图片看起来会多多少少有些不自然。但没想到，完全看不出来的有木有！！！比如下面这两张图，前者是原图，后者在里面隐藏了整篇千字文，完全看不出区别。

#### before

<img src="{{ site.baseurl }}/assets/zhu.png" />

#### after

<img src="{{ site.baseurl }}/assets/zhux.png" />

<div class="divider"></div>