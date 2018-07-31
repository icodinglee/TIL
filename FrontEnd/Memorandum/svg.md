SVG精髓

本书所有完整示例代码 []()
[原书示例代码](https://github.com/oreillymedia/svg-essentials-examples)


[SVG DOM 接口方法](https://github.com/xswei/SVG_Essentials/tree/master/SVG_DOM)

[SVG & XML](https://github.com/xswei/SVG_Essentials/tree/master/SVGandXML)

[SVG高级](https://github.com/xswei/SVG_Essentials/tree/master/advanced)

[SVG-foreignObjectElement](https://developer.mozilla.org/zh-CN/docs/Web/API/SVGForeignObjectElement)

## 第一章 入门指南
### 1. 图形系统
计算机中描述图形信息的两大图形系统：栅格图形和矢量图形。栅格图形中图形被表示为图片元素或者像素的长方形数组。矢量图形中图形被描述为一系列几何形状，通过矢量图形阅读器在指定的坐标集上绘制形状。
### 2. SVG(Scalable Vector Graphics)
SVG是一种XML应用，用来表示矢量图形。所有的图形有关信息被存储为纯文本，具有XML的开放性、可移植性和可交互性。当前稳定的XML和SVG版本都为1.1

SVG文档结构是标准的XML文档，根元素svg定义图形的大小，根元素中包含各种的形状元素。SVG允许使用单独的属性指定元素的样式。

SVG使用g元素对图形进行分组，使用use元素实现元素的复用。

## 第二章 在网页中使用SVG
### 1. 将SVG作为图像
将svg作为图像包含在HTML标记的img元素内，但是这样有一定的局限性：**SVG转为栅格图像时与主页面分离，并且无法在两者之间通信(SVG渲染过程与主页面独立)。主页面上的样式对SVG无效，运行在主页面上的脚本无法感知或者修改SVG文档结构。**

在CSS中包含SVG，最常用的是background-image属性，应该避免SVG元素文件太大。

### 2. 将SVG作为应用程序
使用object元素将SVG嵌入HTML文档中，object元素的type属性表示要嵌入的文档类型，对用SVG应该是type="image/svg+xml"。object元素必须有起始标签和结束标签，这两个标签之间的内容为对象数据本身不能被渲染时显示。

## 第三章 坐标系统
### 1. 视口
视口是指文档打算使用的画布区域。在svg元素上使用width和height属性确定视口的大小，属性值可以仅仅是为数字也可以为带单位的数字(单位可以为em、ex、px、pt、pc、cm、mm和in)也可以为百分比。
### 2. 默认用户坐标
SVG阅读器会设置一个坐标系统，即原点(0,0)位于视口的左上角，x向右递增，y向下递增。这个坐标系统是一个纯粹的几何系统，点没有大小，网格线被认为是无限细。

在SVG中指定单位并不会影响其他元素中给定单位的坐标，也就是说SVG文档中各个元素的单位可以不统一。

### 3. 指定用户坐标
摒弃阅读器设置的默认用户坐标，可以自己为视口设置一个用户坐标。通过在svg元素上设置viewBox属性。

viewBox属性由4个数值组成，分别代表要叠加在视口上的最小x、最小y，宽度、高度。

既然可以对svg自定义用户坐标，那么肯定要解决SVG视口长宽比例和viewBox定义的长宽比例不同的问题以及如何对齐问题。这个时候就需要preserveAspectRatio属性了。

如果viewBox的长宽比例与视口的长宽比例不同，那么SVG可以有以下三种选择：

a. 按较小的尺寸等比例缩放图形，使图形完全填充视口

b. 按较大的尺寸等比例缩放图形，病裁减掉超出视口的部分

c. 拉伸和压缩绘图以使其恰好填充视口

preserveAspectRatio属性允许指定被缩放的图形相对视口的对齐方式,格式为preserveAspectRatio = "alignment[meet|slice]"   

默认值为"xMidYMid meet"

·***alignment***指定轴和位置，x和y方向都有min,mid,max三种方式，分别表示x和y方向的对齐方式，对齐方式由x和y组合指定，共9中方式，也就是alignment共有如下9个取值：


y\x | xMin | xMid | xMax
---|---|---|---
**yMin** | xMinYMin | xMidYMin | xMaxYMin
**yMid** | xMinYMid | xMidYMid | xMaxYMid
**yMax** | xMinYMax | xMidYMax | xMaxYMax

·***meet***说明符在图形超出视口时候会对图形适当缩小调整适配可用的空间

·***slice***说明符直接裁剪超出视口的部分

除了上述操作之外，还可以指定**preserveAspectRatio="none"**，用于在viewBox和视口宽高比不同时缩放图像，此时图像不会被等比例缩放，会被拉伸、挤压、变形。

### 4. 嵌套坐标系统

可以将另一个svg元素插入到文档中来建立一个新的视口和坐标系统，也就是说svg中可以嵌套另一个svg，每个svg都有自己独立的视口和坐标系统。

## 第四章 基本形状

### 1. 线段
line元素，使用x1,y1,x2,y2属性指定线段的起止点坐标。有如下特性:


特性 | 说明
---|---
stroke-width | 笔画宽度，坐标网格线位于笔画的正中间，可以使用css的shape-rendering值来控制反锯齿特性
stroke | 笔画颜色
stroke-opacity | 线条的不透明度
stroke-dasharray | 虚线，由一系列数字组成，数字个数为偶数(负责会自动重复一遍使其为偶数),表示线长-间隙-线长-间隙...

### 2. 矩形

rect元素，使用x,y,width,height表示一个矩形


特性 | 说明
---|---
fill | 填充颜色
fill-opacity | 填充不透明度
stroke | 边框颜色
stroke-width | 边框宽度，边框是骑在矩形边界上的，一半在矩形外，一半在矩形内 
rx/ry | 圆角矩形，最大值为矩形宽/高的一半，如果只指定了一个，则认为两个都为相同的值

### 3. 圆和椭圆

circle元素表示圆,由cx,cy,r属性界定
ellipse元素表示椭圆,由cx,cy,rx,ry界定

特性 | 说明
---|---
fill | 填充颜色
fill-opacity | 填充不透明度
stroke | 边框颜色
stroke-width | 边框宽度，边框是骑在圆的边界上的，一半在圆/椭圆外，一半在圆/椭圆内 

### 4. 多边形

polygon元素指定一个多边形,由points属性指定的一系列坐标点界定，会自动封闭

特性 | 说明
---|---
fill | 填充颜色
fill-opacity | 填充不透明度
stroke | 边框颜色
stroke-width | 边框宽度
fill-rule | 填充规则，如果多边形的边有交叉时，需要指定，可以取mozero(默认)和evenodd两个值。

fill-rule值为nonzero时的原理:判断一个点是在多边形内部还是外部时，从这个点画一条到无穷远的射线，然后数这个线和多边形的边有多少次交叉。如果交叉的边线是从右往左画，则总数加1，如果是从左往右则总数减1.如果最后总数为0则认为改点在图形外部，否则在内部。

fill-rule值为evenodd时只数射线与多边形边的交叉次数，如果为奇数则认为在多边形内部，否则认为在多边形外部。

### 5. 折线

polyline元素表示一个折线，使用points属性指定一系列点，不自动封闭图形。

### 6. 特性总结


特性 | 说明
---|---
stroke | 笔画颜色
stroke-width | 笔画宽度
stroke-opacity | 笔画不透明度
stroke-dasharray | 虚线笔画 
stroke-linecap | 笔画头的形状  butt(默认),round,square
stroke-linejoin | 图形棱角，有miter(默认),round和bevel三个取值
stroke-miterlimit | 相交处显示宽度与线宽的最大比例，默认为4
fill | 填充颜色 默认black
fill-opacity | 填充不透明度
fill-rule | 填充规则

## 第五章 文档结构

### 1. 结构和表现

SVG允许文档表现和文档结构分离，SVG支持四种方式指定表现信息：内联样式、内部样式表、外部样式表以及表现属性


表现方式 | 说明
---|---
内联样式 | 元素内部使用style属性
内部样式表 | 内部样式定义在defs元素内部
外部样式表 | 与html类似，将样式定义在css文件中，使用选择器来设置相应的元素样式
表现属性 | SVG允许以属性的形式指定表现样式，但是**表现属性的优先级最低**，如果以其他三种形式指定了相同的样式属性，则将覆盖通过表现属性指定的样式

内部样式表示例：

```
<svg width="200px" height="200px" xmlns="http://www.w3.org/2000/svg>
    <defs>
        <style type="text/css"><![CDATA[
            circle{
                fill:#ccc
            }
        ]]></style>
    </defs>
    <circle cx="10" cy="10" r="5"/>
</svg>
```

### 2. 分组和引用

g元素用来将其子元素作为一个组合，可以使文档结构更清晰。除此之外，在g标签中指定的所有样式会应用于组合内的所有子元素，可以不用在所有子元素上指定属性。

use元素用来复用图形中重复出现的元素，需要为use标签的xlink:href指定URI来引用指定的图形元素。同时还要指定x和y属性以表示组合应该移动到哪个位置。use元素并不限制只能使用同一个文件内的对象，xlink:href属性可以指定任何有效的文件或URI。

defs元素用来定义复用的元素，但是定义在defs内的元素并不会被显示，而是作为模板供其他地方使用。

symbol元素与g元素不同，symbol永远不会被显示，也可以用来指定被后续使用的元素，symbol元素可以指定viewBox和preserveAspectRatio属性。在引用时通过为use元素指定width和height属性就可以让symbol元素适配视口大小。

image可以用来包含一个完整的SVG或栅格文件。如果包含一个SVG文件，则视口会基于引用的文件的x,y,width,height属性来建立。如果包含栅格文件则会被缩放以适配该属性指定的矩形。SVG规范要求SVG阅读器支持JPEG和PNG两种栅格文件。

## 第六章 坐标系统变换

### 1. translate变换

translate变换用来对用户坐标进行平移，通过制定transform属性值来设置:transform = "translate(x,y)"。

translate工作原理:首先获取整个网络，然后将其移动到画布的新位置而不是移动所在的元素，也就是说移动的是整个坐标系统而不是元素本身。看似比移动元素复杂，其实在使用其他一系列变换时，这种移动整个坐标系的方法从数学和概念上讲，更方便。

### 2. scale变换

缩放坐标系统。transform = "scale(value)"或者transform="scale(x-value,y-value)"。

仅仅使用scale(n)变换时，网格系统的原点位置并没有变化，只是每个用户坐标都变成了原来的n倍，也就是网格变大了，因此线也会变粗(用户单位并没有变)。

*技巧：如果从其他系统传输数据到SVG，则可能必须处理使用笛卡尔坐标表示的矢量图形，在笛卡尔坐标系统中，原点位于左下角，y向上递增，x向右递增。而SVG坐标原点位于左上角，此时使用scale(1,-1)就可以完成两者之间的转换。*

**缩放变换永远不会改变图形对象的网格坐标或者笔画宽度，仅仅改变对应画布上的坐标系统网格的大小。**

### 3. rotate变换

根据指定的角度旋转坐标系统，默认的坐标系统中，角度的测量顺时针增加，0度为3点钟方向。

注意，除非另行指定，否则旋转以原点为中心。 此时可以通过平移+旋转的方式来指定旋转中心：
translate(centerX,centerY) rotate(angle) translate(-centerX,-centerY)

但是有个更简单的方式：rotate(angle,centerX,centerY)

### 5. 围绕中心点缩放

上面提到，缩放默认是以原点为基准的，这显然不能满足需求，那么可以通过如下方式指定缩放中心：

translate(-centerX*(factor-1),-centerY*(factor-1)) scale(factor)


### 6. skewX和skewY变换

这两个变换用来倾斜某个轴，一般形式为skewX(angle),skewY(angle)。这样的结果就是使得x轴和y轴不再垂直。

### 7. 矩阵变换

计算机图形学中坐标变换都通过矩阵来实现，除上述变换方法之外，还可以直接为变换指定变换矩阵，变换矩阵为matrix(a,b,c,d,e,f)，此时指定的变换矩阵为:

```
a  c  e
b  d  f
0  0  1

```
## 第七章 路径

### 1. path命令
SVG中所有基本形状都是path的简写形式，但是建议使用简写形式，因为这样可以使SVG文档更可读。

path元素更通用，可以通过制定一系列相互连接的线、弧、曲线来绘制任意形状的轮廓，这些轮廓也可以填充或者绘制轮廓线，也可以用来定义裁剪区域或蒙版。

下表为path命令总结，其中大写表示绝对坐标，小写表示相对坐标：


命令 | 参数 | 说明
---|---|---
M m | x y | 移动画笔到制定坐标
L l | x y | 绘制一条到给定坐标的线
H h | x  | 绘制一条到给定x坐标的横线
V v |  y | 绘制一条到给定y坐标的垂线
A a | rx ry x-axis-rotation large-arc sweep x y  | 圆弧曲线命令有7个参数，依次表示x方向半径、y方向半径、旋转角度、大圆标识、顺逆时针标识、目标点x、目标点y。大圆标识和顺逆时针以0和1表示。0表示小圆、逆时针
Q q | x1 y1 x y | 绘制一条从当前点到x,y控制点为x1,y1的二次贝塞尔曲线
T t | x y | 绘制一条从当前点到x,y的光滑二次贝塞尔曲线，控制点为前一个Q命令的控制点的中心对称点，如果没有前一条则已当前点为控制点。
C c | x1 y1 x2 y2 x y | 绘制一条从当前点到x,y控制点为x1,y1 x2,y2的三次贝塞尔曲线
S s | x2 y2 x y | 绘制一条从当前点到x,y的光滑三次贝塞尔曲线。第一个控制点为前一个C命令的第二个控制点的中心对称点，如果没有前一条曲线，则第一个控制点为当前的点。

路径的填充同样可以使用fill-rule属性指定填充规则，如果需要填充一个中空的形状，则只需要注意外侧路径顺逆时针方向和内侧空心区域顺逆时针方向即可。


### 2. marker元素

marker元素用来在path上添加一个标记，比如箭头之类的。

首先需要定义好marker元素，然后在path中引用，一个marker标记是一个独立的图形，有自己的私有坐标。


```
<defs>
    <marker id="marker" markerWidth="10" markerHeight="10" refX="0" refY="4" orient="auto">
        <path d="M 0 0 4 4 0 8" style="fill:none;stroke:black;"/>
    </marker>
</defs>

<path d="M 10 20 100 20 A 20 30 0 0 1 120 50 L 120 110"
    style="marker-start:url(#marker);marker-mid:url(#marker);marker-end:url(#marker);fill:none;stroke:black;"/>
```


marker属性 | 说明
---|---
markerWidth | marker标记的宽度
markerHeight | marker标记的高度
refX refY | 指定marker中的哪个坐标与路径的开始坐标对齐
orient | 自动旋转匹配路径的方向，需要设置为auto
markerUnits | 这个属性决定标记的坐标系统是否需要根据path的笔画宽度调整，如果设置为strokeWidth，则标记会自动调整大小。如果设置为useSpaceOnUse，则不会自动调整标记的大小。
viewBox preserveAspectRatio | 设置标记的显示效果，比如可以将标记的(0,0)设置在标记网格中心

path中使用marker-start,marker-mid,marker-end,marker属性来设置标记的位置。如果使用marker属性，则表示同时设置marker-start,marker-mid,marker-end三个属性。

除在path中以属性的形式设置标记外，还可以在css中设置：


```
path{
    marker:url(#marker_id);
}
```

但是此时要注意，如果id为marker_id的标记中也有path元素，则会出现自身无限引用自身的情况，因此需要说明marker中的path元素不需要添加标记：


```
path{
    marker:url(#marker_id);
}
marker#marker_id path{ //marker下的id为marker的元素下的path元素不需要marker标记
    marker:none;
}
```

## 第八章 图案和渐变

填充图形或笔画除了使用fill,stroke纯色之外，还可以使用图案和渐变填充。

### 1. 图案

使用图案填充图形，首先要定义一个水平或垂直方向的重复的图案对象，然后用它填充另一个对象或者作为笔画使用。这个图形对象呗称为"tile"(瓷砖)。

图案对象使用pattern元素定义，pattern元素内部包裹了图案的path元素。定义好之后下一个需要解决的问题是如何排列图案，那就需要使用patternUnits属性.

##### patternUnits = objectBoundingBox

如果希望图案的大小基于要填充对象的大小计算，则需要设置patternUnits属性为objectBoundingBox(0到1之间的小数或百分比)，并需要指定图案左上角的x和y坐标。


```
<defs>
	<pattern id="tile" x="0" y="0" width="20%" height="20%" patternUnits="objectBoundingBox">
    	    <path d="M 0 0 Q 5 20 10 10 T 20 20" style="stroke:black;fill:none"></path>
	    <path d="M 0 0 h 20 v 20 h -20 z" style="stroke:black;fill:none"></path>
	</pattern>
</defs>
<path d="M 0 0 Q 5 20 10 10 T 20 20" style="stroke:black;fill:none"></path>
<path d="M 0 0 h 20 v 20 h -20 z" style="stroke:black;fill:none"></path>
<rect x="40" y="0" width="100" height="100" style="fill:url(#tile);stroke:black"></rect>	
<rect x="155" y="0" width="70" height="80" style="fill:url(#tile);stroke:black"></rect>
<rect x="250" y="0" width="150" height="130" style="fill:url(#tile);stroke:black"></rect>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.1.jpg)

在上图中，第一个正方形宽高都为100，百分之20刚好为图案的尺寸，因此恰好平铺。在第二个图中，正方形的宽高分别为70和80，百分之20小于20，因此图案会被截断。在第三个正方形中，宽高的百分之20大于图案的尺寸，因此图案平铺时会出现间隙。

##### patternUnits = userSpaceOnUse

除了基于被填充对象尺寸方式之外，还可以按用户单位制定图案的width和height。此时要设置patternUnits值为userSpaceOnUse。

```
<defs>
	<pattern id="tile" x="0" y="0" width="20" height="20" patternUnits="userSpaceOnUse">
    	    <path d="M 0 0 Q 5 20 10 10 T 20 20" style="stroke:black;fill:none"></path>
	    <path d="M 0 0 h 20 v 20 h -20 z" style="stroke:black;fill:none"></path>
	</pattern>
</defs>
<path d="M 0 0 Q 5 20 10 10 T 20 20" style="stroke:black;fill:none"></path>
<path d="M 0 0 h 20 v 20 h -20 z" style="stroke:black;fill:none"></path>
<rect x="40" y="0" width="100" height="100" style="fill:url(#tile);stroke:black"></rect>	
<rect x="155" y="0" width="70" height="80" style="fill:url(#tile);stroke:black"></rect>
<rect x="250" y="0" width="150" height="130" style="fill:url(#tile);stroke:black"></rect>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.2.jpg)

此时明确制定了图案的宽高，并且x和y表示图案与所在的坐标系对齐(0,0点)。在第一个正方形中，由于正方形的x和y为20的整数倍，所以不会出现裁剪情况，第二，三个正方形的x坐标不是20的整数倍，所会出现裁剪。


##### patternContentUnits属性

在上面两个例子中，pattern元素内的path元素的坐标都是用户坐标，如果想基于被填充图形设置，则需要设置patternContentUnits属性。patternContentUnits属性默认为userSpaceOnUse,当设置patternContentUnits属性为objectBoundingBox时就可以使用百分比来设置图案的大小。

```
<defs>
	<pattern id="tile" x="0" y="0" width=".2" height=".2" patternUnits="objectBoundingBox" patternContentUnits="objectBoundingBox">
		<path d="M 0 0 Q .05 .2 .1 .1 T .2 .2" style="stroke:black;fill:none;stroke-width:0.01;"></path>
		<path d="M 0 0 h 0.2 v 0.2 h -0.2z" style="stroke:black;stroke-width:0.01;fill:none"></path>
	</pattern>
</defs>
<g transform="translate(20,20)">
	<rect x="0" y="0" width="100" height="100" style="fill:url(#tile);stroke:black"></rect>	
</g>
<g transform="translate(135,20)">
	<rect x="0" y="0" width="150" height="100" style="fill:url(#tile);stroke:black"></rect>	
</g>
<g transform="translate(300,20)">
    <rect x="0" y="0" width="80" height="150" style="fill:url(#tile);stroke:black"></rect>	
</g>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.3.jpg)

当设置patternContentUnits属性为bojectBoundingBox时，图案的大小会根据百分比调整，主要就可以调整图案的宽高比，填满被填充图形。

此外图案也可以由另一个图案填充，这就是图案的嵌套。

### 2. 线性渐变
线性渐变是一系列颜色沿着一条直线过渡，在特定的位置指定想要的颜色，被称为渐变点。渐变点是渐变结构的一部分，颜色是表现的一部分。

线性渐变使用linearGradient元素表示：


```
<defs>
	<linearGradient id="linear">
		<stop offset="0%" style="stop-color:#ffcc00;"></stop>
		<stop offset="100%" style="stop-color:#0099cc;"></stop>
	</linearGradient>
</defs>
	<rect x="20" y="20" width="200" height="100" style="fill:url(#linear);stroke:black;"></rect>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.4.jpg)

stop元素有两个必要属性：offset和stop-color。offset属性用来指定在哪个点的颜色应该等于stop-color。offset的取值范围0%-100%。

stop元素的属性：


属性 | 说明
---|---
offset | 必需，取值范围0%-100%
stop-color | 必需，对应offset位置点的颜色
stop-opacity | 对应offset位置点的不透明度

linearGradient元素属性：


属性 | 说明
---|---
x1,y1 | 渐变的起点位置，使用百分比表示，默认的渐变方向是从左到右
x2,y2 | 渐变的终点位置，使用百分比表示
spreadMethod | 如果设置的offset不能覆盖整个对象，该怎么填充。pad:起点或终点颜色会扩展到对象边缘。repeat:渐变重复起点到终点的过程。reflect:渐变按终点-起点-终点的排列重复。

### 3. 径向渐变

径向渐变的每个渐变点是一个圆形路径，从中心点向外扩散。设置方式与线性渐变大致相同。如果填充对象边界框不是正方形的，则过渡路径会变成椭圆来匹配边界框的长宽比。


```
<defs>
	<radialGradient id="radial" cx="50%" cy="50%" >
		<stop offset="0%" style="stop-color:#f00;"></stop>
		<stop offset="50%" style="stop-color:#0f0;"></stop>
		<stop offset="100%" style="stop-color:#00f;"></stop>
	</radialGradient>
</defs>
<rect x="20" y="20" width="200" height="200" style="fill:url(#radial);stroke:black;"></rect>	
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.5.jpg)

radialGradient元素属性：

属性 | 说明
---|---
cx,cy,r | 定义渐变的范围，测量半径的单位是对象的宽高均值，而不是对角线，默认都为50%
fx,fy | 0%点所处的圆路径的圆心，默认和cx,cy一样
spreadMethod | pad,repeat,reflect三个值，用来解决绘制范围没有到达图形边缘的情况。

## 第九章 文本

### 1. 相关术语

术语 | 说明
---|---
字符 | XML中，字符是指带有一个数字值得一个或多个字节，数字值与Unidode标准对应
符号 | 字符的视觉呈现。每个字符可以有多种视觉呈现
字体 | 代表某个字符集合的一组符号
基线 | 字体中所有符号以基线对齐
上坡度 | 基线到字体中最高字符的顶部距离
下坡度 | 基线到最深字符底部的距离
大写字母高度、x高度 | 大写字母高度是指基线上大写字母的高度，x高度是基线到小写字母x顶部的高度

### 2. text元素的基本属性

text元素以指定的x和y值作为元素内容第一个字符的基线位置，默认样式黑色填充、没有轮廓。

属性 | 说明
---|---
font-family | 以空格分割的一系列字体名称或通用字体名称
font-size | 如果有多行文本，则font-size为平行的两条基线的距离
font-weight | 两个值：bold(粗体)和nromal(默认) 
font-style | 常用的两个值:italic(斜体)和normal
text-decoration | 可能的值:none,underline(下划线),overline(上划线),line-through(删除线)
word-spacing | 单词之间的距离
letter-spacing | 字母之间的间距
text-anchor | 对齐方式：start,middle,end
textLength | 设置文本的长度
lengthAdjust | 在指定了textLength时，可以通过lengthAdjust属性设置字符的调整方式，值为 spacing(默认)时,只调整字符的间距。当值为spacingAndGlyphs时，同时调整字符间距和字符本身的大小

### 3. tspan元素

text元素无法对文本进行换行操作，如果需要分行显示文本，则需要使用tspan元素。tspan元素与html的span元素类似，可以嵌套在文本内容中，并可以单独改变其内部文本内容的样式。

tspan元素除大小，颜色等表现样式之外，还可以设置以下属性：


属性 | 说明
---|---
dx,dy | x和y方向的偏移
x,y | 对tspan进行绝对定位
rotate | 旋转字符，可以同时设置多个值，这些值会依次作用在tspan包裹的字母上
baseline-shift | 与dy属性设置上下标相比，这个属性更方便，当为super时，会上标。sub时为下标。仅仅在所在的tspan内有效

### 4. 纵向文本

文本一般从左到右排列，如果需要上下排列，则需要使用writing-mode属性。

设置writing-mode属性值为tb(top to bottom)，可以将文本上下排列。

### 5. 文本路径

如果要使得文本沿着某条路径排列，则需要使用textPath元素。需要将文本放在textPath元素内部，然后使用textPath元素的xlink:href属性引用一个定义好的path元素。


```
<defs>
	<path id="path" d="M30 40 C 50 10 ,70 10,120 40 S150 0,200 40" style="fill:none;stroke:black"></path>
</defs>
<g transform="translate(10,50)">
	<path id="path" d="M30 40 C 50 10 ,70 10,120 40 S150 0,200 40" style="fill:none;stroke:black"></path>
	<text>
		<textPath xlink:href="#path">
			hello world
		</textPath>
	</text>
</g>
<g transform="translate(10,100)">
	<path id="path" d="M30 40 C 50 10 ,70 10,120 40 S150 0,200 40" style="fill:none;stroke:black"></path>
	<text>
		<textPath xlink:href="#path" startOffset="50%" text-anchor="middle">
			hello world
		</textPath>
	</text>
</g>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/8.6.jpg)

startOffset属性用来指定文本的起点，当设置为50%，并且设置text-anchor为middle时，文本会被定为在path的中间。


## 第十章 裁剪和蒙版

### 1. 裁剪路径
在创建SVG文档时，可以通过指定感兴趣的区域的宽度和高度建立视口，这个视口就会变成默认的裁剪区域，裁剪区域外的任何部分都不会被显示。裁剪区域可以通过clipPath元素建立自己的裁剪区域。

最简单的是建立一个矩形裁剪区域。


```
<defs>
	<clipPath id="rectClip">
		<rect x="100" y="100" width="100" height="90" fill="none" stroke="black"></rect>
		<circle cx="300" cy="150" r="60" fill="none"></circle>
		<path d="M100 50L150,50L100,20L40,60Z" fill="none"></path>
	</clipPath>
</defs>
<image x="0" y="0" width="480" height="270" xlink:href="./10.0.jpg" style="clip-path:url(#rectClip);"></image>
<image x="0" y="0" width="480" height="270" xlink:href="./10.0.jpg" opacity="0.2"></image>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/10.1.jpg)  

在上图中裁剪区域由一个矩形、path路径和一个圆形组成，为了方便对比观察，又添加了一个透明度为0.2的背景图。

裁剪路径也可以指定坐标系，在上面的示例中使用的是用户坐标系，也就是裁剪路径中的坐标值都是基于原点的。也可以根据对象的边界来指定，需要设置clipPathUnits属性为objectBoundingBox(默认值为userSpaceOnUse)。

将上述示例clipPathUnits属性设置为objectBoundingBox：


```
<defs>
	<clipPath id="rectClip" clipPathUnits="objectBoundingBox">
		<rect x=".30" y=".300" width=".200" height=".40" fill="none" stroke="black"></rect>
		<circle cx=".300" cy=".150" r=".090" fill="none"></circle>
    </clipPath>
</defs>
		
<image x="0" y="0" width="480" height="270" xlink:href="./10.0.jpg" style="clip-path:url(#rectClip);"></image>
<image x="0" y="0" width="480" height="270" xlink:href="./10.0.jpg" opacity="0.2"></image>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/10.2.jpg)  

可以注意到clipPath元素中的坐标都根据被裁剪对象的尺寸百分比设置。

### 2. 蒙版

SVG的蒙版会变换对象的透明度，如果蒙版是不透明的，则被蒙版覆盖的对象的像素就是不透明的，如果蒙版是半透明的，则对象就是半透明的。

使用mask元素创建蒙版，使用x,y,width,height指定蒙版的尺寸，这些尺寸默认按照objectBoungdingBox计算，如果想根据用户空间坐标计算，则需要设置mask元素的maskUnits值为userSpaceOnUse。

mask之间是想要用作蒙版的任意基本形状、文本图像或路径。这些元素默认坐标使用用户坐标空间表达，如果想要这些元素使用对象的边界框，则设置maskContenUnits属性为objectBoungdingBox即可。

注意与maskUnits属性的区别，maskUnits针对mask元素，maskContenUnits属性针对mask元素内的元素。

```
<defs>
	<mask id="mask" x="0" y="0" width="1" height="1" maskContentUnits="objectBoundingBox">
		<circle cx=".500" cy=".50" r=".30" fill="red"></circle>
	</mask>
</defs>
<rect x="0" y="0" width="100" height="100" style="mask:url(#mask)"></rect>
<rect x="0" y="0" width="100" height="100" fill="none" stroke="black"></rect>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/10.3.jpg)

在上图中，mask元素中添加了一个圆，用作蒙版形状，添加了两个rect，一个应用了蒙版，一个添加了边框便于观察。

SVG需要确定蒙版的透明度，每个像素由4个值描述：R,G,B,透明度，使用如下公式计算：

(0.2125\*r + 0.7154\*g + 0.0721\*b)\* opacity

系数不同，是因为完全饱和情况下，红绿蓝的亮度不同。

## 第十一章 滤镜

### 1. 滤镜工作原理

SVG阅读器处理一个图形对象时，会将对象呈现在位图输出设备上，它可以将对象的描述信息转化为一组对应的像素。在使用滤镜时，SVG阅读器不会直接将图形渲染为最终结果，而是先将像素保存到临时位图中，然后将滤镜指定的操作应用到该临时位图，其结果作为最终图形。

在SVG中，使用filter元素指定一组操作(也叫基元),在渲染图形对象时，将该操作应用在最终图形上。

filter标记之间就是我们想要的滤镜基元，每个基元有一个或多个输入，但是只有一个输出，输入可以是原始图形(SourceGraphic)、图形的阿尔法通道(不透明度，SourceAlpha)或者是前一个滤镜基元的输出。

### 2. 创建投影效果

filter元素有一些属性用来描述该滤镜的裁剪区域。通过x,y,width,height属性定义一个滤镜范围，这些属性默认情况是按照对象的边界框计算的，即filterUnits属性的默认值为objectBoundingBox,如果要按照用户单位制定边界，则需要设置该属性值为userSpaceOnUse。

还可以用primitiveUnits属性为基元操作指定单位，默认值为userSpaceOnUse,如果设置为objectBoundingBox则会按照图形尺寸的百分比来表示单位。

使用feGuassianBlur元素指定一个高斯投影基元。


```
<defs>
	<filter id="gaussian" x="0" y="0">
		<feGaussianBlur in="SourceGraphic" stdDeviation="2"></feGaussianBlur>
	</filter>
</defs>
<g transform="translate(10,10)">
	<rect x="10" y="10" width="100" height="100" fill="#ccc" filter="url(#gaussian)"></rect>
</g>
<g>
	<rect x="10" y="10" width="100" height="100" fill="black"></rect>
</g>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.1.jpg) 

