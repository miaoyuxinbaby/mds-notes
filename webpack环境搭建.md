# 外部派克 了解一下？(二) -- 搭建开发环境

### 目录

webpack watch mode 实时编译，但是需要自己另外搭建server <br>
webpack-dev-server 主流（各大cli使用方式）<br>
express/koa等框架 + webpack-dev-middleware 更加灵活多变，但配置成本高<br>

### webpack watch mode

webpack -watch [参数] || webpack -w [参数] <br>

--progress 可以看到打包过程<br>
--display-reasons 显示原因 <br>
--color 彩色显示<br>

```
  var CleanWebpackPlugin = require('clean-webpack-plugin')

  plugins: [
    // 每次打包前删除dist目录
    new CleanWebpackPlugin(['dist'])
  ]
```

### webpack-dev-server

- 功能：

    live reloading （类似于刷新浏览器）<br>
    打包文件？ NO 不能用来打包文件，临时打包运行的文件存在于内存中。<br>
    路径重定向 可以吧本地html路径修改为类似于服务器的路径<br>
    支持https<br>
    浏览器中显示编译错误<br>
    接口代理<br>
    模块热更新(HMR - hot module refresh好像是这么拼写的。。。。) 不刷新浏览器<br>
  
- devServer<br>
  npm i webpack-dev-server<br>
  下载后在 node_modules/.bin/目录下运行webpack-dev-server --open运行<br>
  也可以写成npm脚本 "脚本名": "webpack-dev-server --open"<br>
  

  配置项 <br>

    inline devSever的编译信息是显示在页面上还是控制台<br>
    contentBase  提供静态文件的路径，默认为公共目录 <br>
    port  端口<br>
    historyApiFallback 配置路由，某路径请求某资源 可以设置为布尔值或者option Object<br>
    https 是否开启HTTPS<br>
    proxy 代理<br>
    hot 是否开启模块热更新<br>
    openpage 是否打开浏览器<br>
    lazy  资源懒加载（多页面尤其有用）<br>
    overlay 是否把编译错误显示在模态层<br>

  ```
  module.export = {
    entry: {
      app: './src/app.js'
    },
    .....
    ,
    devServer: {
      port: 9001,
      inline: true,
      historyApiFallback: {
        rewrites: [
          {
            form: '/pages/a', // 也可以写正则
            to: '/pages/a.html'
          }
        ]
      }
    }
  }
  ```

- 代理远程接口请求

    主流有2种，且配置基本一致  http-proxy-middleware 和 devServer.proxy <br>
    options: 
    ```
      target: 指定代理的地址
      changeOrigin: 默认是false
      headers: {} 增加http请求头
      logLevel： 在控制台显示代理信息
      pathRewrite 帮助重定向接口的请求和apifallback很相似
    ```

  ```
  devServer: {
    prot: 9001,
    proxy: {
      '/': {
        target: 'www.XXX.com',
        changeOrigin: true,
        logLevel: 'debug',
        pathRewrite: {
          '^/comments': '/api/comments'
          // 把a映射成b
        },
        headers: {
          'cookie': 'abcdefghigklmn'
        }
      }
    }
  }
  ```

- Module Hot Reloading

  在不刷新浏览器的情况下更新前端代码
  - 三个优势
    - 保持应用的数据状态
    - 节省调试时间
    - 样式调试更快
  - 模块热更新的css部分，是依赖style-loader。所以dev环境中一般不抽离出单独的css文件

```
devServer: {
  hot: true
},
plugins: [
  new Webpack.HotModuleReplacementPlugin(),
  new Webpack.namedModulesPlugin()
]
```

- Source Map调试

devtool 有7个常用值，4个用于开发环境

  - Development
    - eval
    - eval-source-map
    - cheap-eval-source-map
    - cheap-module-eval-source-map  
  - Production
    - source-map
    - hidden-source-map
    - nosource-source-map
```
css的sourcemap

style-loader.option.sourcemap
css-loader.option.sourcemap
less-loader.option.sourcemap
scss-loader.option.sourcemap
等等，每一个处理css的Loader都要加上sourcemap: true
当style-loader设置了singleton时 sourcemap会没用，冲突。坑！

js的sourcemap 如果用到了压缩js的插件，要在option里面提前把sourcemap选项打开
```

```
  devtool: 'eval' // 速度最快
```

- EsLint

  - 需要安装：
    - eslint
    - eslint-loader
    - eslint-plugin-html
    - eslint-friendly-formatter
  - webpack中配置 + .eslintrc.*文件
  - Javascript Standard Style (https://standardjs.com/)
  - 老老实实用脚手架。。。感到绝望
  
  ### 开发环境和生产环境

    - webpack-merge  把配置组合在一起
      - webpack.dev.conf.js
      - webpack.prod.conf.js
      - webpack.base.conf.js

  ### 优化打包速度

  - 办法一
    - 分开vender和app 让webpack不处理第三方依赖
      - Dllplugin
      - DllReferencePlugin
  - 办法二
    - UglifyJsPlugin
      - parallel 这个参数是平行打包，使用系统线程数量-1的线程打包，false为串行打包
      - cache
  - 办法三
    - HappyPack 给loader用的，让Loader处理由串行变并行
  - 办法四
    - babel-loader
      - options.cacheDirectory
      - include exclude
  - 其他
    - 减少resolve
    - Devtool 去除sourcemap
    - cache-loader 缓存loader处理结果
    - 升级node,webpack 