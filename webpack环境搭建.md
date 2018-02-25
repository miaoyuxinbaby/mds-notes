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