在上例中，定义了一个高斯模糊滤镜，然后通过绘制两次矩形产生矩形阴影效果，但是这要求SVG阅读器要绘制两次矩形，更好的方法时添加多个滤镜基元，让SVG阅读器一次性完成渲染。于是就需要对滤镜结果进行存储、链接以及合并。修改后如下：


```
<defs>
	<filter id="gaussian" x="0" y="0">
		<feGaussianBlur in="SourceAlpha" stdDeviation="5" result="blur"></feGaussianBlur>
	<feOffset in="blur" dx="10" dy="10" result="offsetBlur"></feOffset>
		<feMerge>
			<feMergeNode in="offsetBlur" />
			<feMergeNode in="SourceGraphic" /> 
		</feMerge>
	</filter>
</defs>
<g>
	<rect x="10" y="10" width="100" height="100" fill="black" filter="url(#gaussian)"></rect>
</g>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.2.jpg) 

result属性指定当前元素的结果稍后可以通过blur引用，这个与id不同，只能在包含该基元的filter中有效。

feOffset基元接受它的输入，在这里接受的是feGaussianBlur的结果(in="blur")，偏移由dx和dy指定，输出为offsetBlur。

feMerge基元包裹一个feMergeNode元素列表,每个元素都指定一个输入。这些输入按照出现的顺序叠加。在这里blur位于原始图形(SourceGraphic)的下面。

### 3. 发光滤镜


feColorMatrix元素用来以一种通用的方式改变颜色值，可以用来创建一个发光的区域。


```
<defs>
	<filter id="matrix" x="0" y="0">
		<feColorMatrix type="matrix" values="
			0 0 0 0   0
			0 0 0 0.9 0
			0 0 0 0.9 0
			0 0 0 1   0
		"></feColorMatrix>
    </filter>
