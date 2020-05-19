---
title: Webpack基础入门01
date: 2020-05-19 12:49:41
tags: webpack
categories:
- 前端构建工具
---
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Webpack是一种前端资源构建工具,一个静态模块打包器。当我们在开发过程中使用一些浏览器并不能识别的语言时，比如：在js文件中使用ES Module语法、使用less语法等等，这时我们就需要一个工具将浏览器不能识别的语法编译成浏览器能够识别的语法，在过去我们会把这些语法通过一个个小工具去编译，这样维护起来会很麻烦，所以前端就提出了一个概念，叫做"构建工具"，构建工具的意思就是找一个大的工具将这些小的工具功能都包含进来，现在我们只要关注这个大工具就可以了。而Webpack就是构建工具的一种。Webpack会根据入口文件的依赖关系，将所有的资源(js/json/css/image/less/...)全部引进来，形成chunk代码块，然后会根据不同的资源进行不同的处理，比如将less编译成css，这个过程叫做打包，打包之后输出出去，输出去的文件我们叫做bundle，所以Webpack还是一个静态模块打包器。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;webpack有五个核心概念，分别是：entry、output、loader、plugins、mode
### 安装
```js
// 全局安装，我们可以使用webpack的相关指令
npm i webpack webpack-cli -global
// 本地安装
npm i webpack webpack-cli -D
```
- 运行指令
  - 开发环境：
  ```js
  // webpack会以./src/index.js为入口文件开始打包，打包后输出到./build/build.js
  webpack ./src/index.js -o ./build/build.js --mode=development
  ```
  - 生产环境：
  ```js
  // webpack会以./src/index.js为入口文件开始打包，打包后输出到./build/build.js
  webpack ./src/index.js -o ./build/build.js --mode=production
  ```
- 总结：
  - 生产环境比开发环境多一个压缩js代码
  - webpack能处理js/json，不能处理css/img等其他资源
  - 生产环境和开发环境将ES6模块化编译成浏览器能识别的模块化

