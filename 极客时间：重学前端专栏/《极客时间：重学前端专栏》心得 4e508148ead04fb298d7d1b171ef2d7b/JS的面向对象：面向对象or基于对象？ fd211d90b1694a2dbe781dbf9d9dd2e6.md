# JS的面向对象：面向对象or基于对象？

# JS的面向对象：面向对象or基于对象？

从人类的认知角度来说：

对象是一切事物可以触摸可以看见的东西

人的智力可以理解的东西

可以知道思考或者行动的东西

这个是面向对象分析与设计书中提到的

各个编程语言也用这种理念抽象出来描述对象，最成功的就是使用类的对象，诸如c++，java

但是js早期就采用了冷门的方式，“原型”，关于原型，下一篇会讲到

但是js在早期推出的时候，受到管理高层的影响，所以作者加上了new，this等字眼，所以es6之前很多js程序员视图在原型体系的基础上变成更基于类的编程，所以产生了很多框架，但是这都是收益小于损失的。

对象有如下几个特点：

对象具有唯一标识性：即使完全相同的两个对象，也并非同一个对象。

对象有状态：对象具有状态，同一对象可能处于不同状态之下。

对象具有行为：即对象的状态，可能因为它的行为产生变迁。

第一个特征就是标识性，对象具有唯一标识的内存地址，所以一模一样的2个对象其实是不相等的

再看第二和第三的特征和行为，不同的语言会使用不同的术语来抽象描述它们，但是在js中

会统称为属性，所以就没有行为和状态这一说。