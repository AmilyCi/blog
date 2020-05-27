---
title: Webpack基础入门02
date: 2020-05-19 23:43:27
tags: webpack
categories:
- 前端构建工具
---
### 开发环境
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Webpack 可以配置两种环境模式，即：```开发环境```和```生产环境```。开发环境是 能让代码本地调试运行的环境，生产环境是 能让代码优化上线运行的环境。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;我们写的源代码里面包含了 es6 语法, less 文件要经过webpack进行编译处理,比如将 less 文件编译成 css 文件，把 es6 语法编译成浏览器能够识别的语法，然后输出的 bundle 这个资源文件就可以被浏览器运行了，为了让开发者更轻松，这里还添加了一些自动化工作，比如自动打开浏览器，自动刷新，自动编译等等一些咧操作，让我们的效率更高，这就是开发环境所做的事情。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;我们的样式在之前处理是整合到js中的，如果让样式在 js 中，会让 js 体积变得很大，下载的很慢，同时，这里会先加载js，再通过创建```<style>```标签，会出现闪屏的现象，所以我们要将css从js文件中提取出来，还有代码压缩，兼容性处理都可以让带吗更快，性能更好，能够平稳的运行，这样我们要做的事情会很多，如果把这些都放在开发环境下，那开发环境要做的事情就太多了，打包速度就会很慢，不利于我们开发，所以这些事情我们要放在生产环境上去做。

### 提取css成单独文件
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;这里需要用到一个新的插件 ```miniCssExtractPlugin```，引用这个插件之后打包出来的样式是以 ```<link>```  引入，而不是 ```<style>``` 标签，所以不会出现闪屏现象，并且 css 文件和 js 文件分割开了，js 文件的体积也没有那么大，解析速度会更好一些。

webpack.config.js
```js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const miniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    // 创建style标签，将样式放入
                    // 'style-loader', 
                    // 这个loader取代style-loader。作用：提取js中的css成单独文件
                    miniCssExtractPlugin.loader,
                    // 将css整合到js文件中
                    'css-loader'
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        new miniCssExtractPlugin({
            // 对输出的css文件进行重命名
            filename: 'css/built.css'
        })
    ],
    mode: 'development'
}
```
目录文件

![](./webpack02/catalogue.jpeg)

打包输出的 index.html 文件
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
<link href="css/built.css" rel="stylesheet"></head>
<body>
    <div id="box1"></div>
    <div id="box2"></div>
<script src="js/built.js"></script></body>
</html>
```