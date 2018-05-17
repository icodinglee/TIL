
###  强制内核渲染
比如说360浏览器是分兼容模式和极速模式，兼容模式使用的是IE内核，而极速模式使用的Chrome的内核(webkit)，因此强制内核渲染也是必须的。另外，即使是在IE下，默认使用的内核也是不同的。

```
//强制使用IE7模式来解析
<meta http-equiv=“X-UA-Compatible” content=“IE=EmulateIE8″>
<meta http-equiv=“X-UA-Compatible” content=“IE=8″>  

// 对于目前来说加上下面的代码就好了
<meta http-equiv="x-ua-compatible" content="ie=edge,chrome=1">
```

### 空白图片 base64
```
data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==
```

### 不要滚动条
```
html ::-webkit-scrollbar {
    /*不要滚动条*/
    display: none;
}
@-ms-viewport {
    width: device-width;
}
```
###  input/trextarea
```
/*取消输入框默认有内部阴影*/
input,
textarea {
    border: 0; /* 方法1 */
    -webkit-appearance: none; /* 方法2 */
}

： 
/*取消input和textarea的聚焦边框*/
input{outline:none} 

/*取消textarea可拖动放大*/
textarea{resize:none} 

/*placeholder设置颜色*/
textarea::-webkit-input-placeholder{  color:#be916a;}

/*input placeholder文字垂直居中(Mobile & PC)*/
input{
　　line-height: normal; /* for non-ie */  
　　line-height: 22px\9; /* for ie */  
}

/*去掉a、input和button点击时的蓝色外边框和灰色半透明背景*/
a,button,input,optgroup,select,textarea {
    -webkit-tap-highlight-color:rgba(0,0,0,0);
}
```

### 移动端去掉长按选择
```
div,span,p,img{  -webkit-user-select: none;/*禁用手机浏览器的用户选择功能 */  -moz-user-select: none;}
```

### 禁止长按链接与图片弹出菜单
```
a, img {
    -webkit-touch-callout: none; 
/*禁止长按链接与图片弹出菜单*/
}
```

### display:inline-block 间隙去除
````
.wrapper{
　　font-size:0
 }
.inlineblock{
    display: inline-block;  
    letter-spacing: normal;
    word-spacing: normal;
}

<div class='wrapper'>
    <div class="inlineblock"></div>
</div>

````

### 响应式
```
@media (max-width: 767px) {
  .hidden-xs {
    display: none !important;
  }
}
@media (min-width: 768px) and (max-width: 991px) {
  .hidden-sm {
    display: none !important;
  }
}
@media (min-width: 992px) and (max-width: 1199px) {
  .hidden-md {
    display: none !important;
  }
}
@media (min-width: 1200px) {
  .hidden-lg {
    display: none !important;
  }
}
```

### 画1px的细线
```
.border1px{  position: relative;}
.border1px:after{
    content:"";
    position: absolute;
    bottom:0px;
    left:0px;
    right:0px;
    border-bottom:1px solid red;
    border-left:1px solid red;
    -webkit-transform:scaleY(.5);
    -webkit-transform-origin:0 0;
}

```

### 占位图生成

```
http://www.atool.org/placeholder.png?size=500x200&text=aTool全站广告位征集&&bg=836&fg=fff
http://placehold.it/640x320 // 推荐
http://placekitten.com/200/300
```

### 浏览器初始化样式不同导致兼容问题
两个浏览器样式初始化库： [reset.css](http://meyerweb.com/eric/tools/css/reset/)  和 [normalize.css](https://necolas.github.io/normalize.css/)

### 透明度兼容css
```
IE: filter:progid:DXImageTransform.Microsoft.Alpha(style=0,opacity=60)
  或者：filter:alpha(opacity=40);

非ie: opacity:0.6
```

###  盒子模型
标准 w3c 盒子模型的范围包括 margin、border、padding、content，并且 content 部分不包含其他部分； IE盒子模型的范围也包括 margin、border、padding、content。

和标准 w3c 盒子模型不同的是：ie 盒子模型的 content 部分包含了 border 和 padding。

因此，问题主要表现在css中的width是否计算border和padding的问题，这个是默认的盒子解析模型不同导致的。

IE6：中包括border和padding(box-sizing: border-box;)
IE7和非IE：width宽度不包括border和padding(box-sizing: content-box;)

解决方式： 根据使用方式，写好box-sizing属性。

### 浮动问题
子元素进行浮动，而父元素没有撑开盒子
```
.clearfix:before,.clearfix:after {  content: " "; /* 1 */  display: table; /* 2 */}
.clearfix:after {  clear: both;}
```
### 高度不适应
高度不适应是当内层对象的高度发生变化时外层高度不能自动进行调节，特别是当内层对象使用margin 或padding时

例如：
```
#box {background-color:#eee; } 
#box p {margin-top: 20px;margin-bottom: 20px; text-align:center; } 
<div id="box">
<p>p对象中的内容＜/p>
</div>
```
解决技巧：
1.  给div 加上border
2.  在P对象上下各加2个空的div对象CSS代 {height:0px;overflow:hidden;}

###  超链接访问过后hover样式就不出现的问题
解决技巧
```
a:link {} 
a:visited {} 
a:hover {} 
a:active {}
```
### calc 的使用
浏览器支持的不是很好，而且在使用的时候要加上厂商前缀，达到兼容性。另外，注意减号之间的空格。

```
width: calc(100% - 80px);

#formbox {
  width:  130px;
  /*加厂商前缀，操作符（+，-，*，/）两边要有空格）*/               
  width:  -moz-calc(100% / 6);   
  width:  -webkit-calc(100% / 6);   
  width:  calc(100% / 6);   
  border: 1px solid black;
  padding: 4px;
}

```

### Chrome中文版12号以下的字体不识别
```
html{-webkit-text-size-adjust:none;} 

/*针对Opera*/ 

@media all and (-webkit-min-device-pixel-ratio:10000), not all and (-webkit-min-device-pixel-ratio:0) { 

#nav{ width:400px; } 

}
```

### 注意事项

- 记得清除浮动。(在具有float元素的容器底部加入清除浮动)

- 浮动元素尽量给一个确定的宽度。

- 尽量使用padding代替margin。

- 若同时有float及margin，加入display:inline。

- 尽量避免使用绝对定位进行布局；若使用，需明确指定z-index, top, left；

- 尽量避免使用半透明png图片(PNG-24)；若使用，用PNG修复补丁修复之；

- 若出现宽度被撑开现象，设置overflow:hidden;

- 若出现莫名padding，设置 font-size:0 及 overflow:hidden;

### 其他技巧
- FF下给 div 设置 padding 后会导致 width 和 height 增加, 但IE不会.(可用!important解决)

- 垂直居中.将 line-height 设置为 当前 div 相同的高度, 再通过 vertical-align: middle.( 注意内容不要换行.)

- 水平居中. margin: 0 auto;(当然不是万能)

- 若需给 a 标签内内容加上 样式, 需要设置 display: block;(常见于导航标签)

- ul 标签在 FF 下面默认有 list-style 和 padding . 最好事先声明, 以避免不必要的麻烦. (常见于导航标签和内容列表)

- 作为外部 wrapper 的 div 不要定死高度, 最好还加上 overflow: hidden.以达到高度自适应.

### 图片默认有间距
解决方案：使用float属性为img布局

### 标签最低高度设置min-height不兼容
解决方案：如果我们要设置一个标签的最小高度200px，需要进行的设置为：
```
{ 
   min-height:200px; 
   height:auto !important;
   height:200px;
   overflow:visible;
}
````
