# webpack css的spliting

# webpack: css的spliting

接着上一个笔记讲的一点遗漏的东西

output: {

filename: "[name].js",

chunkFilename: '[name].chunk.js',

path: path.resolve(__dirname, "../bundle")

},

关于output中的chunkFilename的设置，可以设置入口js中的引入的其他额外的chunk的名称

---

[https://webpack.js.org/plugins/mini-css-extract-plugin/#root](https://webpack.js.org/plugins/mini-css-extract-plugin/#root)

[https://github.com/NMFR/optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin)

第一个是关于css分割的文档，第二个是文档推荐的一个插件的地址，用于合并css代码的

首先这个css分割的插件是不支持HMR的，所以在开发环境是不建议使用，所以我们配置要在生产环境上配置

const merge = require("webpack-merge");

const common = require("./webpack.common")

const MiniCssExtractPlugin = require('mini-css-extract-plugin');

const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');

const productionConfig = {

mode: "production",

module: {

rules:[{

test: /\.css$/,

use: [

MiniCssExtractPlugin.loader,

'css-loader',

'postcss-loader'

]

}]

},

plugins: [new MiniCssExtractPlugin({}), new OptimizeCssAssetsPlugin({})]

}

module.exports = merge(common, productionConfig);

用MiniCssExtractPlugin，loader 替换原先的style-loader

很关键的是，如果发现没有生成出来css，看看是否开启了treeshaking，如果开启了，就在

optimization: {

usedExports: true

},

为true的情况下更改package.json中的值

"sideEffects": [

"*.css"

],

选择不shaking这种类型的文件

![https://www.notion.sowebpack%20css的spliting_files/c62411d17682b34e25242477e353a761.png](https://www.notion.sowebpack%20css的spliting_files/c62411d17682b34e25242477e353a761.png)