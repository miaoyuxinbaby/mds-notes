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

##### babel

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
                                        // postcss等其他插件也会用到broswerslist
                                        // 可以写在package.json或者.browserslistrc里面。让所有插件共用（自动查找）
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

##### 打包公共代码

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
        // 分割出来的代码称之为chunk
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
        // 提取异步加载的公共代码
        new webpack.optimize.CommonsChunkPlugin({
            async: 'async-common',
            children: true,
            minChunks: 2
        }),
    ]
}
```
##### 代码分割和懒加载（一回事儿）

- 应用场景<br>
    分离业务代码 和 第三方依赖 <br>
    分离业务代码 和 业务公共代码 和 第三方依赖<br>
    分离首次加载 和 访问后加载的代码

- webpack 内置的methods 和ES 2015 Loader spec 的动态import

- require.ensure  内置方法
```
 // [] : dependencies 加载进来但不会执行，需要在callback中require一次
 // 其实[]里什么依赖都不写也行，因为callback里require的东西会异步加载。
 require.ensure([], callback, [errorCallback], chunkName)

// 同步加载
 import * as _ form 'lodash'
// 异步加载
 require.ensure(['lodash'], () => {
     let _ = require('lodash')
     _.join(['1', '2'], 3)
 }, 'vender')

```
- require.include

在单entry中，如果pageA中 require.ensure subPageA,subPageB。 并且两者都依赖moduleA。 打包的时候, 两者的chunk会都包含moduleA。这样造成资源浪费。<br>
可以在pageA中 require.include('./moduleA')。两者的chunk内都没有moduleA。而是提升到了pageA中。两份变成了一份

- import() 

动态import()不单单是加载进来，它会执行加载的内容
```
// 语法 通过魔法字符串 -- 注释 进行配置。
import(
    /* webpackChunkName: async-chunk-name */
    /* webpackMode: lazy */
    modulename
)

import(/* webpackChunkName: 'subPageA' */'./subPageA')
  .then((subPageA) => {
      console.log(subPageA)
  })
``` 

##### 处理CSS

style-loader 把css变成style标签打入html <br>
css-loader 让js把css当作一个模块，可以import css。<br>
提取css的好处: 可以缓存，加速页面加载<br>

- style-loader <br>
    options: <br>

        insertAt 插入位置<br>
        insertInto 插入到哪个dom中<br>
        singleton 是否只使用一个style标签<br>
        transform: 一个js文件地址  转化，浏览器环境下，插入页面前执行文件中以commonjs方式导出的函数。 感觉没什么用

- css-loader <br>
    options: <br>

        alias 别名<br>
        importLoader (@import)<br>
        minimize 是否压缩<br>
        modules 是否启用css-modules 

- 提取css <br>
    extract-loader (小众)
    ExtractTextWebpackPlugin (主流)

```
需要在html中写Link
var ExtractTextWebpackPlugin = require('Extract-text-webpack-plugin')

module: {
    rules: [
        {
            test: /\.less$/,
            use: ExtractTextWebpackPlugin.extract({
                fallback: {
                    loader: 'style-loader',
                    options: {
                        singleton: true
                    }
                },
                use: [
                    {
                        loader: 'css-loader',
                        options: {
                            minimize: true
                        }
                    },
                    {
                        loader: 'postcss-loader',
                        options: {
                            // 告诉webpack,下面引入的插件是给postcss用的。
                            ident: 'postcss',
                            plugins: [
                                require('autoprefixer')()
                            ]
                        }
                    },
                    {
                        loader: 'less-loader'
                    }
                ]
            })
        }
    ]
},
plugins: [
    new ExtractTextWebpackPlugin({
        filename: '[name].min.css',
        allChunks: false // 默认为false,异步加载的不打包。true则打包
    })
]
```

##### 关于tree-shaking

摇树，在代码中就是把项目中不会执行的代码（没有机会执行的代码）删除

- 使用场景 <br>
    常规优化 - 代码体积减少<br>
    引入第三方库的某一个功能<br>

- js tree shaking

借助Webpack.optimize.UglifyJsPlugin的帮助<br>
第三方库需要支持ES module，比如Lodash本身是一个函数，所以无法删除无用代码。<br>
可以选择lodash-es代替<br>
目前github上关于lodash-es无法被webpack tree shaking 的 issue 还开着，说明有些库的写法webpack不支持<br>
lodash-es可以用babel-plugin-lodash处理无用代码 配置示例如下： <br>

```
    // lodash-es处理
    rules: [
        {
            test: /\.js$/,
            use: [
                {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env'],
                        plugins: ['lodash']
                    }
                }
            ]
        }
    ]

    // 常规配置

    var Webpack = require('webpack')

    plugins: [
        new Webpack.optimize.UglifyJsPlugin()
    ]
```

- css tree shaking

npm i purifycss-webpack glob-all --save-dev<br>

purifycss-webpack <br>
    options <br>
    
        paths: glob.sync([])

```
    var PurifyCss = require('purifycss-webpack')
    var glob = require('glob-all')
    var path = require('path')

    plugins: [
        // PurifyCss要写在ExtractTextWebpackPlugin后面，因为webpack的数组配置是从下往上执行
        // 单独的文件这么配置
        new PurifyCss({
            paths: glob.sync([
                path.resolve(__dirname, './index.html')
            ])
        })
        // 包含很多文件这么配置
        new PurifyCss({
            paths: glob.sync([
                path.join(__dirname, './index.html'),
                path.join(__dirname, './src/*.js')
            ])
        })
    ]
```
