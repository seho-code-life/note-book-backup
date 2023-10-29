# Jquery开发滚动式网页开发插件

# Jquery开发滚动式网页开发插件

首先引入js和css文件

必须基于jquery

然后写入div

然后用父级div包裹起来

在js中获取父级div然后使用fullpage方法完成js初始化

然后在每个滚屏div中加入类

**section**

**效果如下**

***<!--定义5个屏幕-->***

***<!--父级div包裹-->***

**<div** **id="jq-fullpage">**

**<div** **class="first section"></div>**

**<div** **class="second section"></div>**

**<div** **class="third section"></div>**

**<div** **class="fourth section"></div>**

**<div** **class="fivth section"></div>**

**</div>**

**------js部分**

**$(function** **() {**

***//获取父级标签***

**$('#jq-fullpage').fullpage();**

**})**

**如果要使用深度内容动画**

可以使用css3的库快速开发