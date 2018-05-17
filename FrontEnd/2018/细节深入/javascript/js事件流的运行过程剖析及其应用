#### js事件流触发原理
DOM2.0模型将事件处理流程分为三个阶段：一、事件捕获阶段，二、事件目标阶段，三、事件起泡阶段

![原理图](http://upload-images.jianshu.io/upload_images/5891379-24a77aa68b3a23fe.png?imageMogr2/auto-orient/strip)

事件捕获阶段： 当某个元素触发某个事件（如onclick事件），顶层对象就会发出一个事件流，随DOM树的根节点向目标节点流去，直到到达事件真正发生的目标元素身上。在这个过程中，各个dom上绑定的事件不会被触发。

事件目标阶段： 当事件流到达目标元素的时候，如果目标元素绑定的有事件，则执行，如果没有，将进行事件起泡阶段。

事件起跑阶段： 事件流原路返回，从目标元素开始，向DOM树 根节点流动，途中如果遇到有节点绑定了相应的事件处理函数，这些函数就会被触发。这样，整个事件的触发便形成一个闭环。

#### 关于事件委托
根据事件流原理，我们知道当点击一个元素之后，全局会获得一个event.target对象（注意，是全局对象,IE 下是window.event.srcElement），及目标对象，因为这个event.target变量是全局的，因此，我们可以通过这个对象，在事件冒泡阶段将其传递给父亲或者更高级节点，委托处理。

一个简单的例子：
```
    <div id="uu">
      <ul>
        <li>111</li>
        <li>222</li>
        <li>333</li>
        <li>444</li>
      </ul>
    <div>

//这里我们绑定在了父节点的事件上
document.getElementById('uu').onclick = function(e) {
          var e = e || window.event;
          var target = e.target || e.srcElemnt;
          console.log(target)
          if (target.nodeName.toLowerCase() === 'li') {
            console.log(target.innerHTML)
          }
        }

// 我们还可以绑定在他太爷爷节点上， 反正event.target 都是一样的
document.onclick =function(e){
          var e = e || window.event;
          var target = e.target || e.srcElemnt;
          console.log(target)
          if (target.nodeName.toLowerCase() === 'li') {
            console.log(target.innerHTML)
          }
        }

由于是同一个全局变量event.target   所以得到的输出结果一样
```
### js阻止冒泡
由于事件流的机制，在捕获了事件目标之后，事件流往回走，会执行经过的dom节点上绑定的事件。在很多情况下，我们不想让绑定在父节点事件执行，这时我们就需要阻止冒泡事件的发生。

同一个例子
```
document.getElementById('uu').onclick = function(e) {
          var e = e || window.event;
          if ( e && e.stopPropagation )
              //因此它支持W3C的stopPropagation()方法
              e.stopPropagation(); 
          else
              //否则，我们需要使用IE的方式来取消事件冒泡
              window.event.cancelBubble = true;
          var target = e.target || e.srcElemnt;
          console.log(target)
          if (target.nodeName.toLowerCase() === 'li') {
            console.log(target.innerHTML)
          }
        }

        document.onclick =function(e){
          var e = e || window.event;
          var target = e.target || e.srcElemnt;
          console.log(target)
          if (target.nodeName.toLowerCase() === 'li') {
            console.log(target.innerHTML)
          }
        }
```

此时再次进行点击ul内的元素，ul上绑定的方法会执行，document.onclick方法将不会被执行，冒泡阶段被中断。

### 事件绑定的兼容

HTML5工作组写法
```
var addEvent = (function() {
    if (document.addEventListener) {
        return function(el, type, fn) {
            if (el.length) {
                for (var i = 0; i < el.length; i++) {
                    addEvent(el[i], type, fn);
                }
            } else {
                el.addEventListener(type, fn, false);
            }
        };
    } else {
        return function(el, type, fn) {
            if (el.length) {
                for (var i = 0; i < el.length; i++) {
                    addEvent(el[i], type, fn);
                }
            } else {
                el.attachEvent('on' + type,
                function() {
                    return fn.call(el, window.event);
                });
            }
        };
    }
})();
```
