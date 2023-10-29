# Vue的alias定向以及容易遇到的坑

# Vue的alias定向以及容易遇到的坑

alias的特点就是：不改变url中的地址，这种更为友好，推荐使用。

使用方法：可以实现类似重定向的效果

其实就是给路由其一个别名。

然后就是在<**router-link** **to="/haha"**>testover</**router-link**>

to指向别名，然后别名所属的路由就能访问到其组件，达到跳转的效果。

{

**path**:**'/pengming'**,

**component**:ccc,

**alias**:**'/haha'**

}

alias遇到的坑：

alias在给path为/的组件起别名时候是没有效果的，但是其他网友是可以的，本人测试是不可以的。

{

**path**: **'/'**,

**name**: **'HelloWorld'**,

**component**: HelloWorld,

**alias**:**'home'**

}

指向/是没有效果地，也没有js报错，苦恼。