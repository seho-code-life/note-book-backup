# 4 SpringSecurity安全框架和密码加密实现方法

# 4: SpringSecurity安全框架和密码加密实现方法

今天学习了这个SpringSecurity这个框架，记录一下注意的知识点和坑

首先这个是一个安全框架，主要用途就是在登陆上，它为我们提供了很好的安全性和性能，为我们减少了很多开发需求

我们可以花费一点点时间做一个入门小demo

引入包和webxml加载配置我就不说了，都是复制粘贴的，最主要就是看我们的框架xml配置，我们需要了解一些功能。

<?xml version=*"1.0"* encoding=*"UTF-8"*?>

<beans:beans xmlns=*"[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)"*

xmlns:beans=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)"* xmlns:xsi=*"[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"*

xsi:schemaLocation=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)[http://www.springframework.org/schema/beans/spring-beans.xsd](http://www.springframework.org/schema/beans/spring-beans.xsd)*

*[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)[http://www.springframework.org/schema/security/spring-security.xsd](http://www.springframework.org/schema/security/spring-security.xsd)"*>

<!-- 以下页面不拦截 -->

<http pattern=*"/login.html"* security=*"none"*></http>

<http pattern=*"/login_error.html"* security=*"none"*></http>

<!-- 页面拦截规则 -->

<http use-expressions=*"false"*>

<intercept-url pattern=*"/**"* access=*"ROLE_USER"*/>

<!-- 开启表单自动生成 -->

<form-login default-target-url=*"/index.html"* authentication-failure-url=*"/login_error.html"*/>

<csrf disabled=*"true"*/>

</http>

<!-- 认证管理器 -->

<authentication-manager>

<authentication-provider>

<user-service>

<user name=*"admin"* password=*"admin"* authorities=*"ROLE_USER"*/>

</user-service>

</authentication-provider>

</authentication-manager>

</beans:beans>

我们的页面拦截规则的pattern主要是不拦截资源，比如我们的一些登陆页面或者错误页面，这些页面是不需要拦截的。

我们拦截的规则设置成跟目录下的所有资源，匹配的用户是ROLE_USER , 框架必须指定前缀是ROLE，这里要注意，我们可以开启表单自动生成，然后成功跳转到首页，错误跳转

到错误页面。

我们的认证管理器，主要是写死账号和密码。

这样就可以给我们提供了一个自动生成的，包括html页面都是框架帮助我们生成好了，我们通过了一个入门小案例知道了这个框架的大体运用。

然后开始运用到项目中，引入依赖和webxml就不敲了，我们还是来熟悉一下业务场景，首先我们不能让框架帮助我们生成丑陋的登陆页面了，我们需要指定自己的登陆页面。

我们可以这样写：

<?xml version=*"1.0"* encoding=*"UTF-8"*?>

<beans:beans xmlns=*"[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)"*

xmlns:beans=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)"* xmlns:xsi=*"[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"*

xsi:schemaLocation=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)[http://www.springframework.org/schema/beans/spring-beans.xsd](http://www.springframework.org/schema/beans/spring-beans.xsd)*

*[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)[http://www.springframework.org/schema/security/spring-security.xsd](http://www.springframework.org/schema/security/spring-security.xsd)"*>

<!-- 匹配不拦截的资源 -->

<http pattern=*"/*.html"* security=*"none"*></http>

<http pattern=*"/css/**"* security=*"none"*></http>

<http pattern=*"/img/**"* security=*"none"*></http>

<http pattern=*"/js/**"* security=*"none"*></http>

<http pattern=*"/plugins/**"* security=*"none"*></http>

<!-- 页面拦截规则 -->

<http use-expressions=*"false"*>

<intercept-url pattern=*"/**"* access=*"ROLE_ADMIN"* />

<form-login login-page=*"/login.html"* default-target-url=*"/admin/index.html"*

authentication-failure-url=*"/login.html"* always-use-default-target=*"true"* />

<!-- 跨域拦截是否禁用 -->

<csrf disabled=*"true"* />

<!--设置不拦截iframe框架内置 -->

<headers>

<frame-options policy=*"SAMEORIGIN"* />

</headers>

<logout/>

</http>

<!-- 认证管理器 -->

<authentication-manager>

<authentication-provider>

<user-service>

<user name=*"admin"* password=*"123456"* authorities=*"ROLE_ADMIN"* />

</user-service>

</authentication-provider>

</authentication-manager>

</beans:beans>

我们不拦截的资源就根据项目的场景来决定，我们在form中配置指定登陆页面，而且要加入一个成功默认跳转页面（很重要）

认证管理器我们还是写死一个账号密码，让他可以登陆，这样我们就在管理员后台登陆的时候用到了这个安全框架，值得注意的是:

前端html：action : /login 必须post方法 账号和密码的name必须是username和password

然后我们开始做运营商的商家入驻申请其中非常关键的安全框架的查询数据库登陆+密码加密：

查询数据库进行登陆，我们之前写的security写的是固定的admin和密码，我们如何查询数据库呢？？

我们需要自己在controller写一个包，在包下建立一个特殊的服务，我们在中配置扫描的是controller的包，所以我们这边的注入service是不起作用的通过注解

因为这个服务是在doubbo远程注册，所以我们只能通过xml注入，我们先看看具体的查询服务：

package com.pinyougou.service;

import java.util.ArrayList;

import java.util.Collection;

import java.util.List;

import org.springframework.security.core.GrantedAuthority;

import org.springframework.security.core.authority.SimpleGrantedAuthority;

import org.springframework.security.core.userdetails.User;

import org.springframework.security.core.userdetails.UserDetails;

import org.springframework.security.core.userdetails.UserDetailsService;

import org.springframework.security.core.userdetails.UsernameNotFoundException;

import com.pinyougou.pojo.TbSeller;

import com.pinyougou.sellergoods.service.SellerService;

/**

* 认证类

*

* @author LAOSHEN

*

*/

public class UserDetailsServiceImpl implements UserDetailsService {

// 此服务注入通过dubbo远程注入

private SellerService sellerService;

public void setSellerService(SellerService sellerService) {

this.sellerService = sellerService;

}

public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {

// 添加角色集合

List<GrantedAuthority> authorities = new ArrayList();

authorities.add(new SimpleGrantedAuthority("ROLE_SELLER"));

TbSeller seller = sellerService.findOne(username);

// 判断用户是否存在

if (seller != null) {

// 判断用户是否通过审核

if (seller.getStatus().equals("1")) {

// 如果通过审核就生成改用户的正确密码与输入的进行匹配

return new User(username, seller.getPassword(), authorities);

} else {

// 如果没有通过审核

return null;

}

} else {

// 如果没有此用户

return null;

}

}

}

返回空就直接不能登陆成功，重定向到login页面中，最重要的是我们的安全框架的配置：

<?xml version=*"1.0"* encoding=*"UTF-8"*?>

<beans:beans xmlns=*"[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)"*

xmlns:dubbo=*"[http://code.alibabatech.com/schema/dubbo](http://code.alibabatech.com/schema/dubbo)"* xmlns:mvc=*"[http://www.springframework.org/schema/mvc](http://www.springframework.org/schema/mvc)"*

xmlns:beans=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)"* xmlns:xsi=*"[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"*

xsi:schemaLocation=*"[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)[http://www.springframework.org/schema/beans/spring-beans.xsd](http://www.springframework.org/schema/beans/spring-beans.xsd)*

*[http://code.alibabatech.com/schema/dubbo](http://code.alibabatech.com/schema/dubbo)[http://code.alibabatech.com/schema/dubbo/dubbo.xsd](http://code.alibabatech.com/schema/dubbo/dubbo.xsd)*

*[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)[http://www.springframework.org/schema/security/spring-security.xsd](http://www.springframework.org/schema/security/spring-security.xsd)"*>

<!-- 匹配不拦截的资源 -->

<http pattern=*"/*.html"* security=*"none"*></http>

<http pattern=*"/css/**"* security=*"none"*></http>

<http pattern=*"/img/**"* security=*"none"*></http>

<http pattern=*"/js/**"* security=*"none"*></http>

<http pattern=*"/plugins/**"* security=*"none"*></http>

<http pattern=*"/seller/add.do"* security=*"none"*></http> //把一些业务逻辑的url进行放行，否则无法进行业务逻辑的操作

<!-- 页面拦截规则 -->

<http use-expressions=*"false"*>

<intercept-url pattern=*"/**"* access=*"ROLE_SELLER"* />

<form-login login-page=*"/shoplogin.html"* default-target-url=*"/admin/index.html"*

authentication-failure-url=*"/shoplogin.html"* always-use-default-target=*"true"* />

<!-- 跨域拦截是否禁用 -->

<csrf disabled=*"true"* />

<!--设置不拦截iframe框架内置 -->

<headers>

<frame-options policy=*"SAMEORIGIN"* />

</headers>

<logout/> //注销选项，页面通过访问/logout 即可注销

</http>

<!-- 认证管理器 -->

<authentication-manager>

<authentication-provider user-service-ref=*"UserDetailsService"*>

<password-encoder ref=*"bcryptEncoder"*></password-encoder> //依赖于加密实现类，登陆的时候可以把原密码自动加密，无需将加密后的密码解密进行匹配

</authentication-provider>

</authentication-manager>

<beans:bean id=*"UserDetailsService"* class=*"com.pinyougou.service.UserDetailsServiceImpl"*>

<beans:property name=*"sellerService"* ref=*"sellerService"*></beans:property> //认证类：注入service接口，从dubbo中，需要拥有set方法别忘记了

</beans:bean>

<!-- 引用dubbo 服务 ,从注册中心去寻找接口服务并且注入-->

<dubbo:application name=*"pinyougou-shop-web"* />

<dubbo:registry address=*"[zookeeper://192.168.25.151:2181](zookeeper://192.168.25.151:2181/)"*/>

<dubbo:reference id=*"sellerService"* interface=*"com.pinyougou.sellergoods.service.SellerService"*></dubbo:reference> //从dubbo注册地址找service接口的服务，并且注册给认证类

<beans:bean class=*"org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"* id=*"bcryptEncoder"*></beans:bean> //加密实现类

</beans:beans>

我们存入数据库的密码，可以通过在controller中这样定义：

**public** Result add(@RequestBody TbSeller seller) {

// 对密码进行加密存储到数据库

BCryptPasswordEncoder passwordLock = **new** BCryptPasswordEncoder();

String newPassWord = passwordLock.encode(seller.getPassword());

seller.setPassword(newPassWord);

**try** {

sellerService.add(seller);

**return** **new** Result(**true**, "增加成功");

} **catch** (Exception e) {

e.printStackTrace();

**return** **new** Result(**false**, "增加失败");

}

}

可以通过 BCrypt 类进行封装，让数据库存入加密之后的密码即可

具体的可以看品优购的第四天的讲义。