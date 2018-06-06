---
layout: article
title: 简单密码加密\解密——尝试Java调用MATLAB
mathjax: true
key: 2016-08-27-java-matlab
---

# 背景

今天突然设想出这样一个程序，它会随机出一个密码，并给出一个密钥。使用者使用这个密码（比如压缩文件的密码），但是只记住密钥，不记住密码。程序将设定一个特定的日期，在此日期之前密钥是无效的，此日期之后，程序将根据密钥给出密码。

<!--more-->

简单的说，就是制造一个短时间内无法被得知的密码，在特定的日期才能被解开。

这个其实只是玩玩而已，所以想了一个很简单很简单的方法——矩阵乘法。程序中有一个**固定的**6×6的可逆矩阵M,具体的值是随机的；密码为6位，存储在一个1×6的矩阵A中；解锁日期也是6位（比如2016年8月27日，则为160827），存储在1×6的矩阵D中；密钥有12位，存储在一个1×12的矩阵key中，前六位是加密后的密码，后六位是加密后的解锁日期。它们之间有如下关系：

> <center>
> key=|A*M D*M|
> </center>

如果了解[矩阵乘法](http://baike.baidu.com/link?url=_RigwzNOkF17D9cjiaAGAdT0SpwuYzY6Uw0dwiCWXkL_OVk0A6BhGB5oNGhOidBVPUIDbkT94HVnH_HCX3Rqeq)和[可逆矩阵](http://baike.baidu.com/view/689609.htm)，就不难理解。举个例子:

M = 

> <center>
> ｜７８１８６１｜<br>
> ｜３５９９１９｜<br>
> ｜９４７１４８｜<br>
> ｜７９６０８８｜<br>
> ｜６７７４６２｜<br>
> ｜６０２０１７｜
> </center>

A = 

> <center>
> ｜１２３４５６｜
> </center>

D = 

> <center>
> ｜１６０８２７｜
> </center>

则，key = \|A\*M D\*M\| = 

> <center>
> 　｜０９４｜‘<br>
> 　｜１３５｜<br>
> 　｜１１０｜<br>
> 　｜１３１｜<br>
> 　｜０９９｜<br>
> 　｜０５９｜<br>
> 　｜１１０｜<br>
> 　｜１１８｜<br>
> 　｜１５９｜<br>
> 　｜１７１｜<br>
> 　｜１５０｜<br>
> 　｜０７１｜
> </center>

由于在解密的时候需要计算M<sup>-1</sup>，也就是求矩阵的逆（其实可以一开始就求好，存着就行，但我是后来才发现的(-__-)!）。Andrew老师总是强调说，**不要自己写算法**，除非你是数值计算的专家。于是我就想，java里面有没有算矩阵的库？百度了一下，应该是有的，网上有对应的库，但不太统一。

突然想起来，我似乎从来没有试过在一个程序中使用两种或以上的语言，那这次不妨试试用matlab算矩阵，剩下的逻辑和界面用java来写。

# 实现

### Matlab部分

首先，需要写出加密和解密的两个函数：

```matlab
function code = Encrypt(encryptMatrix, passwd)
code = passwd*encryptMatrix;
```

△这个函数的作用是计算A\*M和D/*M

```matlab
function passwd = Decrypt(encryptMatrix, code)
passwd = round(code*pinv(encryptMatrix));
```

△这个函数的作用是通过key的前六位或后六位，逆推A和D

接下来才是重点，以上两个函数都存在于.m文件中，java是无法调用的，所以需要生成为jar包，让java可以在代码中直接使用。

笔者使用的是MATLAB R2014a 8.3.0.532，java 1.7.0_79。

生成jar包的步骤如下：

* 在MATLAB命令行窗口中进入存放jar包的文件夹

* 在MATLAB的命令行窗口中输入```deplyotool```以打开MATLAB Compiler，选择**Library Compiler**

* MATLAB Compile的左上角，APPLICATION TYPE选择**Java Package**，在EXPORTED FUNCTIONS中添加两个函数对应的.m文件

* 填写Application Information信息，修改类名

* 点击右上角的Package就会生成一个文件夹，里面可以找到jar包。

### Java部分

Java中，比较关键的问题是，Encrypt/Decrypt这两个函数在Java里究竟长什么样子，又该如何使用？

首先是引用包，需要引用的包有两个，第一个就是刚刚生成的jar包，第二个是**javabuilder.jar**，它存在于Matlab安装目录/toolbox/javabuilder/jar/……，32位和64位略有不同，请正确选择。

之后，看看两个函数的样子，如果打开文档，会看到Encrypt的Decrypt都有三种用法。这里我只选用了最好理解的一种：

```
public void Decrypt(java.lang.Object[] lhs,
           java.lang.Object[] rhs)
             throws com.mathworks.toolbox.javabuilder.MWException

Provides the interface for calling the Decrypt M-function where the first input, an Object array, receives the output of the M-function and the second input, also an Object array, provides the input to the M-function.

参数:
    lhs - array in which to return outputs. Number of outputs (nargout) is determined by allocated size of this array. Outputs are returned as sub-classes of com.mathworks.toolbox.javabuilder.MWArray. Each output array should be freed by calling its dispose() method.
    rhs - array containing inputs. Number of inputs (nargin) is determined by the allocated size of this array. Input arguments may be passed as sub-classes of com.mathworks.toolbox.javabuilder.MWArray, or as arrays of any supported Java type. Arguments passed as Java types are converted to MATLAB arrays according to default conversion rules.
抛出:
    com.mathworks.toolbox.javabuilder.MWException - An error has occurred during the function call.
```

这种方法，输入时两个Object[]，lhs是返回值，rhs是输入值，调用时，只需将矩阵按顺序放在rhs中，调用完后，从lhs中就可以取出返回的矩阵。

还有一个问题是，Java中如何表示Matlab的矩阵？

这需要用到javabuilder中的MWNumericArray类，用法如下：

```java
int[] dims = {6, 6};
int[] data = {
			7, 8, 1, 8, 6, 1,
			3, 5, 9, 9, 1, 9,
			9, 4, 7, 1, 4, 8,
			7, 9, 6, 0, 8, 8,
			6, 7, 7, 4, 6, 2,
			6, 0, 2, 0, 1, 7};
MWNumericArray A = MWNumericArray.newInstance(dims, data, MWClassID.DOUBLE);
```
至此，比较棘手的问题就都解决了。下面介绍一种通过网络获取日期的方法。

```
try{
	URL url = new URL(webUrl);
	URLConnection uc = url.openConnection();
	uc.connect();
	long ld = uc.getDate();
	Date date = new Date(ld);
	SimpleDateFormat sdf = new SimpleDateFormat("yyMMdd", Locale.CHINA);
	String nowDate = sdf.format(date);
}
catch(MalformedURLException e){
	e.printStackTrace();
}
catch(IOException e){
	e.printStackTrace();
}
```

其中webUrl是任意的一个网站，“http://www.baidu.com”就可以。

接下来，给出一个可以运行的代码，已经实现了newPasswd（不完全，没有添加日期）、getPasswd、getDate等方法，但没有编写交互关系。

```java
import java.io.IOException;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;

import com.mathworks.toolbox.javabuilder.MWClassID;
import com.mathworks.toolbox.javabuilder.MWNumericArray;

import EncryptToolForRen.EncryptTool;

public class Main {
	
	private static int date;
	private static EncryptTool encryptTool;
	private static MWNumericArray M;
	
	public static void main(String[] args){
		init();
		int[] passwd = new int[6];
		int[] key = new int[12];
		newPasswd(passwd, key);
		for(int i: key){
			System.out.printf("%03d ", i);
		}
		System.out.println();
		if(getPasswd(passwd, key)){
			for(int i: passwd){
				System.out.print(i);
			}
		}
	}
	
	private static void init(){
		date = Integer.valueOf(getDate("http://www.baidu.com"));
		try{
			encryptTool = new EncryptTool();
		}
		catch(Exception e){
			System.out.println(e);
			return;
		}
		int[] dataDims = {6, 6};
		int[] data = {
				7, 8, 1, 8, 6, 1,
				3, 5, 9, 9, 1, 9,
				9, 4, 7, 1, 4, 8,
				7, 9, 6, 0, 8, 8,
				6, 7, 7, 4, 6, 2,
				6, 0, 2, 0, 1, 7};
		M = MWNumericArray.newInstance(dataDims, data, MWClassID.DOUBLE);
	}
	
	private static boolean getPasswd(int[] passwd, int[] key){
		int[] keyDate = new int[6];
		for(int i = 0; i < 6; ++i) keyDate[i] = key[i+6];
		int[] dims = {1, 6};
		MWNumericArray A = MWNumericArray.newInstance(dims, keyDate, MWClassID.DOUBLE);
		Object[] objects = new Object[2];
		Object[] ans = new Object[1];
		objects[0] = M;
		objects[1] = A;
		try{
			encryptTool.Decrypt(ans, objects);
			String[] strings = ans[0].toString().split(" ");
			int nowDate = 0;
			for(String st: strings){
				if(st.equals("")) continue;
				nowDate = nowDate/10 + Integer.valueOf(st)*100000;
			}
			if(nowDate > date){
				System.out.printf("Sorry, this password is invisible until 20%d.%d.%d", nowDate/10000, nowDate/100%100, nowDate%100);
				return false;
			}
			A = MWNumericArray.newInstance(dims, key, MWClassID.DOUBLE);
			objects[1] = A;
			encryptTool.Decrypt(ans, objects);
			strings = ans[0].toString().split(" ");
			int i = 0;
			for(String st: strings){
				if(st.equals("")) continue;
				passwd[i++] = Integer.valueOf(st);
			}
			return true;
		}
		catch(Exception e){
			System.out.println(e);
			return false;
		}
	}

	private static void newPasswd(int[] passwd, int[] key){
		for(int i = 0; i < 6; ++i) passwd[i] = i+1;//(int)(10 * Math.random());
		int[] dims = {1, 6};
		MWNumericArray A = MWNumericArray.newInstance(dims, passwd, MWClassID.DOUBLE);
		Object[] objects = new Object[2];
		Object[] ans = new Object[1];
		objects[0] = M;
		objects[1] = A;
		try{
			encryptTool.Encrypt(ans, objects);
			String[] strings = ans[0].toString().split(" ");
			int i = 0;
			for(String st: strings){
				if(st.equals("")) continue;
				key[i++] = Integer.valueOf(st);
			}
			int now = 160827;//date;
			int[] nowDate = new int[6];
			for(int j = 0; j < 6; ++j){
				nowDate[j] = now % 10;
				now /= 10;
			}
			A = MWNumericArray.newInstance(dims, nowDate, MWClassID.DOUBLE);
			objects[1] = A;
			encryptTool.Encrypt(ans, objects);
			strings = ans[0].toString().split(" ");
			for(String st: strings){
				if(st.equals("")) continue;
				key[i++] = Integer.valueOf(st);
			}
		}
		catch(Exception e){
			System.out.println(e);
		}
	}
	
	private static String getDate(String webUrl){
		try{
			URL url = new URL(webUrl);
			URLConnection uc = url.openConnection();
			uc.connect();
			long ld = uc.getDate();
			Date date = new Date(ld);
			SimpleDateFormat sdf = new SimpleDateFormat("yyMMdd", Locale.CHINA);
			return sdf.format(date);
		}
		catch(MalformedURLException e){
			e.printStackTrace();
		}
		catch(IOException e){
			e.printStackTrace();
		}
		return null;
	}
}

```

<div class="divider"></div>

# 问题

### Matlab部分

##### 1.选择Library Compiler后，弹出“无法保存工程Untitled1。您可能没有相应权限……”

出现这种错误，说明你没有权限修改MATLAB当前所处的文件夹（界面左侧的当前文件夹，或者输入pwd查看）。此时应该回到上一步，重新选择一个存放jar包的文件夹。

##### 2.点击Package后，出现Error during package

点击Open log file，里面如果有“matlab Test checkout of feature 'Compiler' failed”，那说明你正在使用的Matlab破解不完全，提供一个[解决链接](http://blog.sina.com.cn/s/blog_4a189c920102w3hx.html)。如果错误信息不同，请自行百度或谷歌。

### Java部分

##### 3.出现“错误使用 \*。MTIMES 不完全支持整数类。至少有一个输入必须为标量。要按元素进行 TIMES 计算，请改用 TIMES (.\*)。”

出现这种错误，你可能在生成矩阵时使用了MWClassID.INT32或MWClassID.INT64等。将其改成MWClassID.DOUBLE即可解决。