</defs>
<text x="10" y="100" font-size="40" style="filter:url(#matrix)">发光滤镜</text>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.3.jpg) 

feColorMatrix是一个通用的基元，允许修改任意像素点的颜色或阿尔法值，当type="matrix"时，必须指定一个4x5的矩阵。矩阵中每行数字分别乘以输入像素的r,g,b,a的值和常量1，然后加在一起得到输出值。要设置一个变换，将所有不透明区域回执为相同颜色，可以忽略输入颜色和常量。

矩阵模型：


```
    values="
        0    0    0    red    0
        0    0    0    green  0
        0    0    0    blue   0
        0    0    0    1      0
    "
```
red,green,blue的值通常为0到1之间的十进制数，在上述例子中，red为0,green和blue为0.9会产生一个明亮的青色。

上述type为matrxi，除此之外，还有其他三个值：


feColorMatrix基元的type属性 | 说明
---|---
hueRotate | 色相旋转，value是一个单一的数字，描述颜色的色相值应该被旋转多少度
saturate | 饱和度，values属性指定一个0到1之间的数字，数字越小，颜色越不饱和
luminanceToAlpha | 用亮度决定alpha值,这一属性忽略的values属性值

### 4. feImage滤镜

SVG的feImage滤镜允许使用任意的JPG\PNG\SVG文件或带有id属性SVG元素作为输入源。

