在一些可视化项目需求中，常常要求我们兼容到ie8，这里就简单介绍一下怎么在可视化项目里处理ie8的兼容问题。

**项目使用的技术**
- react
- echart
- zrender

**ie8有哪些不兼容**
- **不支持高版本react**
-  **不支持svg canvas**
- 不支持box-shadow
- 不支持base64
- 不支持rgba()
- 支持first-child, 不支持last-child
- 不支持nth-child
- 不支持 :not
- 不支持 ：checked
- 不支持 :disabled
- 不支持border-radius
- 不支持 placeholder
- 不支持opacity
- 不支持渐变
- 不支持中文命名文件
- 不支持 indexOf trim


## 解决方案

### 在ie8下使用 react

- react@0.14.9     react-dom@0.14.9
不使用 React v15 或更高版本。使用仍然支持 IE8 的 React v0.14 即可

- console.polyfill@^0.2.2
-  es5-shim@^4.4.1
Es5-shim是一个简单库，它是在ECMAScript 3的引擎上实现了ECMAScript 5的新特性,，而且在Node.js上和在浏览器上有完全相同的表现，引入该文件后，我们就可以随心所欲使用ECMAScript新增的新方法了。

- es3ify-webpack-plugin@0.0.1
把引入的node_modules 和 src下的 js  || jsx 文件，转化为es3的语法

**配置项如下**

(进行代码拆分)
```
var es3ifyPlugin = require('es3ify-webpack-plugin');
module.exports = {
 ...
 entry: {
        main: ['babel-polyfill', './src/index.jsx'],
        vendor: ['es5-shim', 'es5-shim/es5-sham', 'console-polyfill']
 }，
 plugins: [
       new es3ifyPlugin(),
       ...
 ],
 ...
}

```

或（不进行拆分） 
```

var es3ifyPlugin = require('es3ify-webpack-plugin');
module.exports = {
 ...
 entry: [
    'es5-shim',
    'es5-shim/es5-sham',
    'console-polyfill',  
    'babel-polyfill', 
    './src/index'
 ],
 plugins: [
       new es3ifyPlugin(),
       ...
 ],
 ...
}
```
### 不支持indexOf trim

手动加入一下代码，添加在模板index.html中
```
if (!Array.prototype.indexOf){
        Array.prototype.indexOf = function(elt /*, from*/)
        {
            var len = this.length >>> 0;
            var from = Number(arguments[1]) || 0;
            from = (from < 0)
                ? Math.ceil(from)
                : Math.floor(from);
            if (from < 0)
            from += len;
            for (; from < len; from++)
            {
            if (from in this &&
                this[from] === elt)
                return from;
            }
            return -1;
        };
        if (!String.prototype.trim) {
            String.prototype.trim = function () {
                return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
            };
        }
}
```
### 不支持中文命名文件路径
解决方法： 使用encodeURL
```
mapUrl = `/static/data/map/${encodeURI(province)}.json`;
```
### 不支持渐变
```
progid:DXImageTransform.Microsoft.gradient(startColorstr='#B2FFFFFF', endColorstr='#00FFFFFF',GradientType=0 );
```
- startColorStr:可选项。字符串(String)。设置或检索色彩渐变的开始颜色和透明度
- EndColorStr:可选项。字符串(String)。设置或检索色彩渐变的结束颜色和透明度。

*其格式为 #AARRGGBB 。 AA 、 RR 、 GG 、 BB 为十六进制正整数。取值范围为 00 - FF 。 RR 指定红色值， GG 指定绿色值， BB 指定蓝色值，参阅 #RRGGBB 颜色单位。 AA 指定透明度。 00 是完全透明。 FF 是完全不透明。超出取值范围的值将被恢复为默认值。*

- GradientType:可读写。整数值(Integer)。设置或检索色彩渐变的方向。
　1:默认值。水平渐变。 
　0:垂直渐变。 

### 不支持透明度
```
filter: alpha(opacity=40);
```

### 不支持border-radius
 解决方法： 使用pie.js

下载并引入pie.js,  pie.js的内容如下。
![image.png](http://upload-images.jianshu.io/upload_images/5545478-54f4404b766ecec0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在样式中， 引入pie.htc即可
```
{
  border-radius: 10px;
   behavior: url(/static/plugins/pie/PIE.htc);  /** 引入pie.htc
}
```
### 不支持 input placeholder属性
解决方案： js模拟 placeholder

react Input组件代码示例
```
import styles from './index.scss';
import React, { Component } from 'react';
import classNames from 'classnames';

class Input extends Component {
    constructor(props) {
        super(props);
        this.supportPlaceholder = '';
    }

    componentDidMount() {
        this.props.callback && this.props.callback();
        var supportPlaceholder = 'placeholder' in document.createElement('input');
        this.supportPlaceholder = supportPlaceholder;
        this.placeholder();
    }

    componentDidUpdate(){

        if($(this.textInput).val() || document.activeElement === this.textInput){
            return false;
        }
        this.placeholder();
        

    placeholder = () => {
        let $self = $(this.textInput);
        if(!this.supportPlaceholder && $self.attr('type') === 'text'){        
            
            let defaultValue = this.props.defaultValue;
            const value = this.props.value;
            const inputText = value ? value : defaultValue ? defaultValue : this.props.placeholder;
            if(!defaultValue){
                $self.val(inputText);
            }           
        }
    }

    focus = () =>{
        if($(this.textInput).val() === this.props.placeholder){
            $(this.textInput).val('');
        }
    }

    blur = () => {
        if($(this.textInput).val() === ''){
            $(this.textInput).val(this.props.placeholder).addClass('phcolor');
        }
    }

    keyDown = () => {
        $(this.textInput).removeClass('phcolor');
    }

    render() {
        const { supportPlaceholder } = this;
        let {className, style, ...other} = this.props;
        return (
            <input 
                type="text"
                className={classNames('input', 'border_color_gray', 'synBlockBg', className)}
                {...other}
                style={style}
                onFocus={() => !supportPlaceholder && this.focus()}
                onBlur={() => !supportPlaceholder && this.blur()}
                // onKeyDown={() => !supportPlaceholder && this.keyDown()}
                ref={(input) => { this.textInput = input; }} 
            />
        );
    }
}

export default Input;
```

### 不支持 svg canvas绘图元素

如图，绘制连线

![示例](http://upload-images.jianshu.io/upload_images/5545478-8a441bf2fb691e56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正常情况下，使用svg 或者 canvas 很容易实现，但是由于ie8不支持，我们只能选用其它方式。

**解决方法：使用vml 元素。**
在这里 我们使用了 zrender这个库。 (  "zrender": "3.6.1"  )

```
import zrender from 'zrender';
import Line from 'zrender/lib/graphic/shape/Line';
import 'zrender/lib/vml/vml.js'; // ie8
```
具体代码实现方式，请看点击这里 [zrender.js](https://ecomfe.github.io/zrender-doc/public/api.html)。

