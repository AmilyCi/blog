---
title: webpack04
date: 2020-06-08 01:43:01
tags: webpack
categories:
- 前端构建工具
---
### webpack开发环境的性能优化
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;开发环境下的优化可以分为两方面：优化开发环境下打包构建速度、优化代码调试。

- 优化打包构建速度（HMR）

    HMR 即 hot module replacement 热模块替换 / 模块热替换，它的作用是，一个模块发生变化，只会重新打包这一个模块，而不是打包所有模块，可以极大的提升构建速度。只需要在 devServer 中配置 ``` hot: true```即可。

    对于css 来说，style-loader 已经内部实现了这一个功能；而 js 是默认不能使用 HMR 这个功能的，需要修改 js 代码，添加支持HMR功能的代码，但是要注意的是，HMR 对于 js 来说只能处理非入口 js 文件的其他文件；html 也是默认不能使用 HMR 这个功能，但是 html 也不需要做这个功能，因为我们只有一个 index.html文件，所以一旦更新了，势必要所有的模块都要重新打包，但是 HMR 会导致另一个问题，就是 html 不能热更新了，想要解决这个问题就要修改我们的入口，将html文件引入。

    webpack配置
    ```js
    /*
    HMR：hot module replacement 热模块替换 / 模块热替换
        作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块）
        极大提升构建速度

        样式文件：可以使用HMR功能，因为style-loader内部实现了
        js文件：默认不能使用HMR功能 --> 需要修改js代码，添加支持HMR功能的代码
        注意：HMR功能对于js来说只能处理非入口js文件的其他文件
        html文件：默认不能使用HMR功能。同时会导致问题：html文件不能热更新了(不需要做HMR功能)
        解决：修改entry入口，将Html文件引入
    */


    const { resolve } = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
        entry: ['./src/js/index.js', './src/index.html'],
        output: {
            filename: 'js/built.js',
            path: resolve(__dirname,'build')
        },
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: ['style-loader','css-loader']
                },
                {
                    test: /\.less$/,
                    use: ['style-loader','css-loader','less-loader']
                },
                {
                    test: /\.(jpg|png|gif)$/,
                    loader: 'url-loader',
                    options: {
                        limit: 8 * 1024,
                        esModule: false,
                        name: '[hash:10].[ext]',
                        outputPath: 'imgs'
                    }
                },
                {
                    exclude: /\.(html|js|css|less|jpg|png|gif)/,
                    loader: 'file-loader',
                    options: {
                        name: '[hash:10].[ext]',
                        outputPath: 'media'
                    }
                },
                {
                    test: /\.html$/,
                    loader: 'html-loader'
                }
            ]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: "./src/index.html"
            })
        ],
        mode: 'development',
        devServer: {
            contentBase: resolve(__dirname,'build'),
            compress: true,
            port: 3000,
            open: true,
            // 开启HMR功能
            // 当修改了webpack配置，新配置要想生效，必须重启webpack服务
            hot: true
        }
    }
    ```
    index.js中的代码
    ```js
        import '../css/iconfont.css'
        import '../css/index.less'
        import print from './print'

        console.log('js被重新加载了')

        print()

        function add(x,y){
            return x+y
        }
        console.log(add(1,2))

        if(module.hot){
            // 一旦module.hot为true,说明开启了HMR功能 --> 让HMR工能生效
            module.hot.accept('./print.js', function(){
                // 方法会监听print.js文件的变化，一旦发生变化，其他模块不会重新打包构建，
                // 会执行后面的回调函数
                print()
            })
        }
    ```

