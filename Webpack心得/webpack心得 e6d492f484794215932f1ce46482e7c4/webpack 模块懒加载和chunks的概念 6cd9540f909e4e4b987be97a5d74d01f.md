# webpack 模块懒加载和chunks的概念

# webpack: 模块懒加载和chunks的概念

async function getComponents() {

const {

default: _

} = await import("lodash");

const element = document.createElement("div");

element.innerHTML = _.join([1, 2, 3], "***");

return element;

}

document.addEventListener("click", () => {

getComponents().then(element => {

document.body.appendChild(element);

})

})

这样懒加载的调用，可以节省不必要的资源加载，真正的安=按需加载，但是这个懒加载和webpack没关系，是es6的引入方式而已，直接webpack能转义语法而已；

关于chunks，概念梳理：

打包后的js文件可以称之为一个一个的chunks，上一节笔记的minChinks配置就是： 打包后的chunks文件中有几次引入了模块