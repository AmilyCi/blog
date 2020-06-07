---
title: webpack04
date: 2020-06-08 01:43:01
tags: webpack
categories:
- 前端构建工具
---
### 开发环境的配置

```js
const {resolve} = require('path')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

// 定义nodejs的环境变量：绝决定使用browserslist的哪个环境
process.env.NODE_ENV = 'production'

// 复用loader
const commonCssLoader =  [
    MiniCssExtractPlugin.loader,
    'css-loader',
    {
        // 还需要在package.json中定义browserslist
        loader: 'postcss-loader',
        options: {
            ident: 'postcss',
            plugins: () => [
                require('postcss-preset-env')()
            ]
        }
    }
]

module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'js/built.js',
        path: resolve(__dirname,'build')
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [...commonCssLoader]
            },
            {
                test: /\.less$/,
                use: [
                    ...commonCssLoader,
                    'less-loader'
                ]
            },
            // js语法检查
            /* 正常来讲： 一个文件只能被一个Loader处理。
               当一个文件要被多个loader处理，那么一定要指定loader的执行先后顺序：
                  先执行eslint，再执行babel
            */
            {
                // 在package.json中eslintConfig --> airbnb
                test: /\.js$/,
                exclude: /node_module/,
                // 优先执行
                enforce: 'pre',
                loader: 'eslint-loader',
                options: {
                    fix: true
                }
            },
            // js兼容性处理
            {
                // 在package.json中eslintConfig --> airbnb
                test: /\.js$/,
                exclude: /node_module/,
                loader: 'babel-loader',
                options: {
                    presets: [
                        '@babel/preset-env',
                        {
                            useBuiltIns: 'usage',
                            corejs: {version: 3},
                            targets: {
                                chrome: '60',
                                firefox: '50'
                            }
                        }
                    ]
                }
            },
            {
                test: /\.(gif|jpg|png)/,
                loader: 'url-loader',
                options: {
                    limit: 8 * 1024,
                    name: '[hash:10].[ext]',
                    outputPath: 'imgs',
                    esModule: false
                }
            },
            // 解析html中的图片
            {
                test: /\.html$/,
                loader: 'html-loader'
            },
            // 其他所有的文件处理
            {
                exclude: /\.(js|css|html|less|jpg|png|gif)/,
                loader: 'file-loader',
                options: {
                    outputPath: 'media'
                }
            }
        ]
    },
    plugins: [
        new MiniCssExtractPlugin({
            filename: 'css/built.css'
        }),
        // 压缩css
        new OptimizeCssAssetsWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: './src/index.html',
            minify: {
                collapseWhitespace: true,
                removeComments: true
            }
        })

    ],
    mode: 'production'
}
```
