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


