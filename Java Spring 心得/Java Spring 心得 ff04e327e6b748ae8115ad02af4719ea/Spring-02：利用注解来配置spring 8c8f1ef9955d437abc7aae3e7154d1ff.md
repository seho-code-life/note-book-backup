# Spring-02：利用注解来配置spring

# Spring-02：利用注解来配置spring

首先我们导入必要的包：

值得注意的是不仅要导入spring基础包，在spring高版本中，要引入spring-aop的包

导入约束

首先在配置文件中配置文件，在引入约束的时候写到了prifix为context，所以配置前缀也是context，

<context:component-scan base-package=*"com.entity"*></context:component-scan>

base-package配置是包的路径，会自动扫描此路径以下的所有子包内容，但是不会扫描它的父包内容

在我们的类中可以配置名称，配置一共有4种方式，对应不同的开发层级场景

![https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912100740.png](https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912100740.png)

对应不同的注释，虽然效果都是一样的，但是能够区分不同的层级

那么，修改对象的作用范围呢，是单例还是多例呢？

我们只需要加注解

@Scope(scopeName="singleton")

在注解中对值进行注入也分为两种方式

第一种是：通过原理反射到类的字段进行赋值，这样做的缺点就是破坏了封装性，

第二种是：set方法赋值，推荐使用

此注释写的位置，第一种写法是在类上方写，第二种方式是在set方法上写。

引用类型注入

首先自动装配，会根据类搜索自动匹配，如果在实际开发中创建的对象有很多，那么这种自动装配的注解显然是不合适的

![https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912150326.png](https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912150326.png)

推荐使用Resource

在初始化和销毁的方法 也可以通过注解的方式

![https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912150648.png](https://www.notion.soSpring-02：利用注解来配置spring_files/QQ截图20180912150648.png)