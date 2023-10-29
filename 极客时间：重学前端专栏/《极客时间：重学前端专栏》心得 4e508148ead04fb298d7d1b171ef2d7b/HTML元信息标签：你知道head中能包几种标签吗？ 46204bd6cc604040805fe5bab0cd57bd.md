# HTML元信息标签：你知道head中能包几种标签吗？

# HTML元信息标签：你知道head中能包几种标签吗？

head标签本身并不携带任何东西，他主要是作为盛放其他语义化的容器使用；

HTML规定了在html标签中head标签必须是第一个；且head标签中必须存在title标签作为网站的标题，最多只能包含一个base，如果文档有其他方式指定了title，比如ifrme框架，可以不指定title；

---

我们来讨论一下title，title是标题的意思，但是html中也有一个h1-h6 heading也是标题头部的意思，在中文来讲，它们非常的难以分辨，更别说老外了；

其实HTML研发的时候也讨论了这个问题，解决方案呢就是：title作为元信息标签可能会被用来分享卡片，收藏书签的作用，所以title起一个概括全文的作用，但是h1有上下文链接的作用，只是展示页面即使无法全写，也不会有太大影响

我们再来谈谈一个冷门标签base是HTML历史遗留的标签，base是提供了文档url的一个基准，在base中指定url，我们在全文档体重就可以不用写前缀url，但是这个标签非常的危险，容易和js冲突，所以不建议使用；

下面我们聊聊meta标签，最重中之重的标签，他是一组键值对，它是通用的元信息表示标签；

一般的meta是由name和content两个属性来定义，name表示元信息的名字，content表示元信息的值

<meta name=application-name content="lsForums">

表示页面所在的web-application的名字是IsForums；

**html5引入了charset来设置编码；没有content和name**

这个非常重要，所以我建议放在文档的第一个；

浏览器读HTML是ASCLL字符，众所周知ASCLL是UTF和大多数编码格式的子集；一般清空下，服务端会通过http头来指定正确的编码方式，但是有些特殊的情况事用file协议打开html，如果没http头，就得设置编码啦；

**具有http-equiv属性的meta；**

具有equiv的meta，表示执行一个命令，这样的meta不需要name属性；

<meta http-equiv="content-type" content="text/html; charset=UTF-8">

name 为 viewport 的 meta

<meta name="viewport" content="width=500, initial-scale=1">

我们的content中的内容是键值对的方式，没有在html标准定义viewport，双方约定好就可以自由使用；

![https://www.notion.soHTML元信息标签：你知道head中能包几种标签吗？_files/QQ截图20190330104716.png](https://www.notion.soHTML元信息标签：你知道head中能包几种标签吗？_files/QQ截图20190330104716.png)

其他预定义的meta的name值:

![https://www.notion.soHTML元信息标签：你知道head中能包几种标签吗？_files/QQ截图20190330104844.png](https://www.notion.soHTML元信息标签：你知道head中能包几种标签吗？_files/QQ截图20190330104844.png)

文章梗概：head元信息容器，title文档标题，base页面基准url，meta元信息通用标签

在评论区找到了一批干货：推荐大家看一下

<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">

<!-- 默认使用最新浏览器 -->

<meta http-equiv="Cache-Control" content="no-siteapp">

<!-- 不被网页(加速)转码 -->

<meta name="robots" content="index,follow">

<!-- 搜索引擎抓取 -->

<meta name="renderer" content="webkit">

<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no, minimal-ui">

<meta name="apple-mobile-web-app-capable" content="yes">

<!-- 删除苹果默认的工具栏和菜单栏 -->

<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">

<!-- 设置苹果工具栏颜色 -->

常见的还有

format-detection 禁止 iPhone 的自动识别-》

<meta name="format-detection" content="telephone=no">

<meta name="format-detection" content="date=no">

<meta name="format-detection" content="address=no">

<meta name="format-detection" content="email=no">

ps:关于name为viewport不是HTML规定，你找不到的，是行业规定，但是非常之重要；