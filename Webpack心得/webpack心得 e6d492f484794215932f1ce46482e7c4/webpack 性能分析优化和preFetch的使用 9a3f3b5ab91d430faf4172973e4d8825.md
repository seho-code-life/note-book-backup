# webpack 性能分析优化和preFetch的使用

# webpack: 性能分析优化和preFetch的使用

在前端工程化的今天，今天的笔记主要是阐述一个观点： 2019年了都，不要做性能优化的时候，还在考虑缓存，缓存的优点带来不了什么，需要考虑代码的使用率；

我们可以通过在package.json中的一句代码，将生成日志报告打印在一个json文件中，然后可以通过 [http://webpack.github.io/analyse/](http://webpack.github.io/analyse/) 去分析；

--profile --json > stats.json

我们可以通过异步代码去写一个简单的例子

document.addEventListener("click", () => {

return import("./inner").then(({default: func}) => {

func();

})

})

export default function inner(){

let dom = document.createElement("div");

dom.innerHTML = "123";

document.body.appendChild(dom);

}

我们在没点击的时候，是不会加载这个js文件的，当点击的时候才会加载，但是我们可以看到这一块的代码使用率是多少；

![https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image.png](https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image.png)

我们可以通过这样异步方式加载业务逻辑，导致我们的性能提升，如果没有使用异步引入的方式，将会直接把所有的业务逻辑都会加载进来；

但是你可能会想到，如果当一个事件发生才加载对应的js，会不会造成加载卡顿的问题，那么code spliting中的preFetch和preLoading的魔法注解就可以很好的解决这个办法；

document.addEventListener("click", () => {

return import(/* webpackPrefetch: true */ "./inner").then(({default: func}) => {

func();

})

})

我们可以通过浏览器看到，我们使用预加载fetch的时候： 当带宽空闲，偷偷的加载被标注的异步模块；

页面刷新：

![https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image%20%5B1%5D.png](https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image%20%5B1%5D.png)

页面加载了，然后点击之后，浏览器通过缓存功能（因为已经加载过一次了），那么就可以通过读缓存来引入js

![https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image%20%5B2%5D.png](https://www.notion.sowebpack%20性能分析优化和preFetch的使用_files/Image%20%5B2%5D.png)

preLoading的使用和preFetch的使用也是有区别的，前者是和其他模块一起加载，不是等待带宽富余的情况下加载，更多的业务情景中还是推荐后者；

一定要记得，优化前端性能，靠缓存提升空间并不大，而是要靠提高代码的使用率来的实在，这也就是webpack在做代码分割的时候，默认的chunks是async的原因了

因为webpack本身提倡异步写法和提升代码使用覆盖率这种做法