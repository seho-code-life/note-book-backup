# Vue的redirect重新定向

# Vue的redirect重新定向

我们只要在路由配置文件中（/src/router/index.js）把原来的component换成redirect参数就可以了。我们来看一个简单的配置。

特点：直接改变了url中的地址，来达到重定向。

**export** **default** **new** **Router({**

**routes:** **[**

**{**

**path:** **'/',**

**component:** **Hello**

**},{**

**path:'/params/:newsId(\\d+)/:newsTitle',**

**component:Params**

**},{**

**path:'/goback',**

**redirect:'/'**

**}**

**]**

**})**

**这样就直接到/下了**

**那如何重定向传递参数呢？**

**redirect:'/params/:newsId(\\d+)/:newsTitle'**

**直接在定向后传递需要的参数即可**

而在我们的appvue中就可以设置参数的值

<**router-link** **to="/haha/19/i am title"**>test</**router-link**>

**在组件中这样书写**

![https://www.notion.soVue的redirect重新定向_files/QQ截图20180818154324.png](https://www.notion.soVue的redirect重新定向_files/QQ截图20180818154324.png)