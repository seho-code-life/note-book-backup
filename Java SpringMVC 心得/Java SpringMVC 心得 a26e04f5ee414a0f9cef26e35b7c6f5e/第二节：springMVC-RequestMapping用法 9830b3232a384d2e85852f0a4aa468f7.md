# 第二节：springMVC-RequestMapping用法

# 第二节：springMVC-RequestMapping用法

RequestMapping： 是一个用来处理请求地址映射的注解

用法不仅仅能在方法前映射url，也可以在类上定义；

### **1、 value， method；**

value： 指定请求的实际地址，指定的地址可以是URI Template 模式（后面将会说明）；

method： 指定请求的method类型， GET、POST、PUT、DELETE等；

而且可以指定请求头和请求的参数

多个条件合并使用会使url更精确

看图

![https://www.notion.so第二节：springMVC-RequestMapping用法_files/QQ截图20181016195612.png](https://www.notion.so第二节：springMVC-RequestMapping用法_files/QQ截图20181016195612.png)

RequestMapping还具有ant风格的适配符，这一块仅仅了解一下，就做了一个小demo

![https://www.notion.so第二节：springMVC-RequestMapping用法_files/QQ截图20181016201341.png](https://www.notion.so第二节：springMVC-RequestMapping用法_files/QQ截图20181016201341.png)