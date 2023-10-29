# webpack 使用webpack-dev-serve提升开发效率

# webpack: 使用webpack-dev-serve提升开发效率

我们每次打包之后的代码，每次要手动输入命令，而且要徒手刷新浏览器，这非常不方便；

而且众所周知，我们一般在写vue或者react的时候，它都会开启一个web服务器，我们的代码运行在上面，会有自动保存打包以及自动刷新的功能，那么这个是怎么做到的；

其实在最新的vue-cli3引入了webpack-dev-server，在之前的老版本没有引入这个，是因为当时不成熟，现在成熟了，被很多cli脚手架作为web服务器的首选；

我们如果打开一个html文件，是file浏览器的头不是http头是发送不了请求的，所以我们需要放在一个web服务器上

我们这篇笔记，用3种方式来实现这样的效果，第一种watch指令，第二种node-js的express实现的简易版webpack-dev-server ，第三种是webpack-dev-server

"watch": "webpack --watch",

第一种，我们监听文件的变化，让指令帮助我们打包；

第二种：安装express和webpack编译中间件

// 引入webpack和express

const express = require("express");

const webpack = require("webpack");

const webpackDevMiddleWare = require("webpack-dev-middleware")

const webpackConfig = require("./webpack.config");

// 编译器

const complier = webpack(webpackConfig);

var app = new express();

app.use(webpackDevMiddleWare(complier, {}))

app.listen(3000, ()=>{

console.log("running")

})

第三种：webpack-dev-server我们在config经过简单的配置就好了;

devServer: {

contentBase: "./bundle",

open: true

},

第一个key的意思就是在哪个目录下启动一个服务器；

open的意思是自动打开浏览器

还可以配置port或者proxy等等很多，webpack-dev-server的功能很强大

我们使用webpack-dev-server打包的时候不会出现dist目录，他把dist目录放到了内存中了，可以提升我们的打包效率；

更多配置项等待添加；

[https://www.webpackjs.com/guides/development/](https://www.webpackjs.com/guides/development/)