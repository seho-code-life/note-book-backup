# Vue路由的钩子函数

# Vue路由的钩子函数

钩子函数有2种配置地方：

第一个就是在indexjs中路由配置文件中配置；

*//钩子函数*

beforeEnter:(to,from,next)=>{

***console***.log(to);

***console***.log(from);

next();

},

beforeLeave:(to,from,next)=>{

***console***.log(to);

***console***.log(from);

next();

}

2种类型：一个是进入前，一个是离开前

另外一个是在我们的模板vue中来设置

*//钩子函数*

beforeRouteEnter:(to,**from**,next)=>{

***console***.log(to);

***console***.log(**from**);

next();

},

beforeRouteLeave:(to,**from**,next)=>{

***console***.log(to);

***console***.log(**from**);

next();

}

在vue中与indexjs配置钩子函数是不同的，在模板中配置钩子要加Route，否则会失败。