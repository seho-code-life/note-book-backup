# webpack：对caching的支持contentHash

# webpack：对caching的支持contentHash

为了编写非常nice的webpack配置，又能很好的对浏览器性能提升；

浏览器机制中的缓存：浏览器记住上一次加载的文件名，将直接引入上一次缓存的文件；

但是在写代码的时候，js文件如果名称不发生变化，js就会从上次缓存中取js，那这个js是旧的，没有新的功能，那就和我们的想法相反了；

但是在webpack4中可以通过contentHash来根据源文件的变化来生成hash值附给文件名，那么每次更改就是新的名称，就不会让浏览器又缓存；

我们在开发环境不用这么做，但是在线上环境是很有必要的，所以我们将output抽离出来

const merge = require("webpack-merge");

const common = require("./webpack.common")

const MiniCssExtractPlugin = require('mini-css-extract-plugin');

const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');

const path = require('path');

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

output: {

filename: "[name].js",

chunkFilename: '[name].[contenthash].js',

path: path.resolve(__dirname, "../bundle")

},

plugins: [new MiniCssExtractPlugin({}), new OptimizeCssAssetsPlugin({})]

}

module.exports = merge(common, productionConfig);

但是如果在webpack4的低版本中，如果content没有变化，它的hash也会变化，那是因为js打包每次可能造成的互相依赖不一样，有可能会影响到contenthash的判断，所以更改了hash

我们只需要一个配置，把这些依赖关系抽离出来一个单独js，那么就不会发生变化；

optimization: {

**runtimeChunk: {**

**name: "runtime" // 生成一个js文件存储文件之间的映射关系等（兼容老webpack4版本的contenthash在内容未改变打包hash不一样，因为就是js中间的依赖关系和manifest）**

**},**

splitChunks: { // 如果这是一个空对象，那么分割的代码则是按照默认配置进行

chunks: "all", // 只对异步或者同步或者全部的模块引入方式进行分割，all / async / initial

minSize: 30000, // 引入的模块最小体积如果在值之类进行分割，否则不分割，这个是字节，计算/1000是kb

// 同理还有maxSize，如果代码超出，将再次分割

minChunks: 1, // 打包后的chunks模块中引入库的数量

maxAsyncRequests: 5, // 最大异步并行最大请求数量，用途：控制分割的代码数量（默认是5）轻易不要更改

maxInitialRequests: 3, // 入口并行最大请求数量，轻易不要更改

automaticNameDelimiter: '~', // 分割代码连接字符串

name: true, // 开启分割代码的文件名可定义（filename）

cacheGroups: { // 缓存分组，此分组配置和chunks配置项必须是搭配，在判断引入的模块是异步还是同步之后需要走这个配置项进行分组，可以配置vendors为false

// 缓存组：顾名思义，将分割的代码暂时缓存起来，把所有匹配成功的分割代码进行整合打包在一个文件中

vendors: {

test: /[\\/]node_modules[\\/]/,

priority: -10, // 优先级：越小优先级越高，如果模块分别匹配条件和default成功，将通过此参数决定具体分配到哪个模块

},

default: { // 如果满足不了上面的缓存组，将执行下面的配置

minChunks: 2, // 模块引入数量

priority: -20,

reuseExistingChunk: true // 如果开启了此配置，将分割代码中引入的模块（分割过的）直接引入分割后的地址，不再进行分割

}

}

}

}