```
<defs>
	<filter id="image" x="0" y="0">
		<feImage xlink:href="10.0.jpg" result="bg" x="10" y="10" width="200" height="200" preserveAspectRatio="none"></feImage>
	</filter>
</defs>
<g style="filter:url(#image)">
	<rect x="10" y="10" width="200" height="300" stroke="black"></rect>
</g>
<g>
	<rect x="10" y="10" width="200" height="300" fill="none" stroke="black"></rect>
</g>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.4.jpg)

feImage基元可以将一个图片作为背景，在上示例中，第一个g元素使用了滤镜，因此可以看到一个200*200的图片。第二个g中包含了一个与第一个g中相同大小的矩形，feImage中定义了图片的尺寸，因此没有填充满第一个矩形。默认情况下feImage元素上使用userSpaceOnUse设置宽度、高度以及x和y，如果要基于滤镜区域，则需要设置filter元素上的primitiveUnits属性为objectBoundingBox。

### 5. 光照效果

可以通过滤镜为图形添加光照效果，添加光照效果必须指定以下信息：

~反射类型：漫反射(feDiffuseLighting)或镜面反射(feSpecularLighting)

~想要照亮的对象

~使用的灯光颜色

~想要的光源类型：点光源(fePointLight),远光(FeFistantLight)或聚光灯(feSpotLight)


```
<defs>
	<filter id="diff-light" color-interpolate-filter="sRGB" x="0" y="0">
		<feDiffuseLighting in="SourceGraphic"
			lighting-color="#ffffcc"
			surfaceScale="1"
			diffuseConstant="0.5"
			result="diffuseOutput">
			<fePointLight x="50" y="50" z="20"/>
		</feDiffuseLighting>
		<feComposite in1="diffuseOutput" in2="SourceGraphic" operator="in" result="diffuseOutput"></feComposite>
		<feBlend in1="diffuseOutput" in2="SourceGraphic" mode="screen"></feBlend>
    </filter>
