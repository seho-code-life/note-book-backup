# webpack 能让打包更方便快捷的2个plugin

# webpack: 能让打包更方便快捷的2个plugin

一个是官方推荐的plugin，是HTML-WEBPACK-PLUGIN

它的作用就是我们打包不用自己新建html引入js，插件不仅可以替我们完成工作，还有自己diy模板，只要引入模板，就可以按照我们提供的模板创建html并且自动引入打包后的js！！

第二个是第三方的plugin，我们每次打包maybe会更改js文件等等，当我们重新打包会发现，之前的文件不会删除，这样每次要删除打包后的文件夹是很麻烦的，所以这个插件可以帮助我们删除之前的文件夹；

如何解释plugin？

它像vue react一样，类似于生命钩子，在打包的某一刻时间执行某作用；

const path = require('path')

// 引入插件

const HtmlWebpackPlugin = require('html-webpack-plugin')

const { CleanWebpackPlugin } = require("clean-webpack-plugin")

module.exports = {

mode: "production",

entry: "./index.js",

output: {

filename: "dist.js",

path: path.resolve(__dirname, "bundle")

},

plugins: [new HtmlWebpackPlugin({

template: "./index.html"

}),new CleanWebpackPlugin()]

}

后面的插件记得是引入它的一个其中模块；