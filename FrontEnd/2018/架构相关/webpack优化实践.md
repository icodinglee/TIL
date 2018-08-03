
### 区分开发环境和生产环境
1.开发环境
   - 优化开发体验
   - 尽可能减少构建速度
   
2.生产环境
   - 模块拆包，持久化缓存
   - 尽可能减少打包文件的大小
   - 代码丑化压缩
   - 尽可能减少构建时间
   
### 开发过程
##### 优化开发体验
*自动刷新 -> 模块热更新*
1.实时预览反应更快，等待时间更短
2.不刷新浏览器能保留当前网页的而运行状态

*配置sourcemap*
```
module.exports = {
  devtool: 'source-map',
}
```
方便调试源代码

##### 减少构建时间
在大型应用减少每次构建的时间非常重要。下面的一些方案策略可以提高1-2倍的编译速度。
- 减少模块查找范围，缩小Babel的编译范围，并使用webpack cache缓存模块。
- 使用DLLPlugin预先打包好第三方库。
- 使用Happypack加速构建
- 不使用webpack css模块化方案

1. 缩小babel编译范围，并使用webpack cache缓存模块。
```
module.exports = {
  // 减小模块的查找范围
  resolve: {
    modules: [path.resolve(__dirname, 'node_modules')],
  },
  module: {
    rules: [
      {
        test: /\.js?$/,
        use: [{
          loader: 'babel-loader',
          query: {
            // 将 babel 编译过的模块缓存在 webpack_cache 目录下，下次优先复用
            cacheDirectory: './webpack_cache/',
          },
        }],
        // 减少 babel 编译范围，忘记设置会让 webpack 编译慢上好几倍
        include: path.resolve(__dirname, 'src'),
      },
    ]
  },
}
```

2.使用DLL预先打包好第三方库，避免每次都去编译。开启DLLPlugin需要你配置一份webpack配置。
```
// dll.config.js
const webpack = require('webpack');
const path = require('path');
const DllPlugin = require('webpack/lib/DllPlugin')
const vendors = [
  'react',
  'react-dom',
  'react-router',
  'redux',
  'react-redux',
  'jquery',
  'antd',
  'lodash',
]
module.exports = {
  entry: {
    'dll': vendors,
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'public'),
    library: '__[name]__lib',
  },
  plugins: [
    new DllPlugin({
      name: '__[name]__lib',
      path: path.join(__dirname, 'build', '[name].manifest.json'),
    }),
  ]
}
```
运行则会在public目录下得到dll.js和dll.manifest.json文件，然后需要在开发配置中进行关联。

```
// webpack.config.dev.js
const webpack = require('webpack');

module.exports = webpackMerge(baseConfig, {
  plugins: [
    new DllReferencePlugin({
      manifest: require('./public/dll.manifest.json'),
    }),
  ]
});
```
另外需要在html模板里手动加入dll.js, webpack不会自动引入。速度可以提升一倍。

3.使用happyPack开启多核构建。
webpack之所以这么慢主要是由于有大量文件需要解析和处理，构建是文件读写和计算密集型的操作，特别是文件变多知乎，webpack构建慢的问题就会特别严重。
在整个webpack构建流程中，最耗时的流程可能就是loader对文件的转换操作了，因为要转换的文件数据巨多，而且这些转换操作只能一个个处理。
HappyPack的核心原理就是把这部分任务分解到多个流程中并行处理，从而减少构建时间。

需要配置哪写loader使用HappyPack改写哪配置，如修改babel为多核编译：
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js?$/,
        use: ['happypack/loader?id=babel'],
        include: path.resolve(__dirname, 'src'),
      },
    ]
  },
  plugins: [
    new HappyPack({
      id: 'babel',
      loaders: [{
        loader: 'babel-loader',
        query: {  
          cacheDirectory: './webpack_cache/',
        },
      }],
    })
  ],
};
```
设置 id=babel，webpack 会去找 plugins 中的 id 为 babel 的插件进行处理。不过有些loader不支持多核编译。

4.不要使用webpack里的css模块化方案。（ 这里指的是css-loader提供的模块化方案）
原来的配置
```
module.exports = webpackMerge(baseConfig, {
  module: {
    rules: [
      {
        test: /\.css/,
        use:       [
          'style-loader',
          {
            loader: 'css-loader',
            options: {
              // 开启 css 模块
              modules: true,
              // 设置命名格式
              localIdentName: '[name]__[hash:base64:5]'
            }
          }
        ]
      },
    ]
  },
} 
```
缺点： 
1.类名只能以驼峰式的形式出现，且每个类名需要额外添加到 React 组件当中
2.编译速度慢的坑爹，如果你的应用中有大量的样式(数以万计)需要去解析，编译的时间至少增加一倍以上。

因此如果想要使用css模块化的可以选择其他方案，比如styleComponents或者自己添加命名空间。


### 发布上线
在发布上线的时候需要考虑到性能优化的因素，比如如何有效的利用浏览器缓存，如何减少打包文件的体积等等。
#### 高效的利用浏览器缓存
- 1.在多页面应用中，我们需要将公共模块进行拆包，比如header,footer,以及一些公共区域等等，这样页面在我们的网络中进行跳转的时候由于这些公共模块存在于缓存之中，就可以直接进行加载，而不是再通过网络请求。
- 2.分离业务代码和第三方代码：之所以将业务代码和第三方代码分离出来，是因为业务代码更新频率高，而第三方代码更新迭代速度慢，所以我们将第三方代码（库，框架）进行抽离，这样就可以充分利用浏览器的缓存来加载第三方库。
- 3.从js中抽离css,使得css样式和js逻辑相对独立，这样我们再修改样式或者页面的逻辑的时候他们将互不影响到各自的缓存。
- 4.抽离异步加载的内容，比如路由分割，与首屏渲染无关的内容等。
- 5.生成稳定的hash值，代码修改实现hash值变化最小，即代码修改只影响到其对应文件hash值，而不要去影响其他文件的hash值。

因此，
- 1.对于React.Vue这样整体性偏强的库，可以生成vendor第三方库来去做缓存，因为一般技术体系是固定的，一个站点里面基本上都会用到统一技术体系，所以生成vendor库用于缓存，这部分可以使用DLLPlugin来做。
- 2.像antd、lodash这种功能性组件库，可以通过tree shaking (webpack 在打包的过程中会将没用的代码进行清除) 来进行消除，只保留有用的代码，千万不能直接打包到vendor中去，不然会大量加载执行无用代码。

### 减少打包文件的体积
要想减少打包后的体积，就需要使用到webpack2提供的tree shaking功能和webpack3提供的scope hoisting功能。

### 压缩代码
压缩代码可以使用 UglifyJsPlugin 这个插件对代码进行压缩。
