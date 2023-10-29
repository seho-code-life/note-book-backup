# Spring-03：整合JDBC模板template

# Spring-03：整合JDBC模板template

首先本节练习在javawork文件夹中的spring—03JDBC

首先准备包

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917093556.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917093556.png)

spring4个基础包，还有junit的2个包

以及mysql驱动包还有连接池的包

首先我们在main中写代码

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917094234.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917094234.png)

---

我们把增删改查封装，然后进行api进行分析

首先我们先写接口，然后实现类

![https://www.notion.sofile:///C:/Users/LAOSHEN/AppData/Roaming/Tencent/Users/1018715564/QQ/WinTemp/RichOle/TD_HMJZ%%5BF7$0T)M@U_N)TM.png](https://www.notion.sofile:///C:/Users/LAOSHEN/AppData/Roaming/Tencent/Users/1018715564/QQ/WinTemp/RichOle/TD_HMJZ%%5BF7$0T)M@U_N)TM.png)

接口：

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917181956.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917181956.png)

实现类：

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917181956%20%5B1%5D.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917181956%20%5B1%5D.png)

查询方法（掌握）：

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917183004.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917183004.png)

还有我们的查询全部的方法：

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917185922.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917185922.png)

然后JDBCtemplate模板中还提供了一个自动生成teamplate的方法，就是在实现接口的地方继承一个父类

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917190332.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917190332.png)

同样的，我们的xml中也要改变相应的配置，由于jdbctemplate已经被我们的父类替代，所以我们要删除相关的配置

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917190950.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917190950.png)

注意事项：

在xml中配置dao层对象或者其他对象的时候，我们需配置它的实现类，不要配置接口

而在我们的引用中，要被注入的是接口，而不是实现类

例如：如上图，class名称为IDaoImpl，映射了他的实现类。

而我们注入的时候

![https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917191420.png](https://www.notion.soSpring-03：整合JDBC模板template_files/QQ截图20180917191420.png)