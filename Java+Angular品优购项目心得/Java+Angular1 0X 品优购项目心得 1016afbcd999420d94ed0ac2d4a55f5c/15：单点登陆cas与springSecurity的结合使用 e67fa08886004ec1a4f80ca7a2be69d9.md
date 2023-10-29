# 15：单点登陆cas与springSecurity的结合使用

# 15：单点登陆cas与springSecurity的结合使用

cas服务端就是一个jar，我们需要通过启动tomcat来运行，但是我们需要更改配置，界面等等，让它变成登陆的样子；

这里有个固定的用户名和密码 casuser /Mellon

具体的更改配置就不阐述了，尤其是cas的查询语句，去除https认证，端口修改等等，讲义在下面：

![https://www.notion.so15：单点登陆cas与springSecurity的结合使用_files/73fbe974eb15ff779919459983dd7aca.png](https://www.notion.so15：单点登陆cas与springSecurity的结合使用_files/73fbe974eb15ff779919459983dd7aca.png)

我们用cas与springseactrity结合需要用到几个pom（pinyougou-user-web）：

<dependency>

<groupId>org.springframework.security</groupId>

<artifactId>spring-security-web</artifactId>

<version>4.1.0.RELEASE</version>

</dependency>

<dependency>

<groupId>org.springframework.security</groupId>

<artifactId>spring-security-config</artifactId>

<version>4.1.0.RELEASE</version>

</dependency>

<dependency>

<groupId>org.springframework.security</groupId>

<artifactId>spring-security-cas</artifactId>

<version>4.1.0.RELEASE</version>

</dependency>

配置webxml，引入security过滤器：

<context-param>

<param-name>contextConfigLocation</param-name>

<param-value>classpath:spring/spring-security.xml</param-value>

</context-param>

<listener>

<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>

</listener>

<filter>

<filter-name>springSecurityFilterChain</filter-name>

<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>

</filter>

<filter-mapping>

<filter-name>springSecurityFilterChain</filter-name>

<url-pattern>/*</url-pattern>

</filter-mapping>

springSecurity.xml：

<?xml version="1.0" encoding="UTF-8"?>

<beans:beans xmlns="[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)"

xmlns:beans="[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)" xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)"

xsi:schemaLocation="[http://www.springframework.org/schema/beans](http://www.springframework.org/schema/beans)[http://www.springframework.org/schema/beans/spring-beans.xsd](http://www.springframework.org/schema/beans/spring-beans.xsd)

[http://www.springframework.org/schema/security](http://www.springframework.org/schema/security)[http://www.springframework.org/schema/security/spring-security.xsd](http://www.springframework.org/schema/security/spring-security.xsd)">

<http pattern="/index2.html" security="none"></http>

<!-- entry-point-ref 入口点引用 -->

<http use-expressions="false" entry-point-ref="casProcessingFilterEntryPoint">

<intercept-url pattern="/**" access="ROLE_USER" />

<csrf disabled="true" />

<!-- custom-filter为过滤器， position 表示将过滤器放在指定的位置上，before表示放在指定位置之前 ，after表示放在指定的位置之后 -->

<custom-filter ref="casAuthenticationFilter" position="CAS_FILTER" />

<custom-filter ref="requestSingleLogoutFilter" before="LOGOUT_FILTER" />

<custom-filter ref="singleLogoutFilter" before="CAS_FILTER" />

</http>

<!-- CAS入口点 开始 -->

<beans:bean id="casProcessingFilterEntryPoint"

class="org.springframework.security.cas.web.CasAuthenticationEntryPoint">

<!-- 单点登录服务器登录URL -->

<beans:property name="loginUrl" value="[http://localhost:9100/cas/login](http://localhost:9100/cas/login)" />

<beans:property name="serviceProperties" ref="serviceProperties" />

</beans:bean>

<beans:bean id="serviceProperties"

class="org.springframework.security.cas.ServiceProperties">

<!--service 配置自身工程的根地址+/login/cas -->

<beans:property name="service" value="[http://localhost:9106/login/cas](http://localhost:9106/login/cas)" />

</beans:bean>

<!-- CAS入口点 结束 -->

<!-- 认证过滤器 开始 -->

<beans:bean id="casAuthenticationFilter"

class="org.springframework.security.cas.web.CasAuthenticationFilter">

<beans:property name="authenticationManager" ref="authenticationManager" />

</beans:bean>

<!-- 认证管理器 -->

<authentication-manager alias="authenticationManager">

<authentication-provider ref="casAuthenticationProvider">

</authentication-provider>

</authentication-manager>

<!-- 认证提供者 -->

<beans:bean id="casAuthenticationProvider"

class="org.springframework.security.cas.authentication.CasAuthenticationProvider">

<beans:property name="authenticationUserDetailsService">

<beans:bean

class="org.springframework.security.core.userdetails.UserDetailsByNameServiceWrapper">

<beans:constructor-arg ref="userDetailsService" />

</beans:bean>

</beans:property>

<beans:property name="serviceProperties" ref="serviceProperties" />

<!-- ticketValidator 为票据验证器 -->

<beans:property name="ticketValidator">

<beans:bean

class="org.jasig.cas.client.validation.Cas20ServiceTicketValidator">

<beans:constructor-arg index="0"

value="[http://localhost:9100/cas](http://localhost:9100/cas)" />

</beans:bean>

</beans:property>

<beans:property name="key"

value="an_id_for_this_auth_provider_only" />

</beans:bean>

<!-- 认证类 -->

<beans:bean id="userDetailsService"

class="com.pinyougou.user.service.UserDetailServiceImpl" />

<!-- 认证过滤器 结束 -->

<!-- 单点登出 开始 -->

<beans:bean id="singleLogoutFilter"

class="org.jasig.cas.client.session.SingleSignOutFilter" />

<!-- 经过此配置，当用户在地址栏输入本地工程 /logout/cas -->

<beans:bean id="requestSingleLogoutFilter"

class="org.springframework.security.web.authentication.logout.LogoutFilter">

<beans:constructor-arg

value="[http://localhost:9100/cas/logout?service=http://localhost:9106](http://localhost:9100/cas/logout?service=http://localhost:9106)" />

<beans:constructor-arg>

<beans:bean

class="org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler" />

</beans:constructor-arg>

<beans:property name="filterProcessesUrl" value="/logout/cas" />

</beans:bean>

<!-- 单点登出 结束 -->

</beans:beans>

我们需要配置cas服务端地址以及工程地址；

我们还需要更改对应的认证类包名和类名

![https://www.notion.so15：单点登陆cas与springSecurity的结合使用_files/6996db31ab75bb22e58c6ca64a4ca230.png](https://www.notion.so15：单点登陆cas与springSecurity的结合使用_files/6996db31ab75bb22e58c6ca64a4ca230.png)

对应到具体的包

[D:\01.JAVA就业班全套视频\31.品优购电商系统开发\代码\品优购系统开发代码\day-14\pinyougou-parent\pinyougou-user-web\src\main\java\com\pinyougou\user\service](file:///D:/01.JAVA%E5%B0%B1%E4%B8%9A%E7%8F%AD%E5%85%A8%E5%A5%97%E8%A7%86%E9%A2%91/31.%E5%93%81%E4%BC%98%E8%B4%AD%E7%94%B5%E5%95%86%E7%B3%BB%E7%BB%9F%E5%BC%80%E5%8F%91/%E4%BB%A3%E7%A0%81/%E5%93%81%E4%BC%98%E8%B4%AD%E7%B3%BB%E7%BB%9F%E5%BC%80%E5%8F%91%E4%BB%A3%E7%A0%81/day-14/pinyougou-parent/pinyougou-user-web/src/main/java/com/pinyougou/user/service)

期待添加。。