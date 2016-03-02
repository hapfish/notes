

# MySQL技术内幕(第四版) MySQL Fourth Edition #


## 前言 ##
RDBSMS  (Relational Database Management System)  关系数据库管理系统  
本书是第四版, 讨论的是MySQL 5.0.  

第一部分  1-5章  MySQL基础知识  
第二部分  6-9章  MySQL编程知识  
第三部分  10-14章  MySQL系统管理
第四部分  附录  




## 第一章 MySQL和SQL入门 ##
SQL (Structured Query Language)  结构化查询语言  
数据库管理系统通常被人们用来取代文件柜.   

Civil War  南北战争  
Depression  经济大萧条  
civil right  人权法案  
Thomas Jefferson  托马斯.杰弗逊  

数据库  Database  就是一个用来存放信息的仓库.  
> 数据库里的数据集合都存放在数据表(table)里.  
> 数据表由数据行(row)和数据列(column)构成.  
> 一个数据行就是数据表里的一条记录(record).  
> 记录可以包含多个信息项, 数据表里的每一个数据列都对应一个信息项.  


第三范式  Third Normal Form  
实体联系图  Entity Relationship Diagram  

MySQL采用的是客户／服务器体系结构. 一个程序是MySQL服务程序, 指的是mysqld程序． 另一个程序是MySQL客户程序, 负责连接到数据库.  

GUI 图形用户界面  http://www.mysql.com/products/tools  
libmysqld称为嵌入式服务器库 (embedded server library)  
MySQL指的是一个完整的MySQL RDBMS  
mysql则是一个特定的客户端程序的名字  
`<<MySQL参考手册>>`给出发音 my-ess-queue-ell. SQL读音有"sequel"和"ess-queue-ell"  
mysql命令行可以使用exit或\q退出, 在unix系统下, 利用组合 ctrl-D也可以退出.  
敲入有关命令, 再命令的末尾敲入一个分号字符(;), 另一种方法是使用\g 或\G (表示GO)  

` mysql > select now(), user(), version()  \G `  
\c 可以消除(即取消)命令.  
函数名与它后面的括号中间不允许出现空格, 有时空格会导致语法错误.  
执行语句 ` mysql < myscript.sql `  
` mysql > select database(); `  核实一下当前连接的数据库.  
` mysql > create database sampdb; `  创建数据库  
` mysql > use sampdb; `  连接数据库  
` > mysql -h host.net -p -u user database `  连接到指定数据库  
` mysql > describe tableName `  查看表结构  
` mysql > show tables; `  显示当前数据库所有表  
` mysql > show databases `  显示当前连接所有数据库  

show 语句查看到的信息可以使用mysqlshow程序查到  
```
mysqlshow
mysqlshow databaseName

```

ISAM 是"indexed sequential access methon" 索引化顺序访问方法的缩写  

insert添加数据  
> 一次性列出全部数据列的值  
> 先给出数据列的名字, 再列出它的值  
> 包含col_name:value的set子句对数据列赋值  

source命令只能用在MySQL 3.23.9或者更高版本.  
包含NULL值的数据行, 设定升序排序将出现在查询结果的开头; 设定按降序排序, 将出现在查询的末尾.  


concat 合并字符串  
` mysql> select concat(first_name,' ',last_name) from table1; `  


` select sex, count(*) from student group by sex; `  
count(*)  返回记录个数; count(columnName)  统计非NULL的值.  

having 子句与where子句的不同之处是count()之类的汇总函数的计算结果允许出现在having子句里出现. 适合查询重复出现的值或不重复出现的值(having count = 1)  
with rollup可以称生成统计结果  
` select sex, count(*) from student group by sex with rollup; `  


运行mysql程序  
` % mysql database < select.sql `  
制表分隔符,可以达到整齐效果  
` % mysql database < select.sql `  
重定向结果到文件  
` % mysql -t database < select.sql > sql.log `


导入数据  
` mysql> source data.sql `








  




  





