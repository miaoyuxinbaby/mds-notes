# webpack  study from imooc

### 一些命令
+ npm init 初始化npm,生成package.json
+ ls 查看当前目录
+ webpack 打包文件名称 打包以后的文件名称
+ webpack不支持打包css，需要添加loader
+ css-loader 使得webpack可以识别.css文件，style-loader把css-loader处理完的标签插入html
+ 命令行输入 webpack 可以查看其有哪些参数
+ webpack hello.js hello.bundle.js --module-bind 'css=style-loader!css-loader!' --watch 指定这2个文件的loader前缀
+ -- watch 是自动打包
+ module.exports = {} 是common.js的模块化
+ npm install html-webpack-plugin --save-dev 这是webpack插件，用于让html中自动识别打包的文件名
+ 通过在html中嵌入<%= htmlWebpackPlugin.options.参数 %> 来动态渲染插件中传入的配置参数
+ output里有个publicPath的配置项，是配置公共路径的，上线时可用
+ npm install --save-dev babel-loader babel-core   安装babel
+ npm install --save-dev css-loader style-loader
+ npm install postcss-loader --save-dev
+ npm install autoprefixer --save-dev


### webpack配置
+ entry: ''     入口文件名
+ output: {}    输出。可配置path,filename等
+ plugin: []    插件配置
##### 例子
```
    module.exports = {
        entry: __dirname + '/src/script/main.js',
        output: __dirname + '/dist/js',
        filename: 'bundle.js'
    }
```
+ __dirname 是nodejs里的一个全局变量，指向当前执行脚本所在的位置
+ 入口文件可以指定多个，为防止文件同名可以用占位符
```
    module.exports = {
        entry: {
            a: __dirname + '/src/script/main.js',
            b: __dirname + '/src/script/a.js'
        },
        output: {
            path: __dirname + '/dist/js',
            filename: '[name].js'
        }
    }
```
+ 占位符有3中，[name] 是entry的key, [hash]是hash值, [chunkhash]是chunk的hash值
+ 插件的使用
```
    var htmlWebpackPlugin = require('html-webpack-plugin');

    module.exports = {
        entry: {
            a: __dirname + '/src/script/main.js',
            b: __dirname + '/src/script/a.js'
        },
        output: {
            path: __dirname + '/dist',
            filename: 'js/[name].js'
        },
        plugins: [
            new htmlWebpackPlugin({
                template: 'index.html'
            })
        ]
    }
```

### about loader

+ 例子 babel-loader
```
var htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: {
        main: __dirname + '/src/app.js'
    },
    output: {
        path: __dirname + '/dist',
        filename: 'js/[name].bundle.js'
    },
    module: {
        loaders: [
            {             // 每一项都是一条规则
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: __dirname + '/node_modules/',    // 不扫描该目录下的文件
                include: __dirname + '/src/',    // 只扫描该目录下的文件                
                options: {
                    presets: ['latest']     // latest是ES2015以后的都转换
                }
            }   
        ]
    },
    plugins: [
        new htmlWebpackPlugin({
            title: 'this is test',
            filename: 'index.html',
            template: 'index.html',
            inject: 'body'
        })
    ]
}
```
+ postcss-loader  autoprefixer
```
var htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: {
        main: __dirname + '/src/app.js'
    },
    output: {
        path: __dirname + '/dist',
        filename: 'js/[name].bundle.js'
    },
    module: {
        rules: [
            {             // 每一项都是一条规则
                test: /\.js$/,
                loader: 'babel-loader',
                exclude: __dirname + '/node_modules/',    // 不扫描该目录下的文件
                include: __dirname + '/src/',    // 只扫描该目录下的文件
                options: {
                    presets: ['latest']     // latest是ES2015以后的都转换
                }
            },
            {
                test: /\.css$/,
                use: [
                    'style-loader',
                    'css-loader',
                    {
                        loader: 'postcss-loader',
                        options: {
                            plugins: [require('autoprefixer')()]
                        }
                    }
                ]
            }   
        ]
    },
    plugins: [
        new htmlWebpackPlugin({
            title: 'this is test',
            filename: 'index.html',
            template: 'index.html',
            inject: 'body'
        })
    ]
}
```