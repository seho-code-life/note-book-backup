# webpack libraries的基本使用

# webpack: libraries的基本使用

我们一般写业务的时候，使用webpack打包非常方便，那么我们写一个框架或者ui组件库的时候，打包就变得非常重要，所以libraries这个概念一定要理解

首先我们写一点js代码

import _ from "lodash";

export function add(a, b) {

return _.join([a,b], '_')

}

我们一个模块写完之后，如果我们作为用户，来使用我们这个框架，通常会有以下几种方式来引入

// ES2015 模块引入import * as webpackNumbers from 'webpack-numbers';//

CommonJS 模块引入var webpackNumbers = require('webpack-numbers');// ...// ES2015 和 CommonJS 模块调用

webpackNumbers.wordToNum('Two');// ...// AMD 模块引入require(['webpackNumbers'], function ( webpackNumbers) {

// ...

// AMD 模块调用

webpackNumbers.wordToNum('Two');

// ...});

当然我们还可以通过script直接引入js

<!doctype html><html>

...

<script src="https://unpkg.com/webpack-numbers"></script>

<script>

// ...

// 全局变量

webpackNumbers.wordToNum('Five')

// window 对象中的属性

window.webpackNumbers.wordToNum('Five')

// ...

</script></html>

类似与这样，挂载到了一个全局变量中，jquery应该就是$

如何达到这样的效果呢？？

我们看一下配置

引入了lodash，然后我们配置webpack

const path = require("path");

module.exports = {

mode: 'production',

entry: './src/index.js',

**externals: 'lodash',**

output: {

path: path.resolve(__dirname, 'dist'),

filename: "dist.js",

**library: 'webpackNumbers',**

**libraryTarget: 'umd'**

}

}

**externals 是什么意思？**

我们引入了lodash，导致打包出来的库很大，而且如果用户在引入我们的模块之前，就已经引入了lodash，那么就可能会出现2份一样的lodash，这个影响是很大的

所以webpack推荐，我们把这个权力给用户，在这里设置一个属性，或者对象，数组，来区分在不同环境下，如果绕开对应的模块的打包

我们这边配置了字符串，一个lodash，说明这个lodash是由用户自己导入的，和模块打包不相干;

其他写法，我们分引入，将它们区分以下

+ externals: {+

lodash: {+

commonjs: 'lodash',+

commonjs2: 'lodash',+

amd: 'lodash',+

root: '_'+ }+ }

root是script直接引入的方式

---

当你在 import 引入模块时，这可以将你的 library bundle 暴露为名为 webpackNumbers 的全局变量。为了让 library 和其他环境兼容，还需要在配置文件中添加 libraryTarget 属性。这是可以控制 library 如何以不同方式暴露的选项。

如上所述，代码

library: 'webpackNumbers',

libraryTarget: 'umd'

umd指的是es，common，amd的引入方式

library指的是暴漏一个全局变量

遵循生产环境指南中的步骤，来优化生产环境下的输出。那么，我们还需要通过设置 package.json 中的 main字段，添加生成 bundle 的文件路径。

package.json

{

...

"main": "dist/webpack-numbers.js",

...

}

或者，按照这里的指南添加为标准模块：

{

...

"module": "src/index.js",

...

}

键(key) main 是指 package.json 标准，以及 module 是 一个提案，此提案允许 JavaScript 生态系统升级使用 ES2015 模块，而不会破坏向后兼容性。

module 属性应指向一个使用 ES2015 模块语法的脚本，但不包括浏览器或 Node.js 尚不支持的其他语法特性。这使得 webpack 本身就可以解析模块语法，如果用户只用到 library 的某些部分，则允许通过 tree shaking 打包更轻量的包。

现在你可以将其发布为一个 npm 包，并且在 unpkg.com 找到它并分发给你的用户。

为了暴露和 library 关联着的样式表，你应该使用 ExtractTextPlugin。然后，用户可以像使用其他样式表一样使用和加载这些样式表。