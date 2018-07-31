
#### 持久化缓存场景应用
- 针对 html 文件：不开启缓存，把 html 放到自己的服务器上，关闭服务器的缓存，自己的服务器只提供 html 文件和数据接口
- 针对静态的 js，css，图片等文件：开启 cdn 和缓存，将静态资源上传到 cdn 服务商，我们可以对资源开启长期缓存，因为每个资源的路径都是独一无二的，所以不会导致资源被覆盖，保证线上用户访问的稳定性。
- 每次发布更新的时候，先将静态资源(js, css, img) 传到 cdn 服务上，然后再上传 html 文件，这样既保证了老用户能否正常访问，又能让新用户看到新的页面。

#### 为什么做持久化缓存
1. 用户使用浏览器第一次访问我们的网站时，该页面引入各种静态资源，如果我们能做到持久还缓存的话，可以在http响应头加上Cache-control或者Expires字段来设置缓存，浏览器可以将这些缓存一一缓存到本地。
2. 用户在后续访问的时候，如果需要再次请求同样的静态资源，且静态资源没有过期，那么浏览器可以直接走本地缓存而不用再通过网络请求资源。

#### webpack如何做持久化缓存
我们需要做到以下两点：
1.保证hash值的唯一性，即为每个打包后的资源生成一个独一无二的hash值，只要打包内容不一致，那么hash值就不一致。
2.保证hash值的稳定性，我们需要做到修改某个模块的时候，只有受影响的打包后文件hash值改变，与该模块无关的打包文件的hash值不变。

hash文件名是实现持久化缓存的第一步，目前webpack有两种计算hash值的方式（[hash]和[chunkhash]）
- hash代表每次webpack在编译的过程中会生成唯一的hash值，在项目中任何一个文件改动后就会被重新创建，然后webpack计算新的hash值。
- chunkhash是根据模块计算出来的hash值，所以某个文件的改动只会影响它本身的hash值，不会影响其他文件。

所以如果我们在一份具有持久化缓存的webpack配置应该是这样的
```
module.exports = {
  entry: __dirname + '/src/index.js',
  output: {
    path: __dirname + '/dist',
    filename: '[name].[chunkhash:8].js',
  }
}
```
事实上，我们在大型多页面应用中，我们需要做更多东西：
1.分离业务代码和第三方代码：之所以将业务代码 和第三方代码分离出来，是因为业务代码更新频率高，而第三方代码更新迭代速度慢，所以我们将第三方代码（库，框架）进行抽离，这样可以充分利用浏览器缓存来加载第三方库。
2.按需加载：比如在使用react-router的时候，当用户需要访问某个路由的时候再去加载对应的组件，那么用户没有必要在一开始的时候就将所有的路由组件加载到本地。
3.在多页面应用中，我们往往可以将公共模块代码进行抽离，比如header\footer,这样页面在进行跳转的时候这些公共模块因为存在于缓存里，就可以直接进行加载，而不是进行网络请求了。


#### 如何解决编译过程中抽离的第三方业务代码chunkhash值稳定
影响chunkhash值变化主要由以下四个部分组成：
1.包含模块的源代码
2.webpack用于启动运行时的runtime代码
3.webpack生成的模块moduleid(包括包含模块id 和被引用的依赖模块id)
4.chunkID

上述四个部分只要任意部分发生变化，生成的分块文件就不一样，缓存也会失效。

##### 1.源代码变化
##### 2.webpack启动运行时候的runtime代码.
例如配置项如下：
```
const path = require('path');
const webpack = require('webpack');
const ExtractTextPlugin = require('extract-text-webpack-plugin'); // 将 css 从打包好的 js 文件中抽离，生成独立的 css 文件
module.exports = {
  entry: {
    pageA: [path.resolve(__dirname, './src/pageA.js')],
    pageB: path.resolve(__dirname, './src/pageB.js'),
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: 'js/[name].[chunkhash:8].js',
    chunkFilename: 'js/[name].[chunkhash:8].js'
  },
  module: {
    rules: [
      {
        // 用正则去匹配要用该 loader 转换的 CSS 文件
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: ["css-loader"]
        })  
      }
    ]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'common',  // 公共模块
      minChunks: 2,
    }),
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      minChunks: ({ resource }) => (
        resource && resource.indexOf('node_modules') >= 0 && resource.match(/\.js$/) // 抽离第三方模块
      )
    }),
    new ExtractTextPlugin({
      filename: `css/[name].[chunkhash:8].css`,
    }),
  ]
}
```

此时，在webpack启动的时候需要执行一些启动代码
```
(function(modules) { 
  window["webpackJsonp"] = function webpackJsonpCallback(chunkIds, moreModules) {
    // ...
  };
  function __webpack_require__(moduleId) {
    // ...
  }
  __webpack_require__.e = function requireEnsure(chunkId, callback) {
    // ...
    script.src = __webpack_require__.p + "" + chunkId + "." + ({"0":"pageA","1":"pageB","3":"vendor"}[chunkId]||chunkId) + "." + {"0":"e72ce7d4","1":"69f6bbe3","2":"9adbbaa0","3":"53fa02a7"}[chunkId] + ".js";
  };
})([]);
```
其中有一行代码每次更新之后都会改变，因为启动代码需要清楚的知道chunkid和chunkhash值的对应关系，这样在异步加载的时候才能正确的拼接出异步js文件的路径。

因此我们需要将这部分代码抽离，避免内置在其他代码中（pageA、pageB）的代码发生改变时chunkhash会发生改变。因此我们需要将这一部分runtime代码单独抽取出来。

```
module.exports = {
  // ...
  plugins: [
    // ...
    // 放到其他的 CommonsChunkPlugin 后面
    new webpack.optimize.CommonsChunkPlugin({
      name: 'runtime',
      minChunks: Infinity,
    }),
  ]
}
```
这相当于告诉webpack帮我把运行时的代码进行抽离，放到单独的文件中。

多生成了一个 runtime.xxxx.js，以后你在改动业务代码的时候，common chunk 的 hash 值就不会变了，取而代之的是 runtime chunk hash 值会变，既然这部分代码是动态的，可以通过 chunk-manifest-webpack-plugin 将他们 inline 到 html 中，减少一次网络请求。

##### 三、webpack 生成的模块 moduleid
```
默认情况下，模块的 id 是这个模块在模块数组中的索引。OccurenceOrderPlugin 会将引用次数多的模块放在前面，在每次编译时模块的顺序都是一致的，如果你修改代码时新增或删除了一些模块，这将可能会影响到所有模块的 id。
```
最佳实践方案是通过 HashedModuleIdsPlugin 这个插件，这个插件会根据模块的相对路径生成一个长度只有四位的字符串作为模块的 id，既隐藏了模块的路径信息，又减少了模块 id 的长度。

这样一来，改变 moduleId 的方式就只有文件路径的改变了，只要你的文件路径值不变，生成四位的字符串就不变，hash 值也不变。增加或删除业务代码模块不会对 moduleid 产生任何影响。

```
module.exports = {
  plugins: [
    new webpack.HashedModuleIdsPlugin(),
    // 放在最前面
    // ...
  ]
}
```


-
