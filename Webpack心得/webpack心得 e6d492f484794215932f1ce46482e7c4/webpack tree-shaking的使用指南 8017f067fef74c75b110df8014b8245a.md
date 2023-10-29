# webpack tree-shaking的使用指南

# webpack: tree-shaking的使用指南

tree-shaking是webpack2之后引入的概念，属于webpack范畴，顾名思义： “摇树”

它能够处理es导入语法中的模块，进行一个筛选，将没有使用的模块不会导入到最终的js文件中；

他不能处理commonjs导入语法，因为它们的底层不一样，tree-shaking只能处理静态引入

值得注意的是，tree-shaking在生产环境下是不需要经过什么配置，但是在开发环境下需要经过一个小小的配置;

optimization: {

usedExports: true

}

加了这一句之后我们就可以正常使用tree-shaking

但是在一般引入import "mm.css"这类文件，没有导出的模块，tree-shaking会直接忽略不会打包，这是非常危险的，所以我们必须在package.json中建立配置项，让依赖能够经过tree-shaking处理；

"sideEffects": [

"./src/some-side-effectful-file.js",

"*.css"

]

可选值有很多，true 和 false false是关闭，全部没有导出项的依赖都不能处理

[https://webpack.js.org/guides/tree-shaking/#root](https://webpack.js.org/guides/tree-shaking/#root)