</defs>
<circle cx="50" cy="50" r="50" style="filter:url(#diff-light)"></circle>
<circle cx="170" cy="50" r="50" ></circle>
```
![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.5.jpg)

上述示例中使用feDiffuseLighting定义了一个漫反射元素，设置了光照颜色、计算阿尔法乘积因子(surfaceScale)、RGB值得乘积因子(diffuseConstant)。

fePointLight元素被包裹在feDiffuseLighting中，定义了光源的相关信息(位置)。

feComposite元素接受两个源，并指定两个输入的重叠方式。

feBlend元素也需要两个输入源，还需要一个mode属性指定如何混合输入源。在这里会尝试让图形变亮。


```
<defs>
	<filter id="spec-light" color-interpolate-filter="sRGB" x="0" y="0">
		<feSpecularLighting in="SourceGraphic"
			lighting-color="#ffffcc"
			surfaceScale="1"
			specularConstant="1"
			specularExponent="4"
			result="specOutput"
			>
				<feDistantLight elevation="25" azimuth="0"/>
		</feSpecularLighting>
		<feComposite in1="specOutput" in2="SourceGraphic" operator="in" result="specOutput"></feComposite>
		<feComposite in1="specOutput" in2="SourceGraphic" operator="arithmetic" k1="0" k2="1" k3="1" k4="0"></feComposite>
	</filter>
