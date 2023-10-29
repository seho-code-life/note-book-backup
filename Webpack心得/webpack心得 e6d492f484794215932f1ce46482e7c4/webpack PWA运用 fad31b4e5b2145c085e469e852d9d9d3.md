# webpack PWA运用

# webpack: PWA运用

渐进式web应用程序PWA，是可以提供和本机应用程序相似的体验的web程序，有很多事情可以促成这一点，在这些功能中，最重要的是能够

在离线状态下运行的能力，这是通过service works的网络技术来实现的

以前这种技术实现起来非常复杂，但是webpack提供了大厂封装的插件，我们可以很快的上手PWA，并且尝试注册它;

我们可以使用一个叫workbox的google开源项目来实现

首先我们可以尝试一下不离线工作；

安装一个http服务模块

npm install http-server --save-dev

写如下代码，在dist中启动一个服务器

{

...

"scripts": {

- "build": "webpack"+ "build": "webpack",+ **"start": "http-server dist"**

},

...

}

启动之后，我们可以看到项目正常运行，js代码也正常打印

但是如果我们把服务关掉，那么网页也不会继续存在，变为不可访问

所以我们需要workbox来实现离线加载

npm install workbox-webpack-plugin --save-dev

引入插件

![https://www.notion.sowebpack%20PWA运用_files/Image.png](https://www.notion.sowebpack%20PWA运用_files/Image.png)

![https://www.notion.sowebpack%20PWA运用_files/Image%20%5B1%5D.png](https://www.notion.sowebpack%20PWA运用_files/Image%20%5B1%5D.png)

我们可以看到生成了上面的文件，主要的核心就是service-worker.js, prichache是service-worker运行的依赖

![https://www.notion.sowebpack%20PWA运用_files/Image%20%5B2%5D.png](https://www.notion.sowebpack%20PWA运用_files/Image%20%5B2%5D.png)

我们可以写一段代码来判断浏览器是否支持servicewoker，如果支持，我们就注册

这样我们运行代码，就会把服务器关闭，仍然可以运行上次缓存的！！

[https://webpack.js.org/guides/progressive-web-application/#root](https://webpack.js.org/guides/progressive-web-application/#root)