# webpack 打包css等静态资源指南

# webpack: 打包css等静态资源指南

打包css静态资源我们需要安装2个loader，第一个是style-loader 第二个是css-loader

style-loader的作用就是：把整合好的css代码放到head标签中

css-loader的作用就是把css和css之间的关系疏通并且整合成一个css

如何配置？

{

test: /\.scss$/,

use: ["style-loader", "css-loader", "sass-loader", "postcss-loader"]

}

我们如果要使用less sass stylus这样的预编译框架

需要安装对应的loader在webpack中进行配置

以及非常主要的postcss-loader 我们目前webpack4这个demo中做的只是自动加前缀的功能，所以只用了autoprefixer这一个插件，我们需要用postcss，得用一个配置文件来写；

module.exports = {

plugins: [

require('autoprefixer')

]

}

在这里进行一个配置，前提是得安装好插件

关于postcss如果有内容就更，基本打包css静态文件就到这里了；

---------------------------关于css-loader的更多配置介绍

{

test: /\.scss$/,

use: ["style-loader", {

loader: "css-loader",

options: {

importLoaders: 2, // 应用前面loader的数量,解决import引入的css样式不会再经过post和sassloader的处理

// modules: true // 开启模块化

}

}, "sass-loader", "postcss-loader"]

}

css的模块化的作用就是：单独js引入的css文件只能在此js生效，避免样式混淆；我们的js代码应该这样写；

import style from "index.css"

// 这边的style打印出来是一个对象，如果开启了模块化；对象中的key是css中的id或者class，我们通过classList.add(style.bg)

我们打包字体文件是用file-loader进行配置，但是需要注意的一点，我们写的demo，开启了模块化，配置icon生效不了，class问题，所以这个问题之后找到问题我会回来更改的

{

test: /\.(eot|woff|ttf|svg)$/,

use: {

loader: "file-loader"

}

}

在webpack的管理资源指南上，csv和xml文件也做了对应的介绍，都使用了不同的loader处理，即xml-loader等，具体使用再看手册即可；

[https://www.webpackjs.com/guides/asset-management/#%E5%8A%A0%E8%BD%BD-css](https://www.webpackjs.com/guides/asset-management/#%E5%8A%A0%E8%BD%BD-css)