### 打包资源文件
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;我们可以在根目录下建一个Webpack的配置文件webpack.config.js，用来指示Webpack做哪些事情（当我们运行webpack指令时，会加载里面的配置）
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;所有构建工具都是基于nodejs平台运行的,模块化默认采用commonjs
- 打包样式资源（webpack.config.js）
```js
// resolve用来拼接绝对路径的方法
const { resolve } = require('path')

module.exports = {
  // webpack配置
  // 入口起点
  entry: './src/index.js',
  // 输出
  output: {
    // 输出文件名
    filname: 'build.js',
    // 输出路径
    // __dirname nodejs的变量，代表当前文件的目录绝对路径
    path: resolve(__diranem,'build')
  },
  // loader的配置
  module: {
    rules: [
      // 详细loader的配置
      // 不同文件必须配置不同loader处理
      {
        // 匹配哪些文件
        test: /\.css/,
        // 使用哪些loader进行处理
        use: [
          // use数组中loader执行顺序：从右到左，从下到上 依次执行
          // 创建style标签，将js中的样式资源插入进去，添加到head中生效
          'style-loader',
          // 将css文件变成commonjs模块加载js中,里面内容是样式字符串
          'css-loader'
        ]
      },
      {
        test: /\.less/,
        use: [
          'style-loader',
          'css-loader',
          // 将less文件编译成css文件
          // 需要下载less-loader less
          'less-loader'
        ]
      }
    ]
  },
  // plugins的配置
  plugins: [
    // 详细plugins的配置
  ],
  // 模式
  mode: 'development' // 开发模式
  // mode: 'production'
}
```
注：以上都需要下载相应的loader，例如：```npm i style-loader css-loader -D```
- 打包html资源
打包html需要引入html-webpack-plugin插件
```js
const { resolve } = require('path')
import HtmlWebpackPlugin from 'html-webpack-plugin'

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname,'build')
  },
  module: {
    rules: [
      // loader配置
    ]
  },
  plugins: [
    // plugins的配置
    // 功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（JS/CSS）
    // 如果需要有结构的html文件，可以通过传参的方式
    new HtmlWebpackPlugin({
      // 复制./src/index.html文件，并自动引入打包输出的所有资源（JS/CSS）
      template: './src/index.html
    })
  ],
  mode: 'development'
}
```
- 打包图片资源
index.html
```html
  <div id="box1"></div>
  <div id="box2"></div>
  <div id="box3"></div>
  <img src="./01.jpg" alt="01">
```
index.less
```css
#box1{
  width: 100px;
  height: 100px;
  background-image: url('./01.jpg');
  background-repeat: no-repeat;
  background-size: 100% 100%;
}
#box2{
  width: 100px;
  height: 100px;
  background-image: url('./02.jpg');
  background-repeat: no-repeat;
  background-size: 100% 100%;
}
#box3{
  width: 100px;
  height: 100px;
  background-image: url('./03.jpg');
  background-repeat: no-repeat;
  background-size: 100% 100%;
}
```
index.js
```js
import './index.less'
```
webpack.config.js
```js
const { resolve } =  require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.less$/,
        // 要使用多个loader处理用use
        use: [
          'style-loader',
          'css-loader',
          'less-loader'
        ]
      },
      {
        // 处理图片资源
        // 处理不了html中img图片
        test: /\.(jpg|png|gif)$/,
        // 使用一个loader
        // 下载 url-loader file-loader
        loader: 'url-loader',
        options: {
          // 图片大小小于8kb,就会被base64处理
          // 优点：减少请求数量（减轻服务器压力）
          // 缺点：图片体积会更大（文件请求速度会更慢）
          limit: 8 * 1024,
          // 问题：因为url-loader默认使用es6模块化解析，而html-loader引入图片是commonjs
          // 解析时会出问题：[object Module]
          // 解决：关闭url-loader的es6模块化，使用commonjs解析
          // 注：目前新版本不会有这个问题
          esModule: false,
          // 给图片进行重命名
          // [hash:10]取图片的hash的前10位
          // [ext]取文件原来扩展名
          name: '[hash:10].[ext]'
        }
      },
      {
        test: /\.html$/,
        // 处理html文件的img图片（负责引入img，从而能被url-loader进行处理）
        loader: 'html-loader'
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development'
}
```
最新版本已经优化了url-loader，不会出现在html文件中直接引入图片解析时显示[object Module]这个问题
- 打包其他资源
其他资源一般指除了html、css、js以外的资源，比如说字体文件资源
```js
const {resolve} = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname,'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader','css-loader']
      },
      // 打包其他资源（除了html/js/css资源以外的其他资源）
      {
        // 排除html/js/css资源
        exclude: /\.(html|js|css)$/,
        loader: 'file-loader',
        options: {
          name: '[hash:10].[ext]'
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development'
}
```
### devServer
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;devServer用来自动化(自动编译，自动打开浏览器，自动刷新浏览器)，只会在内存中编译打包，不会有任何输出。
```js
const {resolve} = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'built.js',
    path: resolve(__dirname,'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader','css-loader']
      },
      // 打包其他资源（除了html/js/css资源以外的其他资源）
      {
        // 排除html/js/css资源
        exclude: /\.(html|js|css)$/,
        loader: 'file-loader',
        options: {
          name: '[hash:10].[ext]'
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development',
  devServer: {
    // 项目构建后路径
    contentBase: resolve(__dirname,'build'),
    // 启动gzip压缩
    compress: true,
    // 端口号
    port: 3000,
    // 自动打开默认浏览器
    open: true
  }
}
```
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;在运行前需要安装webpack-dev-server包，指令为```npm i webpack-dev-server -D```，启动devServer指令为：```npx webpack-dev-server```，由于我们没有全局安装webpack-dev-server，所以要在前面加一个npx。
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;webpack指令会将打包结果输出出去，npx webpack-dev-server指令只会在内存中编译打包，没有输出。