</defs>
<circle cx="50" cy="50" r="50" style="fill:#060;filter:url(#spec-light)"></circle>
<circle cx="170" cy="50" r="50" style="fill:#060"></circle>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.6.jpg)

上述例子使用了镜面反射feSpecularLight元素，在feSpecularLight元素中定义了光源颜色、surfaceScale、specularConstant等属性。与漫反射的光源类型也不同。

### 6. 访问背景

除了SourceGraphic和SourceAlpha作为滤镜输入之外，还可以访问以及渲染到画布上的图片的某一部分，这部分内容称为backgroundImage和backgroundAlpha。为了访问这些输入信息，滤镜对象必须位于enable-background属性值为new的容器元素之内。


```
<defs>
	<filter id="blur-background" color-interpolate-filter="sRGB" x="0" y="0">
		<feGaussianBlur in="BackgroundImage" stdDeviation="10" result="blur" />
		<feComposite in1="blur" in2="SourceGraphic" operator="in"/>
		<feOffset dx="4" dy="4" result="offsetBlur"></feOffset>
    </filter>
</defs>
<g enable-background="new">
	<rect x="20" y="20" width="100" height="100" style="fill:lightblue;stroke:black;stroke-width:10"></rect>
	<circle cx="100" cy="100" r="30" style="fill:#fff;filter:url(#blur-background)"></circle>
</g>
```

