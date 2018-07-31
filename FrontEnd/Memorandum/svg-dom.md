# SVG_DOM

  
SVG中的一些DOM接口

在使用脚本操作SVG时，有时候希望能计算一些没有被直接定义的几何属性。比如不管文字怎么变化，都能画一个更好匹配文字区域的矩形。

### 1. SVG Element(SVG命名空间中的任何元素)


方法或属性 | 说明
---|---
.ownerSVGElement() | 返回最近的祖先SVG元素，如果是在顶级SVG调用，则返回null
.viewportElement() | 返回建立当前元素viewport的svg,pattern,symbol或marker元素


### 2. SVG Locatable(占据坐标空间的元素或含有变换属性的元素：图形元素、g、svg)


方法或属性 | 说明
---|---
.nearestViewportElement | 建立当前元素viewport的svg,pattern,symbol或marker元素
.farthestViewportElement | 包含当前元素的顶级svg元素
.getBBox() | 以SVGRect对象的形式返回当前元素的位置和边界，包含x,y,width,height属性，代表能包裹整个图形的最小矩形框，不受笔画宽度，裁剪、蒙版滤镜影响
.getCTM() | 返回SVGMatrix对象，代表从当前元素的坐标系统到nearestViewportElement坐标系统的变换
.getScreenCTM | 返回SVGMatrix对象，代表从当前元素到文档最顶级屏幕坐标或客户端坐标之间的变换矩阵。这个方法在处理事件时可以很方便的转化鼠标/指针使用的坐标系统和图形系统
.getTransformToElement(SVGElement) | 返回SVGMatrix对象，代表当前元素坐标到指定元素坐标之间的变换



### 3. SVG Transform(能使用transform的任何元素)


方法或属性 | 说明
---|---
.transform | 代表定义在元素上的变换原始值和动画过程中的值的SVGAnimatedTransformList

### 4. SVG Stylable(等使用style的任何元素)


方法或属性 | 说明
---|---
.style |返回一个CSSStyleDeclaration元素，代表内联在元素上的样式


### 5. SVG SVGElement(svg元素)


方法或属性 | 说明
---|---
.suspendRedraw(maxWritTimeInMilliseconds) | 告诉浏览器在绘制图形时暂停指定时间(最长一分钟).如果希望做很多修改，但在最后一期应用，则可以使用这个方法。该方法返回一个数字ID，可以稍后传给unsuspendRedraw
.unsuspendRedraw(suspendID) | 取消ID代表的由suspendRedraw创建的暂停
.unsuspendRedrawAll() | 取消SVG上所有的由suspendRedraw创建的暂停
.pauseAnimations() | 暂停SVG上所有的SMIL动画时钟
.unpauseAnimations() | 恢复SVG上所有的SMIL动画时钟
.animationsPaused() | 返回true或false，代表动画是否由上面的方法暂停
.getCurrentTime() | 返回SMIL动画的时钟当前值。正常情况下这个值为文档加载之后的秒数，暂停动画会影响这个值
.setCurrentTime(timeInSeconds) | 修改SMIL动画时钟值，影响所有的动画
.getIntersectionList(rectangle,referenceElement) | 返回元素列表，代表在指定坐标系统下和指定的矩形有重叠并且这些元素全是指定元素的子元素。
.getEnclosureList(rectangle,referenceElements) | 与getIntersectionList类似，区别在于这个只返回完全在矩形中的元素
.checkIntersection(element,rectangle) | 返回true或false，代表svg元素坐标系统下，指定元素与rect是否有重叠
.checkEnclosure(element,rectangle) | 返回true或false，代表svg元素坐标系统下，指定元素是否完全被包含在指定矩形范围内
.createSVGXxx | 创建各种数据对象(SVGPoint,SVGAngle,SVGMatrix)等


### 6. SVG UseElement(use元素)


方法或属性 | 说明
---|---
.instanceRoot | 包含use元素代表的图形DOM树最顶层节点


### 7. SVG PathElement(path元素)


方法或属性 | 说明
---|---
.getTotalLength() | 以用户坐标返回计算后的路径长度，不同客户端中不一定完全一样，因为曲线会做取舍
.getPointAtLength(distance) | 返回以用户坐标计算的距离起点distance单位的点，包含x和y属性


### 8. SVG PathData(path元素以及其他支持路径数据属性的元素，如animateMotion)

方法或属性 | 说明
---|---
.pathSegList | 包含一个代表路径各个部分的对象列表
.normalizedPathSeglist | 简化版的路径各部分列表，每个部分被转化为绝对坐标下的移动、画线、曲线或关闭路径等命令


### 9. SVG AnimatedPoints(polygon和polyline元素)

方法或属性 | 说明
---|---
.points | 返回SVGPointList，代表与元素相关的点的列表

### 10. SVG TextContentElement(SVG命名空间中的任何元素)


方法或属性 | 说明
---|---
.getNumberOfChars() | 返回元素中字符总数，包括tspan元素，多字节字符会用UTF-16呈现然后计数
.getComputedTextLength() | 返回在用户坐标系下应用了所有CSS属性和dx,dy属性之后包含整个文本的长度，不包括在textLength属性基础上做的调整
.getSubStringLength(charNum,nChars) | 返回截取的字符串一部分的长度，从charNum开始，截取nChars个，如果不足nchars个则截取到字符串结尾为止
.getStartPositionOfChar(charnum)|返回一个带有x和y属性的SVGPoint对象，表示在用户坐标系中指定字符的起始位置，字符与起始位置的相对关系取决于书写模式(左到右，上到下)。默认情况下从左到右，起始点位于字符最左侧基线的位置
.getEndPositionOfChar(charNum) | 与getStartPositionOfChar类似，返回结束时的基线位置
.getExtendOfChar(charNum) | 与getBBox方法类似，这个是返回单个字符的包裹矩形框
.getRotationOfChar(charNum) | 返回指定字符在经过所有变换之后的旋转角度(以度为单位)
.getCharNumAtPosition(point) | 返回指定位置的字符索引，如果指定点没有字符则返回-1

### 11. ElementTimeControl和SVGAnimationElement(SVG动画元素animate\set\animateTransform\animateMotion)

方法或属性 | 说明
---|---
.targetElement | SVG动画修改的SVG元素
.beginElement() | 如果动画没有被restart属性never或whenNotActive阻止的话，立即开始
.beginElementAt(offset) | 在offset秒之后开始动画，如果offset为负值，则立即开始
.endElement() | 立即结束当前正在运行的动画
.endElementAt(offset) | 在offset秒之后结束当前动画
.getStartTime() | 如果动画正在进行，则返回相对SVG时钟的开始时间，如果动画还没有开始返回将要开始的时间
.getCurrentTime() | 返回SVG动画时钟的当前时间(秒)
.getSimpleDuration() | 返回动画每一轮的时长(dur属性)，如果没有定义则抛出错误
