# webpack  code-spliting代码分割的2种方式

# webpack: code-spliting代码分割的2种方式

我们在项目中为了更好的代码分发，提升性能，通常会把第三方模块和业务逻辑分开，但是webpack4给我们提供了自动代码分割的方法；

optimization: {

splitChunks: {

chunks: 'all'

}

}

我们可以通过以上的配置，让同步的代码提供代码分割的功能

异步获取模块并且分割，返回一个import语句，需要借助一个babel转换插件

babel-plugin-dynamic-import-webpack

babel引入这个插件

{

"plugins": [

[

"@babel/plugin-transform-runtime",

{

"absoluteRuntime": false,

"corejs": 2,

"helpers": true,

"regenerator": true,

"useESModules": false

},

],

"dynamic-import-webpack"

]

}

业务逻辑，异步获取

// import _ from "lodash";

// console.log(_.join(["1","2","3"], "***"));

function getComponents() {

return import("lodash").then(({

default: _

}) => {

const element = document.createElement("div");

element.innerHTML = _.join([1, 2, 3], "***");

return element;

})

}

getComponents().then(element => {

document.body.appendChild(element);

})

原文链接：

[https://webpack.js.org/guides/code-splitting/#root](https://webpack.js.org/guides/code-splitting/#root)