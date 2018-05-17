目标：实现一个输入框，输入之后其后会动态的显示输入框内字节长度

##  jquery封装组件风格实现组件
```
 var textCount=(function(){ 
      //私有方法，外面访问不到
      var _bind=function(this){
          var self=this;
          this.input.on("keyup",function(){
              self.render()
           })
      }
       var _getNum=function(that){
            return that.input.val().length
       }
      var TextCountFun=function(config){
            
          };
      TextCountFun.prototype.init=function(config){
        this.input=$(config);
          _bind(this);

          return this
      }
      TextCountFun.prototype.render=function(){
         var num=_getNum(this);
          if(num==0) this.input.after('<span id="J_input_count"></span>')
            $('#J_input_count').html(num+'个字');
      }
      return TextCountFun
 })();

new  textCount().init(“#btn").render()
```


上诉代码把所有的东西都包在了一个自动执行的闭包里面，所以不会受到外面的影响，并且只对外公开了TextCountFun构造函数，生成的对象只能访问到init,render方法。大部分的jQuery插件都是这种写法

## es6实现的类继承，面向对象编程

在最新的es2015里，引入了class类这一概念，这一经常出现在java 和c++的神器终于也被引入到了js里，同时被引入的还有
- super  被继承的类  常写在constructor中用来调用父类的构造函数,如果不在子类的构造函数中调用，new一个实例对象的时候会报错
```
The super keyword is used to call functions on an object's parent.
super([arguments]); // calls the parent constructor.
super.functionOnParent([arguments]);
```
- extends  继承类
- get   获取属性
- set   设置属性
- static 静态方法,只能通过类来调用，不能通过实例调用
( ES6 明确规定， Class 内部只有静态方法， 没有静态属性。)
```
Static method calls are made directly on the class and are not callable on instances of the class. Static methods are often used to create utility functions.
```

为了体现继承的优势，我们让子类新加一个功能，当输入框数字字节数大于3时，后面显示的文字变成红色

```
class TextCount {
         constructor(config){
           this.input=$(config);
         }
         _watch(){
           let self=this;
           this.render()  // 这里要渲染一次，不然首次不会出现提示文字
           this.input.on("keyup",function(){
             self.render()
           })
         }
         getNum(){
           return this.input.val().length
         }
         render(){
           let num=this.getNum();
           console.log(num)
           if (num == 0) {
             this.input.after('<span id="J_input_count"></span>');
           };
           $('#J_input_count').html(num+' 个数字');
         }
      }

  class NumCount extends TextCount {
        constructor(data) {
          super(data)  //调用父类的构造函数 得到this.input
        }  
        //子类新添加的方法
        judge(){
          this._watch();   //也可以写成  super._watch()
          let self=this；
          this.input.on("keyup",function(){
            if(self.input.val().length>3){
              $('#J_input_count').css("color","red")
            }else{
              $('#J_input_count').css("color","#000")
            }
          })
        }
      }
     new NumCount("#btn").judge()  //实例化类
```
