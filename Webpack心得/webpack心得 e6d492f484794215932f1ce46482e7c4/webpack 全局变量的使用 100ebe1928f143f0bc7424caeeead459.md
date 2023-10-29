# webpack 全局变量的使用

# webpack: 全局变量的使用

这一节属于拓展，属于了解知识，因为过于简单，本人已经记住了哈哈哈；

首先我们可以在package.json中定义一些变量，这个变量会自动在配置文件中起到至关重要的作用；比如

"dev": "webpack --profile --json > stats.json --config ./build/webpack.common.js --watch",

"build": "webpack --env.prod --config ./build/webpack.common.js"

我们定义了一个env的对象，里面有一个prod的属性，它默认是true；

我们在哪里接收呢？可能你觉得很奇怪，我们的dev和build为何打包的是同一个文件呢，不是分开的么，那是因为我们在commonjs中分别引入了dev环境下和开发环境下的

配置文件，通过这个env传递的对象的不同，在common.js导出最后的配置对象的时候，我们再和不同的配置进行merge;

module.exports = (env) => {

if (env && env.prod) {

return merge(common, prodCommon);

} else {

return merge(common, devCommon);

}

}

![https://www.notion.sowebpack%20全局变量的使用_files/14833c4f9368ccf1dd7acb82ac2386d6.png](https://www.notion.sowebpack%20全局变量的使用_files/14833c4f9368ccf1dd7acb82ac2386d6.png)

![https://www.notion.sowebpack%20全局变量的使用_files/23fe5fd1912f9640b292590530d137eb.png](https://www.notion.sowebpack%20全局变量的使用_files/23fe5fd1912f9640b292590530d137eb.png)

![https://www.notion.sowebpack%20全局变量的使用_files/bc2c4677f491e247be2e7b2feb1c1e5e.png](https://www.notion.sowebpack%20全局变量的使用_files/bc2c4677f491e247be2e7b2feb1c1e5e.png)