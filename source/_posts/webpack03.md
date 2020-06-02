---
title: webpack03
date: 2020-05-31 19:03:18
tags: webpack
categories:
- 前端构建工具
---
### eslint检查js

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;如果我们好多人开发一个项目，每个人有自己一套代码规范，这样会造成代码混乱，不宜维护。而 eslint 可以解决这个问题，让开发者使用同一套代码规范。

webpack.config.js
```js
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'js/built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [
            /*
                语法检查：eslint-loader eslint
                注意：只检查自己写的源代码，第三方的库是不用检查的。
                设置检查规则：
                    package.json中eslintConfig中设置
                        "eslintConfig":{
                            "extends": "airbnb-base"
                        }

                    https://github.com/airbnb/javascript
                    airbnb   -->  eslint-config-airbnb-base eslint eslint-plugin-import
            */
           {
               test: /\.js$/,
               exclude: /node_modules/,
               loader: 'eslint-loader',
               options: {
                   // 自动修复eslint的错误
                   fix: true
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
需要 ```npm i eslint-loader eslint eslint-config-airbnb-base eslint-plugin-import```

package.json
```json
{
  "name": "webpack_code",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "css-loader": "^3.5.3",
    "eslint": "^7.1.0",
    "eslint-config-airbnb-base": "^14.1.0",
    "eslint-loader": "^4.0.2",
    "eslint-plugin-import": "^2.20.2",
    "file-loader": "^6.0.0",
    "html-loader": "^1.1.0",
    "html-webpack-plugin": "^4.3.0",
    "less": "^3.11.1",
    "less-loader": "^6.1.0",
    "mini-css-extract-plugin": "^0.9.0",
    "optimize-css-assets-webpack-plugin": "^5.0.3",
    "postcss-loader": "^3.0.0",
    "postcss-preset-env": "^6.7.0",
    "style-loader": "^1.2.1",
    "url-loader": "^4.1.0",
    "webpack": "^4.43.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.11.0"
  },
  "browserslist": {
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ],
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ]
  },
  "eslintConfig":{
    "extends": "airbnb-base"
  }
}
```
接下来我们可以试验一下，在 js 中写一些不规范的代码

index.js
```js
function add(a, b){
  return a+b;
}
console.log(add(2,3))
```
之后运行webpack，我们再来看一下 index.js 文件

```js
function add(a, b) {
  return a + b;
}
console.log(add(2, 3));
```
所有的代码都规范了，但是，在控制台有一个警告，建议我们不要在这里写 console.log
![](./webpack03/warn.png)

我们再来修改一下这个 index.js 文件吧

```js
function add(a, b) {
  return a + b;
}

// 下一行eslint所有规则都失效（下一行不进行eslint检查）
// eslint-disable-next-line
console.log(add(2, 3));
```

现在再运行 webpack 就不会有任何警告了

