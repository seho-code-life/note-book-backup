# webpack  production和development

# webpack: production和development

开发模式和生产模式，对于srouceMap以及webpack-dev-server的配置都不一样，我们每次打包生产环境代码要更改很多内容，所以我们可以用公共模块，把公共模块抽取出来，再用webpack-merge插件合并可以达到配置合并的目的；

const merge = require("webpack-merge");

const common = require("./webpack.common")

const productionConfig = {

mode: "production"

}

module.exports = merge(common, productionConfig);

package.json

"dev": "webpack --config ./build/webpack.development.js",

"build": "webpack --config ./build/webpack.production.js"