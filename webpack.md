# webpack  study from imooc

### 一些命令
+ npm init 初始化npm,生成package.json
- npm run dev args  arg可以在webpack中取到 process.argv[2]
+ webpack <打包文件名称> <打包以后的文件名称>
+ output里有个publicPath的配置项，是配置公共路径的，上线时可用
- webpack --config webpack.conf.dev.js 指定配置文件
- webpack entry output entry可以是多个，output可以是自定义规则

### webpack配置

+ 入口文件可以指定多个，为防止文件同名可以用占位符
+ 占位符有3中，[name] 是entry的key, [hash]是hash值, [chunkhash]是chunk的hash值

###### babel

- npm i --save-dev babel-loader babel-core babel-preset-env babel-plugin-transform-runtime
- npm i --save babel-polyfill babel-runtime
babel Polyfill 函数和方法的垫片 <br>
babel runtime transform 局部垫片，为开发框架准备<br>
babel-plugin-transform-runtime在.babelrc里配置plugin<br>
```
    module: {
        rules: [
            {
                test: /\.js$/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            [
                                'babel-preset-env',
                                {
                                    targets: {
                                        browsers: ['> 1%', 'last 2 versions']
                                        // 浏览器市场占比1%以上的
                                    }
                                }
                            ]
                        ]
                    }
                }
            }
        ]
    }
```

###### 打包公共代码
- new webpack.optimize.CommonsChunkPlugin(option)
- 必须有2个或以上entry才有用

options.name or options.names 字符串 or 数组
options.filename 打包后的名字
options.minChunks 需要打包的代码出现的最小次数
options.chunks 指定提取公共代码的范围
options.children 是不是在entry的子模块中
options.deepChildren 所有模块中 这两个options不太确定。详情见文档
options.async 创建一个异步的公共代码块
```
module.export = {
    entry: {
        'pageA': './src/pageA',
        'pageB': './src/pageB',
        'vender': ['lodash']
    },
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: '[name].bundle.js',
        chunkFilename: '[name].chunk.js'
    },
    plugins: [
        // 将第三方依赖和webpack生成的代码区分开
        new webpack.optimize.CommonsChunkPlugin({
            names: ['vender', 'manifest'],
            minChunks: Infinity
        }),
        // 提取业务代码公共部分
        new webpack.optimize.CommonsChunkPlugin({
            name: 'common',
            minChunks: 2,
            chunks: ['pageA', 'pageB'] // 不给定chunks范围会报错
        }),
    ]
}
```

#
