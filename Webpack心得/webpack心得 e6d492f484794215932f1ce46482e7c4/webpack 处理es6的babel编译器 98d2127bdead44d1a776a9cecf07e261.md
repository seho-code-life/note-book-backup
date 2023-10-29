# webpack 处理es6的babel编译器

# webpack: 处理es6的babel编译器

babel是目前非常流行的js语法编译器，他能够把我们的es6+的js代码转换成es5可供浏览器运行；

首先babel是前端工程师，尤其是写框架源码，类库源码的前端来说，babel是必需知识，而我们使用只使用它的几个知识点就好了，官网都有比较明确的配置；

值得一提的是：babel的涵盖太多，足以当作一个新的学习体系去学习，而且所谓的中文网，文章都是英文的，cccc！

首先，使用babel的第一步是要安装babel；

需要安装2个核心的babel库

npm install --save-dev babel-loader @babel/core

配置规则

module: { rules: [ { test: /\.js$/, exclude: /node_modules/, loader: "babel-loader" } ] }

除了我们的第三方依赖模块，我们都要做babel处理;

首先我们可以在根目录创建.babelrc这样的文件，他是babel的配置文件，我们可以在里面配置一个对象；这个对象就是关于babel的api‘

npm install @babel/preset-env --save-dev

启用预设:

{ "presets": ["@babel/preset-env"] }

---

我们如此配置之后就可以进行es6代码转换es5了，但是，在谷歌浏览器中是对es6+支持的非常好的，最先进的；

比如我们写set 或者symbol map等等这些函数，我们需要把对应的es6资源填充到浏览器中，我们应该要使用babel的polyfill填充器

npm install --save @babel/polyfill

在配置文件中配置

options: {

presets: [["@babel/preset-env",{

useBuiltIns: 'usage'

}]]

},

加了useBuiltIns: “usage”之后，我们明显的发现打包之后的js代码非常小，原因是因为，如果不配置，会把所有的es6+的资源全部塞到js中，如果配置了

会把用到的es6+代码编译进去，没用过的就不会塞；

配置后，我们需要引入在js文件最前面引入，以确保他能够正常填充

import "@babel/polyfill";

---

在一些开源项目或者组件库中，一般不会使用填充其polyfill，而会使用一个插件

npm install --save-dev @babel/plugin-transform-runtime

npm install --save @babel/runtime

{ "plugins": [ [ "@babel/plugin-transform-runtime", { "absoluteRuntime": false, "corejs": 2, "helpers": true, "regenerator": true, "useESModules": false } ] ] }

官方推荐默认值配置参数

如果要把corejs: 2的话，必须要安装插件:

npm install --save @babel/runtime-corejs2

打包试试吧...