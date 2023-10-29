# Oracle 心得（基本不更新了）

创建一个表：

cteate table 表名（列1 类型，

列2 类型）;

查询所有序列：

select * from user_sequences;

查看表结构 desc表名

添加一个字段 alter table 表名 add（列类型）;

修改字段类型 alter table 表名 modify(列类型);

删除一个字段 alter table 表名 drop column列名;

修改表的名字 rename student to stu

删除表 drop table 表名

插入数据:

1、 create table test(

id number,

name varchar2(20),

age number(2)

);

2、

insert into 表名 values(所有列的值);

insert into test values(1,'zhangsan',20);

插入的的数据应该与字段数据类型相同

数据的大小应该在列规定的范围内

在values中列出的数据位置必须与被加入的列的排列位置响对应

3、

insert into 表名（列）values(对应的值);

insert into test(id,name)values(2,'lisi');

更新语句：

update 表 set 列=新的值[where 条件]--》更新满足条件的记录

update test set name='zhangsan2'where name='zhangsan'

update 表 set 列=新的值--》更新所有的数据

update test set age=20;

删除数据：

1、delete from 表名 where 条件--》删除满足条件的记录

delete from test where id=1;

delete from test-->删除所有

commit；--》提交数据

rollback；-->回滚数据

2、truncate table 表名

删除所有数据，不会影响表结构，不会记录日志，数据不能恢复--》删除很快

3、drop table 表名

删除所有数据，包括表结构一并删除，不会记录日志，数据不能恢复--》删除很快

如何看一个SQL执行了多长时间：set timing on

如何快速的复制数据:1、insert into test select * from test;

2、create table 表名 select * from test;

导入数据：@文件名

去除重复的显示：select distinct 列 from 表名

日期类型:to_date(字符串1，字符串2)字符串1是日期的字符串，字符串2是格式

to_date('1990-1-1','yyyy-mm-dd')-->返回日期的类型是1990-1-1

SQL的模糊查询：like

查询结果需要排序 order by字段

select * from emp order by sal-->> order by后面，默认升序

asc 升序 desc降序

group by 字段--》按照指定的字段进行升序

having 字句--》对分组后的结构进行筛选输出