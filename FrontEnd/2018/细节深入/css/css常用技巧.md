### **继承box-sizing**
从html元素继承box-sizing
``` 
html {
  box-sizing: border-box;
}

*, *::before, *::after {
  box-sizing: inherit;
}
```

### **学会使用:not 选择器**
给除了最后一个元素的部分加边框
```
.nav li:not(:last-child) {
  border-right: 1px solid #666;
}
```
### **使用负的 nth-child 来选择元素**
```
/* 选择除前3个之外的所有项目，并显示它们 */
li:not(:nth-child(-n+3)) {
  display: none;
}
```

### **创造格子等宽的表格**
table-layout: fixed 可以让每个格子保持等宽：
```
.calendar {
  table-layout: fixed;
}
```

### **利用属性选择器来选择空链接**
```
a[href^="http"]:empty::before {
  content: attr(href);
}
```


### **清除浮动 clearfix**

```
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
```

### **滚动条样式**


```

html 
<div class="custom-scrollbar">
  <p>Lorem ip</p>
</div>

css
/* Document scrollbar */
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
}
::-webkit-scrollbar-thumb {
  border-radius: 10px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}
/* Scrollable element */
.some-element::webkit-scrollbar {

}
```
### ** 设置鼠标选择文字的样式**

```
::selection {
  background: aquamarine;
  color: black;
}
.custom-text-selection::selection {
  background: deeppink;
  color: white;
}
```

### ** css变量**

```
html
<p class="custom-variables">CSS is awesome!</p>

css
:root {
  --some-color: #da7800;
  --some-keyword: italic;
  --some-size: 1.25em;
  --some-complex-value: 1px 1px 2px whitesmoke, 0 0 1em slategray, 0 0 0.2em slategray;
}
.custom-variables {
  color: var(--some-color);
  font-size: var(--some-size);
  font-style: var(--some-keyword);
  text-shadow: var(--some-complex-value);
}
```

### **禁止内容被选择**

```
html
<p>You can select me.</p>
<p class="unselectable">You can't select me!</p>

css
.unselectable {
  user-select: none;
}
```

### ** 简单构造loading**
[效果实现](https://codepen.io/pen/?)

```
html
<div class="donut"></div>

css
@keyframes donut-spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.donut {
  display: inline-block;
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #7983ff;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: donut-spin 1.2s linear infinite;
}
```

### **创建一个基于元素颜色的box-shadow**

```
html
<div class="dynamic-shadow"></div>

css
.dynamic-shadow {
  position: relative;
  width: 10rem;
  height: 10rem;
  background: linear-gradient(75deg, #6d78ff, #00ffb8);
  z-index: 1;
}
.dynamic-shadow::after {
  content: '';
  width: 100%;
  height: 100%;
  position: absolute;
  background: inherit;
  top: 0.5rem;
  filter: blur(0.4rem);
  opacity: 0.7;
  z-index: -1;
}
```

![实现效果](/img/bV9VEB)

### **绘制文字颜色渐变**

```
html
<p class="gradient-text">Gradient text</p>

css
.gradient-text {
  background: -webkit-linear-gradient(pink, red);
  -webkit-text-fill-color: transparent;
  -webkit-background-clip: text;  // 设置背景绘制区域
}
```

### **下划线滑动效果**

```
html
<p class="hover-underline-animation">Hover this text to see the effect!</p>

css
.hover-underline-animation {
  display: inline-block;
  position: relative;
  color: #0087ca;
}
.hover-underline-animation::after {
  content: '';
  position: absolute;
  width: 100%;
  transform: scaleX(0);
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #0087ca;
  transform-origin: bottom right;
  transition: transform 0.25s ease-out;
}
.hover-underline-animation:hover::after {
  transform: scaleX(1);
  transform-origin: bottom left;
}
```

### **选中一个时，隐藏兄弟**

[查看效果](https://codepen.io/pen/?)
```
html
<div class="sibling-fade">
  <span>Item 1</span>
  <span>Item 2</span>
  <span>Item 3</span>
  <span>Item 4</span>
  <span>Item 5</span>
  <span>Item 6</span>
</div>

css
span {
  padding: 0 1rem;
  transition: opacity 0.2s;
}
.sibling-fade:hover span:not(:hover) {
  opacity: 0.5;
}
```

### **为更好的移动体验，为表单元素设置字体大小**
当触发<select>的下拉列表时，为了避免表单元素在移动浏览器（IOS Safari 等等）上的缩放，加上font-size
```
input[type="text"],
input[type="number"],
select,
textarea {
  font-size: 16px;
}
```
