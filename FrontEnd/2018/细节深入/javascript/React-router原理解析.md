#### 为什么需要路由系统

最开始的时候网页都是多页面的，后来随着ajax技术的出现，才慢慢有了React,vue这种单页面应用框架，不过缺少路由系统的spa框架也有其存在的弊端。
1.用户在使用过程中，url不会发生变化，那么用户在进行多次跳转之后，如果一不小心刷新了页面，又回到了最开始的状态，用户体验极差。
2.由于缺乏路由，不利于seo,搜索引擎进行收录。

主流的前端路由是通过hash或者history来实现的。

#### hash路由
url上的hash以#开头，原本是为了作为锚点，方便用户在文章导航到相应的位置，因为hash值的改变而会引起页面的刷新，因此可以采用hash值来做单页面应用的路由，并且当url的hash发生变化的时候，可以触发相应的hashchange回调函数。

所以我们可以写一个Router对象，代码如下：

```
class Router {
  constructor(){
    this.routes = {};
    this.currentUrl = '';
  }
  route(path, callback){
    this.routes[path] = callback || function() {};
  }
  updateView() {
    this.currentUrl = location.hash.slice(1) || '';
    this.routes[this.currentUrl] && this.routes[this.currentUrl]();
  }
  init(){
    window.addEventListener('load', this.updateView.bind(this), false);
    window.addEventListener('hashchange', this.updateView.bind(this), fasle)
  }
}

```
1.routes用来存放不同路由对应的回调函数
2.init用来初始化路由，在load事件发生之后刷新页面，并且绑定hashchange事件，当hash值改变时触发对应的回调函数。
```
<div id="app">
  <ul>
    <li>
      <a href="#/">home</a>
    </li>
    <li>
      <a href="#/about">about</a>
    </li>
    <li>
      <a href="#/topics">topics</a>
    </li>
  </ul>
  <div id="content"></div>
</div>
const router = new Router();
router.route('/', function() {
   // 这里可以对视图进行更新操作
});
router.route('/about', function() {
  // 这里可以对视图进行更新操作
});

```

在对应 html 中，只要将所有链接路径前加 # 即可做成软路由，而不去触发刷新页面。

值得注意的是在第一次进入页面的时候，需要触发一次onhashchange事件，保证页面能够正常显示，用hash在做路由跳转的好处在于简单实用，便于理解，但是它虽然解决单页面应用路由控制的问题，但是url缺引入#号，不够美观。

#### History路由

history路由是基于html5规范，在html5规范中提供了history.pushState || history.replaceState来进行路由控制。

当你执行history.pushState({}, null, '/about')时候，页面url会从http://xxx/跳转到hhtp://xxxx/about 可以在改变url的同时，并不会刷新页面。

pushState的参数说明：
1.state: 存储JSON字符串，可以用在popstate事件中
2.title: 现在大多数浏览器忽略了这个参数，直接用null
3.url：任意有效的URL,用于更新浏览器的地址栏

这么说下来history也有着控制路由的能力，hash的改变可以触发onhashhistory事件，而history的改变并不会触发任何事件，这让我们无法直接去监听history的改变而做出相应的改变。

因此我们需要换个思路，把所有可能触发history改变的情况罗列出来，并将这些方式进行一一拦截监听。
可以触发的条件有：
1.点击浏览器的前进或者后退按钮
2.点击a标签
3.在js代码中触发history.push(replace)State函数

只要对上述三种情况进行拦截，就可以变相监听到history的改变而做出调整。
HTML5规范中有相应的onpopState事件，通过它可以监听到前进或者后退按钮。值得注意的是，调用history.push(replace)State并不会触发onpopstate事件。
因此，可以实现一个简单的路由系统
```
class Router {
  constructor() {
     this.routes = {};
     this.currentUrl = '';
  }
  route(path, callback) {
    this.routes[path] = callback || function() {};
  }
  updateView(url){
    this.currentUrl = url;
    this.routes[this.currentUrl] && this.routes[this.currentUrl]();
  }
  bindLink(){
    const allLink = document.querySelectorAll('a[data-href]');
    for(let i = 0, len = allLink.length; i < len; i++){
      const current = allLink[i];
      current.addEventListener('click', e=>{
        e.preventDefault();
        const url = current.getAttribute('data-href');
        history.pushState({}, null,url);
        this.updateView(url);
      }, false)
    }
  }
  init(){
    this.bindLink();
    window.addEventListener('popState', e=>{
      this.updateView(window.location.pathname)
    });
    window.addEventListner('load', ()=> this.updateView('/'), false)
  }
}
```
router跟之前Hash路由很像，不同的地方在于init初始化函数，首先需要获取所有特殊的链接标签，然后监听点击事件，并阻止默认行为,触发history.pushState以及更新相应的视图。

另外绑定popState事件，当用户点击前进或者后退按钮的时候，能够及时更新视图，另外当刚进去页面的时候也会触发一次视图更新。

修改相应的html内容：
```
<div id="app">
  <ul>
    <li><a data-href="/" href="#">home</a></li>
    <li><a data-href="/about" href="#">about</a></li>
    <li><a data-href="/topics" href="#">topics</a></li>
  </ul>
  <div id="content"></div>
</div>
<script src="js/router.js"></script>
<script>
  const router = new Router();
  router.init();
  router.route('/', function() {
    document.getElementById('content').innerHTML = 'Home';
  });
  router.route('/about', function() {
    document.getElementById('content').innerHTML = 'About';
  });
  router.route('/topics', function() {
    document.getElementById('content').innerHTML = 'Topics';
  });
</script>
```
跟以前的html一样，区别在于用data-href来表示要实现软路由的链接标签。

#### 基于 React 的 Hash 路由系统
思路： 当path匹配上路由时则显示component,匹配不上不显示，如果没有path字段则默认一直显示，而exact字段则表示必须要完全匹配，避免像path='/'匹配上path='/about'这样的情况出现。
所谓的局部刷新，本质在于：当路由发色变化的时候，跟当前url匹配的component正常渲染，跟当前url不匹配的component渲染为null.

**Link组件**
```
export class Link extends Component {
    render() {
        const {to, children} = this.props;
        return <a href={`${to}`}>{this.props.children}</a>
    }
}
```
**Route组件**
```
export class Route extends Component{
    componentWillMount(){
      window.addEventListener('hashchange', this.updateView, fasle);
    }
    componentWillUnmount(){
      window.removeEventListener('hashchange', this.updateView, false);
    }
    updateView = ()=>{
      this.forceUpdate();
    }
    render(){
      const { path, exact, componnet } = this.props;
      const match = matchPath(window.location.hash, {exact, path})；
      if (!match) {
         return null;
      }
      if (component) {
        return React.createElement(component, { match });
      }
      return null;
    }
}
```

































































-
