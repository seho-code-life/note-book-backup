# webpack：splitChunksPlugins的配置

# webpack：splitChunksPlugins的配置

上偏笔记说的那个插件不是webpack官方推荐的，需要在babel中下载babel推荐的异步引入的插件；

[https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import/](https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import/)

关于上篇文章的分割配置在webpack.common.js中配置的玩意，我们看一下webpack官方的文章，里面有一套配置，我们需要熟悉它的作用

optimization: {

splitChunks: { // 如果这是一个空对象，那么分割的代码则是按照默认配置进行

chunks: "all", // 只对异步或者同步或者全部的模块引入方式进行分割，all / async / initial

minSize: 30000, // 引入的模块最小体积如果在值之类进行分割，否则不分割，这个是字节，计算/1000是kb

// 同理还有maxSize，如果代码超出，将再次分割

minChunks: 1, // 模块最小引入数量

maxAsyncRequests: 5, // 最大异步并行最大请求数量，用途：控制分割的代码数量（默认是5）轻易不要更改

maxInitialRequests: 3, // 入口并行最大请求数量，轻易不要更改

automaticNameDelimiter: '~', // 分割代码连接字符串

name: true, // 开启分割代码的文件名可定义（filename）

cacheGroups: { // 缓存分组，此分组配置和chunks配置项必须是搭配，在判断引入的模块是异步还是同步之后需要走这个配置项进行分组，可以配置vendors为false

// 缓存组：顾名思义，将分割的代码暂时缓存起来，把所有匹配成功的分割代码进行整合打包在一个文件中

vendors: {

test: /[\\/]node_modules[\\/]/,

priority: -10, // 优先级：越小优先级越高，如果模块分别匹配条件和default成功，将通过此参数决定具体分配到哪个模块

filename: "ventor.js" // 分割代码分组之后一起打包到哪个文件，这里设置文件名

},

default: { // 如果满足不了上面的缓存组，将执行下面的配置

minChunks: 2, // 模块引入数量

priority: -20,

reuseExistingChunk: true // 如果开启了此配置，将分割代码中引入的模块（分割过的）直接引入分割后的地址，不再进行分割

}

}

}

}

[https://www.webpackjs.com/plugins/split-chunks-plugin/](https://www.webpackjs.com/plugins/split-chunks-plugin/)