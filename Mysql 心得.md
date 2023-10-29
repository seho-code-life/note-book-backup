# Mysql 心得 （基本不更新了）

*# select yq_privilege_list.moudle_name from yq_privilege_list*

*# create table test1 (*

*# id int not null auto_increment,*

*# clm2 varchar(20),*

*# clm3 varchar(20),*

*# clm4 date null,*

*# primary key (`id`)*

*# )*

**alter table** test1 **add** **clm5** **char**(20); *#添加一列字段*

**alter table** test1 **drop column** **clm5**; *#删除一列字段*

**drop table** test1; *#* *删除表*

**insert into** test1(**clm2**,**clm3**) **values**(**'test1'**,**null**); *#* *插入一条数据*

**insert into** test1(**clm2**,**clm3**) **select** **clm2**,**clm3** **from** test1; *#* *插入查询出来的语句*

**create table** test2 **as select** *** **from** test1; *#* *创建新表并将查询表内容导入到新表*

**update** test1 **set** **clm4** = **'2019-1-1'** **where** **id** = 2; *#* *更新记录*

**delete from** test1 **where** **id** = 2; *#* *删除某一列*

*#* *使用更新和删除的时候，建议要跟where条件，以免全部的记录被修改和删除*

**truncate table** test1; *#* *清空所有记录*

**select distinct** **clm2** **from** test1; *#* *过滤相同值的查询*

**select** *** **from** test1 **limit** 1; *#* *查询条数，默认从0开始计数，查询一条*

**select** *** **from** test1 **limit** 1,5; *#* *查询条数，默认从1开始，查询5条*

**select** *** **from** test1 **order by** **clm3** **asc**; *#* *查询数据依靠排序，asc默认升序，desc降序*

**select** *** **from** test1 **where** **clm3** = **'0'**; *#* *条件查询* *= <= > between*

**select** *** **from** test1 **where** **clm3** **between** 0 **and** **'test2'**; *# 2者之间*

**select** *** **from** test1 **where** **clm3** **is null**;

**select** *** **from** test1 **where** **clm3** **in** (**select** **clm3** **from** test1); *#* *子查询*

**select** *** **from** test1;a