# 第六部分：通过axios  后台代理模式获取歌单列表

# 第六部分：通过axios | 后台代理模式获取歌单列表

我们之前做完了轮播图之后，现在来到了歌单列表，我们还是老样子通过jsonp来获取歌曲列表，可是正当老沈抓取数据的时候，发现歌单接口是带有请求头信息等参数，

这个参数是无法通过前端js设置的，所以我们需要通过后台代理模式来修改这些数据。

首先我们在api下的推荐页面js文件定义一个方法

//获取歌单列表的接口

export function getDiscList(){

//定义url接口

const url='/api/getDiscList'

//拼接url后的参数，用es6的语法assign塞进去

const data=Object.assign({},commonParams,{

platform: 'yqq',

hostUin: 0,

sin: 0,

ein: 29,

sortId: 5,

needNewCode: 0,

categoryId: 10000000,

rnd: Math.random(),

format: 'json'

})

return axios.get(url,{

params:data

}).then((res)=>{

return Promise.resolve(res.data)

})

}

axios获取的接口是等会要用到的：/api/getDiscList

然后我们比较重要的返回数据：返回一个发送请求，，然后数据参数是data对象以及默认配置对象，然后返回一个相应的数据。

重要的是，我们今天的主要bug是在vuecli3.0中我们发现没有在build目录下有配置server的configjs。然后就百度了一下，发现了在vuecli3.0中的新写法

首先我们定义一下：

const axios = require('axios');

const express = require('express');

const apiRoutes = express.Router();

首先引入了axios库，然后定义了express框架（nodejs）

然后配置了路由模块

然后我们需要在

devServer 下添加一个方法

before(apiRoutes){

apiRoutes.get('/api/getDiscList',(req,res)=>{

const url = '[https://c.y.qq.com/splcloud/fcgi-bin/fcg_get_diss_by_tag.fcg](https://c.y.qq.com/splcloud/fcgi-bin/fcg_get_diss_by_tag.fcg)';

axios.get(url, {

headers: {

referer: '[https://c.y.qq.com/](https://c.y.qq.com/)',

host: '[c.y.qq.com](http://c.y.qq.com/)'

},

params: req.query //这是请求的query

}).then((response) => {

//response是api地址返回的，数据在data里。

res.json(response.data)

}).catch((e) => {

console.log(e);

})

})

//app.use('/api/getDiscList', apiRoutes) // 所有通过接口相关的api都会通过api这个路由导向到具体的路由

},

在这里我们通过get方法拿到了和在推荐页面一样的url，就是推荐页面请求的url，到了现在这个路由了，到这个路由中我们就可以拿到req和res对象了。

分别是请求和响应

然后在这里通过axios来获取值，url是qq音乐的接口，然后在这里可以设置header一些数据，然后也拿到了参数，通过req.query拿到，拿到之后，我们就可以

拿到axios返回的值了，这个值是response中的data，然后我们通过res响应对象通过json方式传输到我们的推荐页面js，然后组件模块再调用我们的js

就轻松的拿到了歌单的数据了。