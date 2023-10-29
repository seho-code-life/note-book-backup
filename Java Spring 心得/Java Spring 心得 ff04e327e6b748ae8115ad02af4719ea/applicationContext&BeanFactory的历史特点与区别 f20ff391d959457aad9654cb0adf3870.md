# applicationContext&BeanFactory的历史特点与区别

# applicationContext&BeanFactory的历史特点与区别

在spring初创的时候最早的接口是BeanFactory

这个接口一开始的功能是非常简陋的，一直到后面的版本迭代，后面的功能不断的扩充，到了applicationContext才是我们常用的

他们的缺点是：

首先是历史层面的：

spring初创的的时候是0几年，电脑设备较为落后，所以BeanFactory他的特点就是：

每次在获得对象时才会创建对象

而慢慢发展到applicationContext，特点就是：

每次容器启动的时候就会创建配置的对象

在applicationContext下加载xml文件有2种方式：

从类路径下加载配置文件:ClassPathXmlApplicationContext(cc.xml);

从硬盘绝对路径下加载配置文件:FileSystemXmlApplicationContext("d:/xxx/yyy/xxx")

结论：结论:web开发中,使用applicationContext. 在资源匮乏的环境可以使用BeanFactory.