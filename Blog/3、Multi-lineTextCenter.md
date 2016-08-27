#View多行文本居中<br>

<br>

## 自定义View系列

* View篇

	* [Android坐标系与View绘制流程](https://github.com/Idtk/Blog/blob/master/Blog/1%E3%80%81CoordinateAndProcess.md)
	* [Canvas与ValueAnimator](https://github.com/Idtk/Blog/blob/master/Blog/2%E3%80%81CanvasAndValueAnimator.md)
	* View多行文本居中
	* [Path图形与逻辑运算](https://github.com/Idtk/Blog/blob/master/Blog/4%E3%80%81PathFigureAndLogical.md)
	* [PieChart扇形图的实现](https://github.com/Idtk/Blog/blob/master/Blog/5%E3%80%81PieChart.md)
	* [Path中的贝塞尔曲线](https://github.com/Idtk/Blog/blob/master/Blog/6%E3%80%81Bezier.md)
	* [雷达图(蜘蛛网图)的实现](https://github.com/Idtk/Blog/blob/master/Blog/7%E3%80%81RadarChart.md)

* ViewGroup篇

	* [View的弹性滑动](https://github.com/Idtk/Blog/blob/master/Blog/8%E3%80%81Scroll.md)
	* [View的invalidate传递与绘制流程分析](https://github.com/Idtk/Blog/blob/master/Blog/9%E3%80%81Invalidate.md)
	* [View的requestLayout传递与测量、布局流程分析](https://github.com/Idtk/Blog/blob/master/Blog/10%E3%80%81RequestLayout.md)
	* [更简单的学习Android事件分发](https://github.com/Idtk/Blog/blob/master/Blog/11%E3%80%81TouchEvent.md)
	* [Android事件分发机制源码解析](https://github.com/Idtk/Blog/blob/master/Blog/12%E3%80%81TouchEventSource.md)


******

## 涉及知识<br>
**绘制过程**<br>

| 类别        | API           |描述  |
| ------------- |:-------------:|-----|
| 布局     | onMeasure  |  测量View与Child View的大小 |
|         | onLayout  |   确定Child View的位置|
|         | onSizeChanged  |   确定View的大小|
| 绘制     | onDraw  |   实际绘制View的内容|
| 事件处理     | onTouchEvent  |   处理屏幕触摸事件|
| 重绘     | invalidate  |   调用onDraw方法，重绘View中变化的部分|
<br>
**Canvas涉及方法**</br>

| 类别        | API           | 描述   |  
| ------------- |:-------------:| -----   |  
| 绘制图形      | drawPoint, drawPoints, drawLine, drawLines, drawRect, drawRoundRect, drawOval, drawCircle, drawArc | 依次为绘制点、直线、矩形、圆角矩形、椭圆、圆、扇形 |
| 绘制文本      | drawText, drawPosText, drawTextOnPath |    依次为绘制文字、指定每个字符位置绘制文字、根据路径绘制文字|
| 画布变换      | translate, scale, rotate, skew |   依次为平移、缩放、旋转、倾斜（错切） |
| 画布裁剪      | clipPath, clipRect, clipRegion |   依次为按路径、按矩形、按区域对画布进行裁剪 |
| 画布状态      | sava,restore |   保存当前画布矩阵，恢复之前保存的画布 |
</br>
**Paint涉及方法**</br>

| 类别        | API           | 描述  |
| ------------- |:-------------:| -----   | 
| 颜色      | setColor, setARGB, setAlpha | 依次为设置画笔颜色、透明度 |
| 类型      | setStyle |   填充(FILL),描边(STROKE),填充加描边(FILL_AND_STROKE) |
| 抗锯齿      | setAntiAlias |   画笔是否抗锯齿 |
| 字体大小      | setTextSize |   设置字体大小 |
| 字体测量      | getFontMetrics()，getFontMetricsInt() |   返回字体的各种测量值，返回值依次为float、int |
| 文字宽度测量      | measureText |   返回文字的宽度 |
| 文字对齐方式      | setTextAlign |   左对齐(LEFT),居中对齐(CENTER),右对齐(RIGHT) |
| 宽度      | setStrokeWidth |   设置画笔宽度 |
| 笔锋      | setStrokeCap |   默认(BUTT),半圆形(ROUND),方形(SQUARE) |
<br>

（**PS: 因API较多，只列出了涉及的方法，想了解更多，请查看[官方文档](http://developer.android.com/reference/packages.html)**)<br>

## 一、绘制文本

在Canvas中绘制文本，使用在[上一篇](http://www.idtkm.com/customview/customview2/)绘制的坐标系
### 1、drawText
drawText的几种方法如下:
```	Java
public void drawText (String text, float x, float y, Paint paint)
public void drawText (String text, int start, int end, float x, float y, Paint paint)
public void drawText (CharSequence text, int start, int end, float x, float y, Paint paint)
public void drawText (char[] text, int index, int count, float x, float y, Paint paint)
```
x、y值表示绘制文本左下角的坐标
```Java
Paint mPaint = new Paint();
mPaint.setAntiAlias(true);//抗锯齿
mPaint.setColor(Color.BLUE);//设置画笔颜色
mPaint.setTextSize(50);
String string = "Idtk";
canvas.drawText(string,0,0,mPaint);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/drawText1.png" alt="drawText1" title="drawText1"width="300"/>
<br>
或者只绘制其中的一部分字符<br>
start表示从第几个字符开始，end表示到第几个字符之前结束

```Java
canvas.drawText(string,1,4,0,0,mPaint);//截取第一到第四个字符
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/drawText2.png" alt="drawText2" title="drawText2"width="300"/>
<br>
可以使用CharSequence绘制

```Java
CharSequence charSequence = "charSequence";
canvas.drawText(charSequence,5,10,0,0,mPaint);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/drawText3.png" alt="drawText3" title="drawText3"width="300"/>

或者使用字符数组，index表示从第几个字符开始，count表示截取的数组长度

```Java
char[] chars = "chars".toCharArray();
canvas.drawText(chars,2,3,0,0,mPaint);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/drawText4.png" alt="drawText4" title="drawText4"width="300"/>

### 2、drawPosText

给文本中的每个字符都设定一个坐标，但是已经不推荐使用。

### 3、drawTextOnPath
根据path绘制文本，不过path是一个比较重要且有趣的东西，在之后绘制折线图或者曲线图之前的涉及知识中会进行分析。drawTextOnPath的几种方法:
```Java
public void drawTextOnPath (String text, Path path, float hOffset, float vOffset, Paint paint)
public void drawTextOnPath (char[] text, int index, int count, Path path, float hOffset, float vOffset, Paint paint)
```
这里简单写一个，path为一条从原点到(0,200)的直线，水平偏移量、垂直偏移量都设置为100，注意偏移量设置时不要过大，一面影响字符串的正常显示。
```	Java
Path path = new Path();
path.lineTo(0,200);
canvas.drawTextOnPath(string,path,100,100,mPaint);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/path.png" alt="drawTextOnPath" title="drawTextOnPath"width="300"/>

另一种方法，和上面drawText中使用字符数组的方法，同理。

## 二、文本居中

### 1、单行文本居中

#### a、getFontMetrics()，getFontMetricsInt()

getFontMetrics()，getFontMetricsInt()用于返回字符串的测量，而两个方法的区别就是返回值得类型。返回值一共有五个属性:

* Top : baseline到文本顶部的最大的距离
* Ascent : baseline到文本顶部的推荐距离
* Descent : baseline到文本底部的推荐距离
* Bottom : baseline到文本底部的最大距离
* Leading : 两行文本之间推荐的额外距离，一般为0

<img src="https://github.com/Idtk/Blog/blob/master/Image/String%20Center.png" alt="center" title="center"/>

<br>
在Android的坐标系之中，向下为Y轴正方向，向上位Y轴负方向，所以baseline之上的Top与Ascent都是负数，而baselin之下的Descent、Bottom都是正数。如果要让字符串在垂直方向上居中，则需要在纵坐标上增加Asecent绝对值与descent的差。
<br>
leading值是一行文本的底部与下一行文本的顶部之间的距离，图中就是line1的橙色线条与line2的紫色线条之间测距离，一般情况下是0。根据[stackoverflow](http://stackoverflow.com/questions/27631736)中的说法，是一行的bottom与下一行的top已经为字符串提供了足够的空间，所以leading的值在一般情况下都为0。需要注意的是，在获取FontMetrics之前请完成Paint的设置，这样才能准确的获取测量值。

```Java
mPaint.setTextSize(50);
String string = "Idtk";
Paint.FontMetrics fontMetrics= mPaint.getFontMetrics();
float textHeight = (-fontMetrics.ascent-fontMetrics.descent)/2;
canvas.drawText(string,0,textHeight,mPaint);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/center1.png" alt="center" title="center" width="300"/>

#### b、setTextAlign
setTextAlign可以设置画笔绘制文本的对齐方式，选择center即可完成文本的水平居中。
```Java
mPaint.setTextAlign(Paint.Align.CENTER);
```

<img src="https://github.com/Idtk/Blog/blob/master/Image/center2.png" alt="center" title="center" width="300"/>

#### c、measureText
measureText可以测量文本的宽度，即横向长度，这样也可以来完成居中设置，但在这之前，需要恢复文本对齐方式至默认设置(居左)。
```Java
float textWidth = mPaint.measureText(string);
canvas.drawText(string,-textWidth/2,textHeight,mPaint);
```

<img src="https://github.com/Idtk/Blog/blob/master/Image/center3.png" alt="center" title="center" width="300"/>
<br>
我们可以看出结果与之前相同。

### 2、多行文本居中

这是整片博客来说最有趣的地方，我们如何让一个多行字符串居中呢？
<br>
假设有设定好的字符串数组，画笔，画布，坐标点
<br>
* 1、drawText每次只能绘制一行，所以我们有个for循环
* 2、更具上图，假设字符串数组大于1，每个字符串的高度应该为-top+bottom，总高度就为length*(-top+bottom);
* 3、偏移量就等于length*(-top+bottom)/2-bottom;
* 4、如果顺序向上(即Y负方向)排列的话，那么第i个字符串的高度就是-(length-i-1)*(-top+bottom);
* 5、每个字符串高度的减去偏移量，就应该是每个字符串的baseline的Y坐标
<br>
详细代码如下:
```Java
private void textCenter(String[] strings, Paint paint, Canvas canvas, Point point,Paint.Align align){
    mPaint.setTextAlign(align);
    Paint.FontMetrics fontMetrics= mPaint.getFontMetrics();
    float top = fontMetrics.top;
    float bottom = fontMetrics.bottom;
    int length = strings.length;
    float total = (length-1)*(-top+bottom)+(-fontMetrics.ascent+fontMetrics.descent);
    float offset = total/2-bottom;
    for (int i=0; i<length; i++){
        float yAxis = -(length-i-1)*(-top+bottom)+offset;
        canvas.drawText(strings[i],point.x,point.y+yAxis,mPaint);
    }
}
```
偏移量取值的变化，是因为在字符串的首尾两个字符串，需要把top改成ascent，bottom改成descent。通过设置画笔来完成横向的居中，居左，居右。
<br>
使用代码:
```Java
mPaint.setTextSize(50);
mPaint.setColor(Color.BLUE);
String[] strings = new String[]{"Idtk","是","一","个","小","学","生"
Point point = new Point(0,0);
textCenter(strings,mPaint,canvas,point,Paint.Align.CENTER);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/Multi-line.png" alt="Multi-line" title="Multi-line" width="300"/>

### 3、多列文本居右
这个是模仿古文的书写方式<br>
* 1、一个字符串数组，分割正多个字符数组
* 2、每个字符数组中，获取最宽的字符宽度，为字符数组的字符宽度
* 3、计算数组x坐标，再调用之前的文本居中代码
<br>
我们先重载一个textcenter方法:
```Java
private void textCenter(char[] chars, Paint paint, Canvas canvas, Point point,Paint.Align align){
    mPaint.setTextAlign(align);
    Paint.FontMetrics fontMetrics= mPaint.getFontMetrics();
    float top = fontMetrics.top;
    float bottom = fontMetrics.bottom;
    int length = chars.length;
    float total = (length-1)*(-top+bottom)+(-fontMetrics.ascent+fontMetrics.descent);
    float offset = total/2-bottom;
    for (int i=0; i<length; i++){
        float yAxis = -(length-i-1)*(-top+bottom)+offset;
        canvas.drawText(chars[i]+"",point.x,point.y+yAxis,mPaint);
    }
}
```
接下来编写多列居左的代码:
```Java
private void rowTextRigth(String[] strings, Paint paint, Canvas canvas, Point point){
    mPaint.setTextAlign(Paint.Align.RIGHT);
    int length = strings.length;
    float len,newLen;
    float total = 0;
    for (int j=0; j<length; j++){
        char[] chars=strings[j].toCharArray();
        len = mPaint.measureText(chars[0]+"");
        for (int i=1; i<chars.length; i++){
            newLen = mPaint.measureText(chars[i]+"");
            len = Math.max(newLen,len);
        }
        point.x = point.x - (int) total;
        textCenter(chars,mPaint,canvas,point,Paint.Align.RIGHT);
        point.x = point.x + (int) total;
        total = total+len;
    }
}
```
使用方法示例:
```Java
String[] strings2 = new String[]{"床前明月光，","疑是地上霜。","举头望明月，","低头思故乡。"};
rowTextRigth(strings2,mPaint,canvas,point);
```
<img src="https://github.com/Idtk/Blog/blob/master/Image/row.png" alt="Multi-line" title="Multi-line" width="300"/>

### 4、文本自动换行居中
系统中有一个StaticLayout方法，可以在设置宽度，当前行文本超过此宽度后，进行自动换行，提供ALIGN_CENTER(居中)、ALIGN_NORMAL(标准)、ALIGN_OPPOSITE(与标准相反)三种对齐方式。<br>
可以简单的封装成一个方法:
``` Java
private void textCenter(String string, TextPaint textPaint, Canvas canvas, Point point, int width,
                        Layout.Alignment align, float spacingmult, float spacingadd, boolean includepad){
    StaticLayout staticLayout = new StaticLayout(string,textPaint,width, align,spacingmult,spacingadd,includepad);
    canvas.save();
    canvas.translate(-staticLayout.getWidth()/2+point.x,-staticLayout.getHeight()/2+point.y);
    staticLayout.draw(canvas);
    canvas.restore();
}
```
使用方法,这里设置宽度为150:
```Java
String mString = "Idtk是一个小学生";
TextPaint tp = new TextPaint();
tp.setColor(Color.BLUE);
tp.setStyle(Paint.Style.FILL);
tp.setTextSize(50);
Point point = new Point(0,0);
textCenter(mString,tp,canvas,point,150,Layout.Alignment.ALIGN_CENTER,1.5f,0,false);
```

<img src="https://github.com/Idtk/Blog/blob/master/Image/Multi-line2.png" alt="Multi-line" title="Multi-line" width="300"/>

## 三、小结
本文介绍了如何在canvas中绘制文字，以及单行、多行文字居中，多列文本居右。在下一篇文章将会迎来一个阶段性的目标，进行[PieChart](https://github.com/Idtk/CustomView/blob/master/gif/PieChart.gif)的绘制。如果在阅读过程中，有任何疑问与问题，欢迎与我联系。<br>
**博客:www.idtkm.com**<br>
**GitHub:https://github.com/Idtk**<br>
**微博:http://weibo.com/Idtk**<br>
**邮箱:IdtkMa@gmail.com**<br>
