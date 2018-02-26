# 外部派克 了解一下？（一） 常规配置

### 一些命令

+ npm init 初始化npm,生成package.json
+ npm run dev args  arg可以在webpack中取到 process.argv[2]
+ webpack <打包文件名称> <打包以后的文件名称>
+ output里有个publicPath的配置项，是配置公共路径的，上线时可用
+ webpack --config webpack.conf.dev.js 指定配置文件
+ webpack entry output。 entry可以是多个，output可以是自定义规则
+ 入口文件可以指定多个，为防止文件同名可以用占位符
+ 占位符有3中，[name] 是entry的key, [hash]是hash值, [chunkhash]是chunk的hash值

### babel

- npm i --save-dev babel-loader babel-core babel-preset-env babel-plugin-transform-runtime
- npm i --save babel-polyfill babel-runtime<br>
babel Polyfill  函数和方法的垫片 <br>
babel runtime transform  局部垫片，为开发框架准备<br>
babel-plugin-transform-runtime  在.babelrc里配置plugin<br>
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

### 打包公共代码

- new webpack.optimize.CommonsChunkPlugin(option)
- 必须有2个或以上entry才有用

options.name or options.names  字符串 or 数组<br>
options.filename  打包后的名字<br>
options.minChunks  需要打包的代码出现的最小次数<br>
options.chunks  指定提取公共代码的范围<br>
options.children  是不是在entry的子模块中<br>
options.deepChildren  所有模块中 这两个options不太确定。详情见文档<br>
options.async  创建一个异步的公共代码块<br>

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
### 代码分割和懒加载（一回事儿）

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

### 处理CSS

style-loader 把css变成style标签打入html <br>
css-loader 让js把css当作一个模块，可以import css。<br>
提取css的好处: 可以缓存，加速页面加载<br>

- style-loader 

    options: 
    ```
        insertAt 插入位置
        insertInto 插入到哪个dom中
        singleton 是否只使用一个style标签
        transform: 一个js文件地址  转化，浏览器环境下，插入页面前执行文件中以commonjs方式导出的函数。 感觉没什么用
    ```

- css-loader 

    options: 
    ```
        alias 别名
        importLoader (@import)
        minimize 是否压缩
        modules 是否启用css-modules 
    ```

- 提取css 

```
    extract-loader (小众) 
    ExtractTextWebpackPlugin (主流)
```

```
// 需要在html中写Link
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

### 关于tree-shaking

摇树，在代码中就是把项目中不会执行的代码（没有机会执行的代码）删除

- 使用场景 

```
    常规优化 - 代码体积减少
    引入第三方库的某一个功能
```

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
    options 
```
        paths: glob.sync([])
```

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

### 图片处理

css中引入的图片   file-loader <br>
自动合成雪碧图    postcss-sprites<br>
压缩图片         img-loader<br>
Base64编码       url-loader<br>

- file-loader

```
    {
        test: /\.(png|jpg|jpeg|gif)$/,
        use: [
            {
                loader: 'file-loader',
                options: {
                    publicPath: '',
                    outputPath: 'dist/',
                    useRelativePath: true
                    // useRelativePath should be true if you wish to generate a relative URL to the for each file context.
                    // eg :  useRelativePath: process.env.NODE_ENV === "production"
                    // 按当前路径，打包到输出目录下的相同路径。如果要把不同地方引用的资源打包到一起。就不能设置这个属性
                }
            }
        ]
    }
```

- url-loader

```
    options: {
        // ext 是后缀
        name: '[name]-[hash:5].[ext]',
        limit: 10000,
        publicPath: '',
        outputPath: 'dist/',
        useRelativePath: true
    }
```

- img-loader

```
    // 这个loader的各个参数需要自行百度。
    option: {
        pngquant: {
            quality: 80
        }
    }
```

- postcss-sprites

```
    {
        loader: 'postcss-loader',
        options: {
            // 告诉webpack,下面引入的插件是给postcss用的。
            ident: 'postcss',
            plugins: [
                require('postcss-sprites')({
                    // 设置路径
                    spritePath: 'dist/assets/imgs/sprites',
                    // retina屏不同处理，图片文件加@2x
                    retina: true
                })
            ]
        }
    },
```

### 字体处理

```
    {
        test: /\.(eot|woff2?|ttf|svg)$/,
        use: [
            {
                loader: 'url-loader',
                options: {
                    name: '[name]-[hash:5].[ext]',
                    limit: 5000,
                    publicPath: '',
                    outputPath: 'dist/',
                    useRelativePath: true
                }
            }
        ]
    }
```

### 第三方JS库处理

- webpack.ProvidePlugin 
    key 是alias value是包名（npm install的）

```
// 如下配置，Jquery就可以直接在所有模块中使用$
plugins: [
    new webpack.ProvidePlugin({
        $: 'jquery'
    })
]
```

如果第三方库在本地的一个目录下，就要借助resolve解析路径到node_modules里

```
resolve: {
    alias: {
        // 这里别名叫jquery是为了让下面plugins中能匹配到jquery
        jquery$: path.resolve(__dirname, 'src/libs/jquery.min.js')
    }
},
plugins: [
    new webpack.ProvidePlugin({
        $: 'jquery'
    })
]
```

- imports-loaders

```
    {
        test: path.resolve(__dirname, 'src/app.js'),
        use: [
            {
                loader: 'imports-loader',
                options: {
                    // webpack自动匹配，无论是node_modules里的还是alias里的都行
                    $: 'jquery'
                }
            }
        ]
    }
```

### HTML in webpack

- 生成html
    HtmlWebpackPlugin<br>
    options

    ```
        // 标签页logo的位置
        favicon:'./src/images/favicon.ico',

        // 生成的html名字
        filename:'/resume.html',

        // 模版所在路径
        template:'./src/html/resume.html',
        
        // 压缩生成的html文件
        minify: {
            // 去除空格、换行
            collapseWhitespace: true
        },

        // 是否自动插入css和js 的 link,script到html中
        inject: true,

        // 加载哪些entry chunk(也可以不是entry中的chunk,但要有name)
        chunks: ['vendors', 'resume']
    ```

- html中引入的图片 
    html-loaders <br>
    options <br>

        // [a: b] a是标签，b是属性  
        attrs: ['img:src']

```
    {
        test: /\.html$/,
        use: [
            {
                loader: 'html-loader',
                options: {
                    attrs: ['img:src', 'img:data-src']
                }
            }
        ]
    }
```

- 配合优化 减少HTTP请求

把webpack公共代码打入HTML中<br>

提前载入webpack加载代码 <br>
    inline-manifest-webpack-plugin <br>
    html-webpack-inline-chunk-plugin <br>

```
    var HtmlInlineChunkPlugin = require('html-webpack-inline-chunk-plugin')

    plugins: [
        new Webpack.optimize.CommonsChunkPlugin({
            // 如果name不是entry里的，就会单独提取webpack自己生成的公共代码
            name: 'manifest'
        }),
        new HtmlInlineChunkPlugin({
            // 要插入html中的chunk的名称
            inlineChunks: ['manifest']
        }),
        new htmlWebpackPlugin({          
            favicon:'./src/images/favicon.ico',
            filename:'/resume.html',
            template:'./src/html/resume.html',  
            inject:true,
            chunks: ['vendors', 'resume', 'manifest']
        }),
    ]
```