- source-map

    source-map 一种提供源代码到构建后代码映射技术（如果构建后代码出错了，通过映射可以追踪源代码错误），它的组合方式为``` [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map```，在 devtool 中设置即可
    ```js
    const { resolve } = require('path')
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    module.exports = {
        entry: ['./src/js/index.js', './src/index.html'],
        output: {
            filename: 'js/built.js',
            path: resolve(__dirname,'build')
        },
        module: {
            rules: [
                {
                    test: /\.css$/,
                    use: ['style-loader','css-loader']
                },
                {
                    test: /\.less$/,
                    use: ['style-loader','css-loader','less-loader']
                },
                {
                    test: /\.(jpg|png|gif)$/,
                    loader: 'url-loader',
                    options: {
                    limit: 8 * 1024,
                    esModule: false,
                    name: '[hash:10].[ext]',
                    outputPath: 'imgs'
                    }
                },
                {
                    exclude: /\.(html|js|css|less|jpg|png|gif)/,
                    loader: 'file-loader',
                    options: {
                    name: '[hash:10].[ext]',
                    outputPath: 'media'
                    }
                },
                {
                    test: /\.html$/,
                    loader: 'html-loader'
                }
            ]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: "./src/index.html"
            })
        ],
        mode: 'development',
        devServer: {
            contentBase: resolve(__dirname,'build'),
            compress: true,
            port: 3000,
            open: true,
            hot: true
        },
        devtool: 'inline-source-map'
    }
    /*
    source-map: 一种提供源代码到构建后代码映射技术(如果构建后代码出错了，通过映射可以追踪源代码错误)

    [inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map
    
    source-map：外部
        错误代码的准确信息 和 源代码的错误位置
    inline-source-map：内联
        只生成一个内联source-map
        错误代码的准确信息 和 源代码的错误位置
    hidden-source-map：外部
        错误代码错误原因，但是没有错误位置
        不能追踪源代码错误，只能提示到构建后代码的错误位置
    eval-source-map：内联
        每一个文件都生成对应的source-map，都在eval
        错误代码的准确信息 和 源代码的错误位置
    nosources-source-map：外部
        错误代码的准确信息，但是没有任何源代码信息
    cheap-source-map：外部
        错误代码的准确信息 和 源代码的错误位置
        只能精确到行
    cheap-module-source-map：外部
        错误代码的准确信息 和 源代码的错误位置
        module会将loader的source map加入

    内联和外部的区别：1.外部生成了文件，内联没有。2.内联构建速度更快

    开发环境：速度快，调试更友好
        速度快（eval>inline>cheap>...）
        eval-cheap-source-map
        eval-source-map
        调试更友好
        source-map
        cheap-module-source-map
        cheap-source-map
        --> eval-source-map / eval-cheap-module-source-map
    生产环境：源代码要不要隐藏，调试要不要更友好
        内联会让代码体积变大，所以在生产环境下不用内联
        nosources-source-map 全部隐藏
        hidden-source-map 只隐藏源代码，会提示构建后代码错误信息
        --> source-map / cheap-module-source-map

    */
    ```

- oneOf 提升构建速度

    正常情况下，我们文件匹配 loader，会把所有的 loader 配置都走一遍，这样会大大降低我们的构建速度，所以我们这时候可以选 oneOf，一旦匹配到就不会继续向下匹配，但是有的文件需要匹配多个 loader，我们可以把其它的 loader 提取出来就可以了

    ```js
    const {resolve} = require('path')
    const MiniCssExtractPlugin = require('mini-css-extract-plugin')
    const OptimizeCssAssetsWebpackPlugin = require('optimize-css-assets-webpack-plugin')
    const HtmlWebpackPlugin = require('html-webpack-plugin')

    process.env.NODE_ENV = 'production'

    // 复用loader
    const commonCssLoader =  [
        MiniCssExtractPlugin.loader,
        'css-loader',
        {
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
                    test: /\.js$/,
                    exclude: /node_module/,
                    enforce: 'pre',
                    loader: 'eslint-loader',
                    options: {
                        fix: true
                    }
                },
                {
                    // 以下loader只会匹配一个
                    // 注意：不能有两个配置处理同一类型文件
                    // 提升构建速度，让我们这个文件以后不会反复的被多个loader都去过一遍
                    oneOf: [
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
                        {
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
                        {
                            test: /\.html$/,
                            loader: 'html-loader'
                        },
                        {
                            exclude: /\.(js|css|html|less|jpg|png|gif)/,
                            loader: 'file-loader',
                            options: {
                                outputPath: 'media'
                            }
                        }
                    ]
                }
            ]
        },
        plugins: [
            new MiniCssExtractPlugin({
                filename: 'css/built.css'
            }),
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