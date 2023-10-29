# 第一节：入门springmvc（Hello world）

# 第一节：入门springmvc（Hello world）

在介绍什么是 SpringMVC 之前，我们先看看 Spring 的基本架构。如下图：

![https://www.notion.so第一节：入门springmvc（Hello%20world）_files/1120165-20170817204804350-377625461.png](https://www.notion.so第一节：入门springmvc（Hello%20world）_files/1120165-20170817204804350-377625461.png)

我们可以看到，在 Spring 的基本架构中，红色圈起来的 Spring Web MVC ，也就是本系列的主角 SpringMVC，它是属于Spring基本架构里面的一个组成部分，属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面，所以我们在后期和 Spring 进行整合的时候，几乎不需要别的什么配置。

SpringMVC 是类似于 Struts2 的一个 MVC 框架，在实际开发中，接收浏览器的请求响应，对数据进行处理，然后返回页面进行显示，但是上手难度却比 Struts2 简单多了。而且由于 Struts2 所暴露出来的安全问题，SpringMVC 已经成为了大多数企业优先选择的框架。

那么多的不说，我们直接通过一个实例来看看 SpringMVC 的魔力。

首先导入jar包：

![https://www.notion.so第一节：入门springmvc（Hello%20world）_files/QQ截图20181015155530.png](https://www.notion.so第一节：入门springmvc（Hello%20world）_files/QQ截图20181015155530.png)

然后我们新建一个web工程，配置webxml

<?xml version=*"1.0"* encoding=*"UTF-8"*?>

<beans xmlns=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)"*

xmlns:xsi=*"[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"*

xmlns:mvc=*"[http://www.springframework.org/schema/mvc](http://www.springframework.org/schema/mvc)"*

xmlns:context=*"[http://www.springframework.org/schema/context](http://www.springframework.org/schema/context)"*

xmlns:aop=*"[http://www.springframework.org/schema/aop](http://www.springframework.org/schema/aop)"*

xmlns:tx=*"[http://www.springframework.org/schema/tx](http://www.springframework.org/schema/tx)"*

xsi:schemaLocation=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)*

*[http://www.springframework.org/schema/beans/spring-beans-4.0.xsd](http://www.springframework.org/schema/beans/spring-beans-4.0.xsd)*

*[http://www.springframework.org/schema/mvc](http://www.springframework.org/schema/mvc)*

*[http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd](http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd)*

*[http://www.springframework.org/schema/context](http://www.springframework.org/schema/context)*

*[http://www.springframework.org/schema/context/spring-context.xsd](http://www.springframework.org/schema/context/spring-context.xsd)*

*[http://www.springframework.org/schema/aop](http://www.springframework.org/schema/aop)*

*[http://www.springframework.org/schema/aop/spring-aop-4.0.xsd](http://www.springframework.org/schema/aop/spring-aop-4.0.xsd)*

*[http://www.springframework.org/schema/tx](http://www.springframework.org/schema/tx)*

*[http://www.springframework.org/schema/tx/spring-tx.xsd](http://www.springframework.org/schema/tx/spring-tx.xsd)"*>

<!-- 配置自定扫描的包 -->

<context:component-scan base-package=*"com.springtest"*></context:component-scan>

<!-- 配置视图解析器: 如何把 handler 方法返回值解析为实际的物理视图 -->

<bean class=*"org.springframework.web.servlet.view.InternalResourceViewResolver"*>

<property name=*"prefix"* value=*"/WEB-INF/views/"*></property>

<property name=*"suffix"* value=*".jsp"*></property>

</bean>

</beans>

扫描包下的类：我们定义一个类：

package com.springtest;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Controller;

import org.springframework.web.bind.annotation.RequestMapping;

import org.springframework.web.servlet.ModelAndView;

@Controller

public class test {

/**

* 1. 使用 @RequestMapping 注解来映射请求的 URL

* 2. 返回值会通过视图解析器解析为实际的物理视图, 对于 InternalResourceViewResolver 视图解析器, 会做如下的解析:

* 通过 prefix + returnVal + 后缀 这样的方式得到实际的物理视图, 然会做转发操作

*

* /WEB-INF/views/success.jsp

*

* @return

*/

@RequestMapping("/hello")

public String hello(){

System.out.println("helloworld");

return "success";

}

}

映射路径是前端填写的url，我们在webxml中设置了

<?xml version=*"1.0"* encoding=*"UTF-8"*?>

<web-app xmlns:xsi=*"[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"*

xmlns=*"[http://java.sun.com/xml/ns/javaee](http://java.sun.com/xml/ns/javaee)"*

xsi:schemaLocation=*"[http://java.sun.com/xml/ns/javaee](http://java.sun.com/xml/ns/javaee)*

*[http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd](http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd)"* id=*"WebApp_ID"* version=*"3.0"*>

<display-name>SpringMVC_01</display-name>

<!-- 配置前端控制器DispatcherServlet -->

<servlet>

<servlet-name>springmvc</servlet-name>

<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

<init-param>

<param-name>contextConfigLocation</param-name>

<!-- 对应上一步创建全局配置文件的文件名以及目录 -->

<param-value>classpath:springmvc.xml</param-value>

</init-param>

</servlet>

<servlet-mapping>

<servlet-name>springmvc</servlet-name>

<url-pattern>/</url-pattern>

</servlet-mapping>

</web-app>