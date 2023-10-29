# Spring-02：spring与junit整合测试

# Spring-02：spring与junit整合测试

首先介绍一下junit框架：

JUnit是一个Java语言的单元测试框架。它由Kent Beck和Erich Gamma建立，逐渐成为源于Kent Beck的sUnit的xUnit家族中最为成功的一个。 JUnit有它自己的JUnit扩展生态圈。多数Java的开发环境都已经集成了JUnit作为单元测试的工具

spring和junit框架的整合测试

//创建容器

@RunWith(SpringJUnit4ClassRunner.**class**)

@ContextConfiguration("classpath:applicationContext.xml")

**public** **class** main {

@Resource(name="user")

**private** User u;

@Test

**public** **void** text() {

System.***out***.println(u);

}