![image](https://github.com/xswei/SVG_Essentials/blob/master/image/11.7.jpg) 

在g元素设置enable-background属性,则它所有的子元素都可以利用背景图像和阿尔法信息。

### 8. 滤镜总结

上述所有滤镜之外，还有好多...写到崩溃(无奈脸)

但是这里只写出了几种常见的滤镜。更多的滤镜相关可以查询相关资料。


## 第十二章 SVG动画

### 1. 动画基础

SVG动画特性基于"同步多媒体集成语言"(SMIL)规范。在这个动画系统中，可以指定想要进行的动画的属性(颜色、动作或者变形等)的起始值和初始值，以及动画开始的时间和持续时间。


```
<rect x="10" y="10" width="200" height="200" stroke="black" fill="none">
	<animate 
		attributeName="width" 
		attributeType="XML" 
		from="200" to="20" 
		begin="0s" dur="5s" 
		fill="freeze">
	</animate>
</rect>
```
上述示例中，rect的宽度从200变化到20，历时5秒。rect不再是一个空元素，包含了动画元素animate。 如果需要在一个元素上指定多个属性的过渡则需要同时使用多个animate元素，分别指定过渡的这些元素的过渡属性。

animate元素指定了以下信息：

属性 | 说明
---|---
attributeName | 动画中改变的值
attributeType | width是一个XML属性，另一个常用的值是CSS，如果忽略这个值，则默认是auto，会先搜索CSS属性，再搜索XML属性
from to | 起始值和结束值
values | from和to只能指定两个值，使用values时可以以列表形式同时指定多个中间值，变换会依次使用列表内的值。如果要交替动画，则指定为start,end,start三个即可实现
dur | 持续时间
fill | 结束时该怎么做，freeze表示冻结，如果不指定会使用默认值：remove，表示动画完成后会返回原始值。
repeatCount | 动画重复次数
repeatDur | 重复应该进行多长时间
calcMode | 过渡类型，可以为线性(linear)，直接跳转(不支持过渡时直接跳转到结束值discrete)，spline，paced等。

### 2. 动画时间与同步动画

SVG动画用到的动画时钟在SVG加载完成时开始启动计时，当用户离开页面时停止计时。因此可以以下列任意一种方式指定动画开始和持续时间为一个数值。


时间 | 说明
---|---
时分秒的完整时间值 | 例如1:20:23
分秒形式 | 例如02:15
以h、min、s或者ms结尾的时间值 | 例如begin="3.5s" dur="1min" 值和单位之间不能有空格

可以将一个动画的开始时间设置另一个动画的结束或开始，而不是固定的时间值。


```
<rect x="10" y="10" width="200" height="25" stroke="black" fill="none">
	<animate 
		id="c1"
		attributeName="width" 
		attributeType="XML" 
		from="200" to="20" 
		begin="0s" dur="5s" 
		fill="freeze">
	</animate>
</rect>
<circle cx="120" cy="60" r="10" stroke="black" fill="none">
	<animate
		attributeName="r"
		attributeType="XML"
		begin="c1.end"
		from="10" to="20"
		dur="4s"
	></animate>
</circle>
```

上述示例中包含了两个过渡，一个是矩形，一个是圆，矩形中的animate元素指定了id="c1"，圆中的animate元素的begin属性为c1.end，也就是圆的过渡要等到矩形的过渡结束之后才开始。此时两个过渡是无延迟的，也可以在两个过渡之间加一个延迟时间：

```
<rect x="10" y="10" width="200" height="25" stroke="black" fill="none">
	<animate 
		id="c1"
		attributeName="width" 
		attributeType="XML" 
		from="200" to="20" 
		begin="0s" dur="5s" 
		fill="freeze">
	</animate>
</rect>
<circle cx="120" cy="60" r="10" stroke="black" fill="none">
	<animate
		attributeName="r"
		attributeType="XML"
		begin="c1.end+5s"
		from="10" to="20"
		dur="4s"
	></animate>
</circle>
```

圆的过渡时间改为c1.end+5s，也就是矩形的过渡结束5秒之后才开始圆的过渡，同理，也可以设置为c1.begin+xs，矩形开始x秒之后开始圆的过渡。

### 3. 多边形和path动画

多边形和path的值为数字列表，也可以对这些属性进行过渡，但是要保证数字列表中数字的数字数量没有变即可。列表中的每个点都是单独变换的。


```
<polygon points="30 30 70 30 90 70 10 70" style="fill:#fcc;stroke:black">
	<animate
		attributeName="points"
		attributeType="XML"
		to="50 30 70 50 50 90 30 50"
		begin="0s" dur="5s" fill="freeze"
	></animate>
</polygon>
<path d="M15 50 Q40 15,50 50,65 32,100 40" style="fill:#fcc;stroke:black" transform="translate(0,50)">
	<animate
		attributeName="d"
		attributeType="XML"
		to="M50 15Q15 40,50 50,32 65,40 100"
		begin="0s" dur="5s" fill="freeze"
	></animate>
</path>
```

### 4. 对坐标变换进行过渡

animate元素不适合对平移、旋转、缩放进行过渡，因为这些坐标变换被包裹在transform属性内。animateTransform元素可以解决这个问题。


```
<g transform="translate(100,60)">
	<rect x="-10" y="-10" width="20" height="20" style="fill:#ff9;stroke:black">
		<animateTransform
			attributeType="XML"
			attributeName="transform" type="scale"
			from="1" to="4 2"
			dur ="3s"
			begin = "0s" fill="freeze"
		></animateTransform>
	</rect>
</g>
```
上述示例中，会对矩形的transform属性进行过渡，通过type指定是对scale进行过渡。可以对矩形进行缩放。缩放的中心为100,60(因为矩形被包裹在g中，g被平移了100,60)

如果同时指定了多个坐标变换，比如同时对平移和缩放，则需要指定additive属性。additive属性默认为replace，即会替换动画对象的指定变换。不适合一系列变换，因为后面的会将之前的过渡覆盖掉，因此要设置additive属性值为sum。


```
<rect x="-10" y="-10" width="20" height="20" style="fill:#ff9;stroke:black">
	<animateTransform
		attributeType="XML"
		attributeName="transform" type="scale"
		from="1" to="4 2"
		dur ="3s"
		begin = "0s" fill="freeze"
		additive = "sum"
	></animateTransform>
	<animateTransform
		attributeType="XML"
		attributeName="transform" type="rotate"
		from="0" to="90"
		dur ="3s"
		begin = "3s" fill="freeze"
		additive = "sum"
	></animateTransform>
</rect>
```
指定additive="sum"后，先对矩形进行缩放，然后在缩放的基础上进行旋转。

### 5. 沿着path运动

我们已经可以在animateTransform元素中通过translate对过渡对象进行平移变换，但是如果需要让过渡对象沿着更复杂的路径运动，则需要使用animateMotion元素。


```
<rect x="-10" y="-10" width="20" height="20" style="fill:#ff9;stroke:black">
	<animateMotion
		path="M50,135C100,25 150,225 200,125"
		dur="6s" fill="freeze"
	></animateMotion>
</rect>
```

上述示例中，可以使矩形沿着path="M50,135C100,25 150,225 200,125"做轨迹运动，但是运动过程中，矩形不会自动根据轨迹方向旋转，因此需要设置animateMotion元素的rotate属性值为auto。

如果已经有了path轨迹，不想再在animateMotion元素中定义一次path，则可以使用mPath元素。mPath元素定义在animateMotion元素内部，通过xlink:href引用指定的路径即可。


```
<path id="movePath" d="M50,135C100,25 150,225 200,125" fill="none" stroke="black"></path>
<rect x="-10" y="-10" width="20" height="20" style="fill:#ff9;stroke:black">
		<animateMotion dur="6s" fill="freeze" rotate="auto">
			<mPath xlink:href="#movePath"></mPath>
		</animateMotion>
</rect>
```

结果同上。

### 6. CSS处理SVG动画

现代浏览器都支持CSS梳理SVG动画，使用CSS处理SVG动画需要两个步骤：一是选中要运动的元素，然后设置将动画属性作为一个整体进行设置。二是告诉浏览器改变选中元素的哪个属性以及在动画的什么阶段。这些都定义在@keyframes说明符中。


动画属性 | 说明
---|---
animation-name | @keyframes说明符的名称
animation-duration | 动画持续时长
animation-timing-function | 如何计算中间值，也就是动画类型：线性，缓入缓出等等
animation-iteration-count | 重复次数
animation-direction | 动画是反向还是正向或者交替执行
animation-play-state | 可以设置为running或paused
animation-delya | 延迟时间
animation-fill-mode | 动画不再执行时使用什么属性，可以为forwards(结束时属性)、backwards(开始时属性值)、both


```
<defs>
	<g id="starDef">
		<circle cx="50" cy="50" r="10"></circle>
	</g>
</defs>
<use id="star" class="starStyle" xlink:href="#starDef" fill="#008000"></use>
```
对应的CSS代码为：


```
.starStyle{
	animation-name:starAnim;
	animation-duration: 2s;
	animation-iteration-count: 4;
	animation-direction: alternate;
	animation-timing-function: ease;
	animation-play-state: running; 
}
@keyframes starAnim{
	0%{
		fill:red;
		transform: translate(100px,100px);
	}
	50%{
		fill:green;
		transform: translate(120px,100px);
	}
	100%{
		fill:blue;
		transform: translate(100px,120px);
	}
}
```

在@keyframes中设置关键帧属性。




