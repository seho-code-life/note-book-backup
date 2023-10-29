# Vue的动画（2）

# Vue的动画（2）

**animate.css是国外大神做的一个css3的动画库，样式非常多，非常好看，所以vue2.0中调用这种类似的动画库是非常不一样的。**

**首先我们的标签要换成transition-group**

**其次，运动元素要引入class类anmited，而且要添加一句** **key="index"；**

**在vue中已经取消了transition的元素，所以要引入外部的库需要在标签中直接添加** **enter-active-class="zoomInleft"** **leave-active-class="zoomOutRight"；**

**--类似这样**

**--案例**

**<!DOCTYPE** **html>**

**<html** **lang="en">**

**<head>**

**<meta** **charset="UTF-8">**

**<title>Title</title>**

**<link** **rel="stylesheet"** **href="animate.css">**

**<script** **src="vue.js"></script>**

**<style>**

**#box{**

**width:** **400px;**

**height:** **auto;**

**margin:** **0** **auto;**

**}**

**#color** **{**

**width:** **100px;**

**height:** **100px;**

**background:** **red;**

**}**

**</style>**

**</head>**

**<body>**

**<div** **id="box">**

**<input** **type="button"** **value="点击"** **@click="change">**

**<transition-group** **enter-active-class="zoomInLeft"** **leave-active-class="zoomOutRight">**

**<div** **id="color"** **v-show="panduan"** **class="animated"** key="index"**></div>**

**</transition-group>**

**</div>**

**<script>**

**var** ***vm*=new** **Vue({**

**el:'#box',**

**data:function** **() {**

**return** **{**

**panduan:true**

**}**

**},**

**methods:{**

**change(){**

**this.panduan=!this.panduan;**

**}**

**}**

**})**

**</script>**

**</body>**

**</html>**