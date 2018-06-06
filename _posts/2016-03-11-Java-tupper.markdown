---
layout: article
title:  Java实现Tupper自我指涉公式的计算
mathjax: true
key: 2016-03-11-Java-tupper
---

# Tupper自我指涉公式

最早是在Matrix67的《思考的乐趣》中知道了Tupper自我指涉公式。当时的感觉仅仅是挺神奇的。后来逛Matrix67的博客时，无意间看见的他关于此公式的[博文](http://www.matrix67.com/blog/archives/301)，发现了这句话：

> ***你甚至可以告诉你的MM，说你发现了一个函数，函数在某个位置的图象正好是某某某我爱你的字样。***

一语惊醒梦中人啊，这句话让我大受启发，此公式的作用远不止自指涉那一个用途啊。

<!--more-->

# 尝试

高中时候晚上一到家就不想学习，那天实在没事干了，就想起了这个公式，要不我也算一个n？想了半天，实在没啥好词，那就写“九班加油”好了。

以下是步骤：

一、先要整出那种像素很少的图片：

  <img src="{{ site.baseurl }}/assets/images/tupper1.png" />

  _方法很简单，百度“像素字+在线”即可找到相应工具。_

二、将这些字的像素转换成二进制码，顺序就是从上到下，从左到右，先列后行。
  （太过久远，当时转换的找不到了）

三、然后把二进制码当成一个二进制数，转换成十进制数。当时是用Python算的，毕竟高中一直拿Python当计算器用。

四、到第三步已经可以算完成了，但我当时还想要一个更漂亮的图象，于是自己又画了一个（微软自带画图，用橡皮画的）。

最终效果如下：

<img src="{{ site.baseurl }}/assets/images/tupper2.jpg" />

<img src="{{ site.baseurl }}/assets/images/tupper3.jpg" />

<img src="{{ site.baseurl }}/assets/images/tupper4.png" />

这次效果还不错，但太费时间了，做完之后心力憔悴，几年都不想碰这个公式。

# Java实现

懒惰(Laziness)是优秀程序员的三大美德之一。所以当这几天又想到Tupper的公式时，我想不能这样下去了，我要一劳永逸的解决这个问题。嗯，编程实现。

方法跟上面半手工的方法是一样的，这学期在学java，所以就用java写了一个，可以实现字符串向图像/n值得转换，以及n值向图像的转换：

```java

import java.util.*;
import java.math.*;
import java.awt.geom.*;
import java.awt.font.*;
import java.awt.image.*;
import java.awt.*;
import java.io.*;
import javax.imageio.*;

public class Tupper{
    public static void main(String[] args) throws Exception {

      Scanner in = new Scanner(System.in);

      //选择文字转图象（n值），或n值转图象
      String st; char ch = ' ';
      while(ch != 'A' && ch != 'B'){
	System.out.print("(a)文字 -> 图象/n值\n(b)n值 -> 图象\n请输入a或b：");
	st = in.nextLine();
	st = st.toUpperCase();
	ch = st.charAt(0);
      }
     
      //输入相应参数
      if(ch == 'A'){
	System.out.print("输入一个字符串：");
	String content = in.nextLine();
	System.out.print("输入图片的行数：");
	int lines = in.nextInt();
	System.out.print("输入图片的名称：");
	in.nextLine();
	String name = in.nextLine();
	name = name + ".png";
	BigInteger number = createImage(content, lines, new Font("黑体", Font.BOLD, 72));
	System.out.println("n = " + number);
	translateImage(lines, 10, number, new File(name));
      }
      else{
        System.out.print("输入行数：");
	int lines = in.nextInt();
	System.out.print("输入n值：");
	BigInteger number = in.nextBigInteger();
	System.out.print("输入图片的名称：");
	in.nextLine();
	String name = in.nextLine();
	name = name + ".png";
	translateImage(lines, 5, number, new File(name));
      } 
    }

    public static void translateImage(int lines, int side, BigInteger number, File outFile) throws Exception {

      number = number.divide(BigInteger.valueOf(lines));

      //判断所需要的列数
      BigInteger x = number;
      int k = 0;
      while(x.compareTo(BigInteger.valueOf(0)) != 0){
	k++;
	x = x.divide(BigInteger.valueOf(2));
      }
      k = k/lines + 1;

      //计算图片的长宽
      int width = (k + 2) * side;
      int height = (lines + 2) * side;

      //创建图片
      BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_BGR);
      Graphics g = image.getGraphics();
      g.setColor(Color.WHITE);
      g.fillRect(0, 0, width, height);//背景色

      //绘制图象
      g.setColor(Color.black);
      for(int rank = k; rank > 0; --rank)
	for(int line = 1; line <= lines; ++line){
	  if(number.mod(BigInteger.valueOf(2)).compareTo(BigInteger.valueOf(1)) == 0)
	    g.fillRect(rank * side, line * side, side, side);
	  number = number.divide(BigInteger.valueOf(2));
	}

      g.dispose();
      ImageIO.write(image, "png", outFile);
    }

    //<根据str,font的样式以及输出文件目录>
    public static BigInteger createImage(String str, int lines, Font font) throws Exception{

      //<获取font的样式应用在str上的整个矩形>
      Rectangle2D r=font.getStringBounds(str, new FontRenderContext(AffineTransform.getScaleInstance(1, 1),false,false));

      int unitHeight=(int)Math.floor(r.getHeight());//<获取单个字符的高度>

      //<获取整个str用了font样式的宽度这里用四舍五入后+1保证宽度绝对能容纳这个字符串作为图片的宽度>
      int width=(int)Math.round(r.getWidth())+1;
      int height=unitHeight;//<把单个字符的高度+3保证高度绝对能容纳字符串作为图片的高度>
      //<创建图片>
      BufferedImage image=new BufferedImage(width,height,BufferedImage.TYPE_INT_BGR);
      Graphics g = image.getGraphics();
      g.setColor(Color.WHITE);
      g.fillRect(0, 0, width, height);//<先用白色填充整张图片,也就是背景>
      g.setColor(Color.black);//<在换成黑色>
      g.setFont(font);//<设置画笔字体>
      g.drawString(str, 0, font.getSize());//<画出字符串>
      int ranks = width / (height / lines);

      //生成n值
      BigInteger ans = BigInteger.valueOf(0);
      BigInteger k = BigInteger.valueOf(1);
      for(int rank = ranks-1; rank >= 0; --rank){
	for(int line = 0; line < lines; ++line){
	  //判断一格应该选择黑色还是白色
	  int x = rank * width / ranks;
	  int y = line * height / lines;
	  int black = 0;
	  int white = 0;
	  for(int i = x; i < x + width / ranks && i < width; ++i)
	    for(int j = y; j < y + height / lines && j < height; ++j){
	      if(image.getRGB(i, j) < -1) black++;
	      else white++;
	    }
	  if(white <= black)
	    ans = ans.add(k);
	  k = k.multiply(BigInteger.valueOf(2));
	}
      }
      g.dispose();
      return ans.multiply(BigInteger.valueOf(lines));
    }
}

```

_注：代码中有“<>”注释的代码来自一个[帖子](http://bbs.csdn.net/topics/390594481)_

第一步获取像素字的方法可谓简单粗暴，一个格子了黑的多就全涂黑，白的多就全抹白。createImage()方法的效果其实并不是很好，只能依靠设置更大的行数(lines)来弥补。下面这张图的行数是30，n值已经比自指涉的那个n长出一倍：

<img src="{{ site.baseurl }}/assets/images/tupper5.png" />

translateImage()这个方法的效果不错，生成的原本的自指涉公式：

<img src="{{ site.baseurl }}/assets/images/tupper6.png" />

最后，按照Matrix67教的，我发现了一个函数

> lines = 30
> 
> n = 1700588472888268386616099055151107898449466865618167050285900960468542510070550603295531262008076593778655726752857952009189287582683506343855996761652624418921380642270818749133295248126528444626652177756015782801108909393833066453966737590118955916237832946837998688814125346884114535839563585674380557033157390711191161262252298646605632005244091016308467189777828038651948144748565265821649377396689499727131791596575029207578753839278182223578330856732779310321233622275648824557927333543050268932249064955918609670005302824643489254418012849456997594230366888919853450399152782431350010426750850595513305806612377128227982913907954345543984500933887768590182246191125121073030646399643723788666062583892353889726370650995025190770300323814544511187508718162523521106939052592139639700356810828408406908408950067508616713653777281938984193281039850266071213490536657412974701161341646115168498734446257226358803692953684933931059272202364422508820447005930030484806216628824934611848543276037919944519596448031976520837068552069481311886982420661403011028827675081062291800881001130125590179590077783746050556387977851206273823345924453346485792316092203264157490951650097510528229247315801862041466935134393189561023114338332186743559193935505120236089076289633627176402439067659646455323772498015308897717981379567143759631859179276049776386054228601049252580878243258003051965187006602458508067200388519473179363843054248002234697297485520223798829546734857559807574944906003497490572107490349592258607775938416702447137191007310422330506295553842736329708500411220438696879908250551501451342420823189042759257807711326709766940381266510710299294416590336659774336421802856185511488693102040421767571457197647012532788760025442876304138356071731864054370031053886185086327881223863046312261747909926443943457076157546429831417483270184775374849786359811656228219685111193045326387567729913087854789686917223172713638744798668060462670749973122791469818840345386092800880045237244587908556715296798447155224952540889516098223823912960
