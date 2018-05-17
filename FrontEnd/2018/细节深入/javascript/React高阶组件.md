### react 高阶组件

高阶组件听起来很高大上，其实很简单，就是就是一个方法，这个方法返回了一个包含原组件的新组件

最简单的高阶组件
```
function hoc(Component) {
    return class HOC extends React.Component {
       componentDidMount(){
         console.log("fatherdid")
      }
      componentWillMount(){
         console.log("fatherwill")
      }
       render() {
            return <Component />  //渲染被包裹的组件，这里可以进行渲染劫持
       }
    }
}


class Hello extends React.Component {
      componentDidMount(){
        console.log("sondid")
      }
      componentWillMount(){
         console.log("fatherwill")
      }
    render() {
        return <div> hello world</div>
    }
}

export default  hoc(Hello);    //返回新组件，这个地方的写法是不是和Radium 一样呢？

//上面依次输出 fatherwill  sonwill sondid  fatherdid
```


**高阶组件可以用来操纵props**

你可以在高阶组件中对ComponentClass中的props进行读取、添加、编辑操作。

```
//hoc.js
var getData = function (url) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve(url);
        }, 1000);
    })
};

import React from 'react'
import 'babel-polyfill'  //解决async 和await语法不识别问题

const Hoc = (url) => (WarrapComponent) => (
  class extends React.Component{
      constructor(){
        super()
        this.state = {
          content: ""
        }
      }
      
      async _loadData(){
       this.setState({ content: "loading...."})
       const content =  await getData(url)  //await后面必须是一个异步函数
       this.setState({ content })
     }

     render(){
       const props = {
         content: this.state.content,
         refresh: this._loadData.bind(this),
         ...this.props     //解构赋值真好用！！
       }
       return <WarrapComponent {...props} />  //处理外围新组件传递进来的props，我们可以进行处理，然后把新的props传递到新的组件中去
     }
  }
)

export default Hoc

//Hello.js
import React from 'react'
import Hoc from './Hoc'

class Hello extends React.Component {
  render () {
    return (
      <div>
        <p>{this.props.content}</p>
        <div>{this.props.name}</div>
        <button onClick={() => this.props.refresh()}>刷新</button> 
      //refresh方法放在Hoc之中，这个方法不同样可以被其他被Hoc包裹的
      //组件中调用吗？  可以替代 Minix(react通用方法管理解决方案) 了~
      </div>
    )
  }
}


export default Hoc("/get")(Hello)

```

**Inheritance Inversion  继承反转（获取state）**

上面获取props例子写的是props 在父组件进行处理之后拿到子组件进行渲染，下面这个例子讲的是**如何拿到子组件的state值**

```
function hoc(Component) {
    return class HOC extends Component {  //直接继承被包裹组件
        componentDidMount() {
            console.log(this.state);   //可以拿到被包裹组件的state的值
        }
        render() {
            return super.render();  
        }
    }
}

@hoc
export default class Hello extends React.Component {
    state = {
        id: 1,
        value: "jack"
    };
    render() {
        return <div>Hello </div>
    }
}

你也可以通过super.[xxxx]来调取Hello 的声明周期或在其中定义的函数
```

**Inheritance Inversion渲染劫持**
之所以被称为渲染劫持是因为 HOC 控制着 WrappedComponent 的渲染输出，可以用它做各种各样的事
- 在由 render输出的任何 React 元素中读取、添加、编辑、删除 prop
- 读取和修改由 render 输出的 React 元素树
- 有条件地渲染元素树
- 把样式包裹进元素树（就像在 Props Proxy 中的那样）

```
function iiHOC(WrappedComponent) {
  return class Enhancer extends WrappedComponent {
    render() {
      if (this.props.loggedIn) {
        return super.render()
      } else {
        return null
      }
    }
  }
}
```

***通过 refs 获取组件实例***
你可以通过引用（ref）访问到 this （WrappedComponent 的实例），但为了得到引用，WrappedComponent 还需要一个初始渲染，意味着你需要在 HOC 的 render 方法中返回 WrappedComponent 元素，让 React 开始它的一致化处理，你就可以得到 WrappedComponent 的实例的引用。


```
function refsHOC(WrappedComponent) {
  return class RefsHOC extends React.Component {
    proc(wrappedComponentInstance) {
        console.log(wrappedComponentInstance)  //组件实例
        //在这里都可以调用实例的方法
    }

    render() {
      const props = Object.assign({}, this.props, {ref: this.proc.bind(this)})
      return <WrappedComponent {...props}/>
    }
  }
}
```
 Ref 的回调函数会**在 WrappedComponent 渲染时执行**，你就可以得到 
WrappedComponent 的引用。这可以用来读取/添加实例的 props ，实例的方法也都可以被调用
