

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








  
## 第二章 使用SQL管理数据 ##

存储函数  stored function  
存储过程  stored procedure  

MySQL服务器有一个sql_mode系统变量, 可以让你调控其SQL模式.  
SQL模式  
> STRICT_ALL_TABLES 和 STRICT_TRANS_TABLES 启用严格模式  
> TRADITION 类似严格模式的混合模式, 更接近于传统的SQL服务器  
> ANSI_QUOTES 告诉MySQL服务器把双引号识别为一个标识符引用字符  
> PIPES_AS_CONCAT 将导致 "||" 视为一个标准的SQL字符串合并操作符  
> ANSI 是一种复合模式, 同时启用ANSI_QUOTES, PIPES_AS_CONCAT.   

模式不区分大小写.  
查询会话级和全局级SQL模式  
```  
select @@SESSION.sql_mode;  
select @@GLOBAL.sql_model;  

```  

绝大多数标识符的最大长度64个字符, 假名的最大长度是256个字符.  

想指定一个数据库表名, 有两种选择:  
> 使用完整的数据库表名.  
> 一个数据表标识符本身对应着默认的(当前)数据库里的一个数据表.  

对数据表名加以限定的考虑同样适用于视图(它们是"虚拟的"数据表)和存储过程的名字.  


想指定一个数据列有3种选择:  
> (1) 使用完整的数据列名.  
> (2) 对默认数据库里某给定数据表的一个数据列, 可以使用tbl_name.col_name的形式部分限定名.  
> (3) 只写出一个非完整名col_name表示该数据列属于上下文环境所确定的那个数据表.  


一般来说, 没有必要提供完整的名字. 只有在无法根据上下文确定数据表或数据库的时候才必须使用完整的标识符.  
如果打算在引用一个完整名字时使用引号, 就应该给该名字里的每一个标识符分别加上引号. 例如 'database'.'tableName' .  
把某个保留字用作标识符时, 必须给它加上引号. 但这个保留字紧跟在一个句号限定符后面时例外. 因为已经可以根据上下文而确定这个保留字其实是一个标识符.  
  
  
SQL语句中的大小写问题:  
> (1) SQL关键字和函数名不区分字母大小写, 但输出结果中的数据列标题将是不同字母大小写组合.  
> (2) 数据库, 数据表个视图的名字取决于服务器主机上的操作系统. windows文件名不区分大小写, Unix主机上的区分大小写. Mac OS X平台上的HFS+文件系统是个例外, 不区分字母的大小写.  
> (3) 存储函数, 存储过程和事件的名字不区分字母的大小写, 触发器的名字要区分字母的大小写. 这一点和标准SQL行为是不一样的.  
> (4) 数据列和索引的名字在MySQL环境里不区分字母的大小写.  
> (5) 数据表别名在默认情况下区分字母大小写. 如果需要在同一条语句里多次用到同一个别名, 就必须保持同样的字母大小写组合. 如果lower_case_table_banes变量是非0值, 数据表别名将不区分字母的大小写.  
> (6) 字符串值是否区分字母大小写, 取决于它是二进制还是非二进制. 非二进制还要取决于字符集的排序方式.  


避免字母大小写问题演变成棘手难题的办法之一, 是选定一种字母大小写方案, 一直遵照该方案去创建数据库和数据表.  
建议统一使用小写字母, 这在使用InnoDB数据表时也有益处. 因为InnoDB引擎在其内部是把数据库和数据表的名字保存为小写字母.  
MySQL不仅支持多种字符集, 还允许对服务器, 数据库, 数据表, 数据列或字符串常数级别的字符集做出互不影响的设定.  
选用一种Unicode字符集(用单一编码方案表示多种语言的字符)去实现多语言支持和排序方式.  
服务器字符集在启动时使用 --character-set-server 和 --collation-server 选项, 启动后设置 character-set-server 和 collation-server 覆盖它们.  


字符集和排序方式规则:  
> (1) 数据库创建时  
` create database db_name character set charsetName collate collatonName `  
如果未设置, 服务器级别设置将传递给这个数据库.  
> (2) 数据表创建时  
` create table tbl_name character set charsetName collate collationName  `  
如果未设置, 数据库级别默认值将传递给这个数据表.  
> (3) 数据列  
` c char(10) character set characterName collate collationName  `  
如果未设置, 数据表级别的设置将传递给这个数据列. 适用于 char, varchar, text, enum 和 set 数据类型.  
  
  
可以利用 collate 操作符按照特定方式对字符串值排序.  
` select c from t order by c collate latin1_spanish_ci; `  


查看字符集和排序方式:  
```
show character set;  
show collation;  

```  
支持 like 过滤  
` show collation like 'utf8%'; `  

字符集和排序方式信息, 还可以从information_schema数据库中的character_sets和collations数据表查到.  
显示服务器的当前字符集和排序方式的设置情况, 可以使用  
```
show variables like 'character\_set\_%';  
show variables like 'collation\_%';  

```

UTF使用一到三字节表示一个字符, Unicode Transformation Format(统一编码转换格式)的缩写.  

不存在才创建数据库  
` create database if not exists db_name; `  

drop database 会删除数据库和其中的所有东西. drop database 语句失效, 通常因为那个数据库子目录里还包含有一些与数据库对象无关的文件. 真想删除那个数据库, 必须手动删除该数据库子目录里遗留的文件和子目录本身. 然后再发出drop database.  




MySQL支持好几种存储引擎(Storage engine, 以前成为"数据表处理器")  
查询服务器支持的存储引擎  
` show engines; `  

MySQL支持的存储引擎:  
> (1) ARCHIVE 用于数据存档的引擎(数据行被插入后就不能再修改了)  
> (2) BLACKHOLE 这种存储引擎的写操作是删除数据, 读操作是返回空白记录  
> (3) CSV 这种存储引擎在存储数据时以逗号作为数据项之间的分隔符  
> (4) EXAMPLE 示例(存根)存储引擎   
> (5) Falcon 用来进行事务处理的存储引擎  
> (6) FEDERATED 用来访问远程数据表的存储引擎  
> (7) InnoDB 具备外键支持功能的事务处理引擎  
> (8) MERGE 用来管理由多个MyISAM数据表构成的数据表集合  
> (9) MyISAM 默认的存储引擎  
> (10) NDB MySQL Cluster专用存储引擎  

show engines 中 transaction 栏里的值表名存储引擎是否支持事务, XA 和 Savapoints 栏里的值表名某种存储引擎是否支持分布式事务处理(本书不讨论)和部分事务回滚.  

MySQL 把数据库的字符集和排序方式等属性保存在相应的 db.opt , 数据表格式扩展名是 .frm (MyISAM).  

由存储引擎创建的数据表文件  
> (1) MyISAM  .MYD (数据)  .MYI (索引)  
> (2) MERGE  .MRG (由各成员MyISAM数据表的名字构成的清单)  
> (3) InnoDB  .ibd (数据和索引)  
> (4) ARCHIVE  .AR2 (数据)  .ARM (元数据)  
> (5) CSV  .CSV (数据)  .CSM (元数据)  

MyISAM 支持全文检索, 需要通过FULLTEXT索引来实现.  



MERGE 数据表提供了一种把多个MyISAM数据表合并为一个逻辑单元的手段. 查询一个MERGE数据表相当于查询所有的成员数据表. 这种好处之一是可以绕开文件系统对各个MyISAM数据表的最大长度限制.  
构成MERGE数据表的所有数据表必须有同样的结构. 必须为各成员数据表里的数据定义同样的名字, 同样的类型和同样的顺序. 索引也必须以同样的办法按同样的顺序定义.  


MEMORY 存储引擎把数据表保存在内存里, 这些数据有着长度固定不变的数据行. 这两个特点使得数据表的检索速度非常快.  
MEMORY 数据表是临时性的, 当服务器掉电时, 表中的内容也就消失了. MEMORY数据表在服务器重启后仍会存在, 只是它们的内容将是一片空白. MEMORY数据表的另一个特点是其内容对其它客户来说是可见的. 这与用 create temporary table 语句创建出来的临时数据表形成了对照.  
> (1) 在默认的情况下, MEMORY数据表使用散列索引. 利用索引进行 "相等比较" 的速度非常快. 但进行 "范围比较" 的速度慢多了. 散列索引适合用在使用 "=" 和 "<=>" 操作符进行的比较操作符.  
不合适用在使用 "<" 和 ">" 操作符进行的比较操作里. 同样, 散列索引也不适合用在 order by 子句里.  
> (2) MEMORY 数据表里的数据行使用的是长度固定不变的格式. 以此加快处理速度. 这意味着你不能使用 BLOB 和 TEXT 这样长度可变的数据类型.  
VARCHAR 是一种长度可变的类型, 但因为它在MySQL内部被当作一种长度固定不变的 CHAR 类型, 所以可以在MEMORY数据表里使用 VARCHAR 类型.  

如果确实需要 MEMORY 数据表和 "<" , ">" 或 between 操作符进行某种比较以判断某个值是否在某个范围. 可以使用 BTREE 索引来加快速度.  


InnoDB 存储引擎最早由Innobase Oy公司开发, 该公司后来被Oracle收购. 功能如下:  
> (1) 支持提交和回滚, 可以通过保存点(savepoint)的办法来实现部分回滚 (partial roolback).  
> (2) 系统崩溃后可以自动恢复.  
> (3) 外键和引用完整性支持. 包括递归式删除和更新.  
> (4) 数据行级别的锁定和多版本共存. 在需要同时进行检索和更新操作的复杂查询里表现出非常好的并发性能.  
> (5) 在默认情况下, InnoDB存储引擎会把数据表集中存储在一个共享的表空间里. InnoDB表空间可以由多个文件构成. 还可以包括多个原始分区. 
InnoDB 表空间就像一个虚拟的文件系统, 它存储和管理所有InnoDB 数据表的内容. 也可以把InnoDB存储引擎配制成会为每个数据表分别创建一个表空间的样子. 此时, 每个数据表在它的数据库子目录里都有一个对应的 .ibd 文件.  

Falcon 存储引擎从MySQL 6.0 开始才有的, 它有以下功能:  
> (1) 支持提交和回滚操作, 可以通过创建保存点来实现部分会滚.  
> (2) 系统崩溃后可以自动恢复.  
> (3) 灵活的锁定级别和多版本共存, 使得Falcon数据表在需要同时进行检索和更新操作的复杂查询里表现出非常好的并发性能.  
> (4) 在存储时对数据进行压缩, 在检索时对数据进行压缩以节省空间.  
> (5) 日常管理和维护方面的开销低.  

FEDERATED 存储引擎的用途是访问其他MySQL服务器管理下的数据表. FEDERATED数据表的内容不是存放在本地主机里的.  
创建一个FEDERATED数据表时, 需要指定一台运行着其它服务程序的主机, 并提供那个服务器的合法账户的用户名和口令. 访问FEDERATED数据表时, 本地服务器将使用这个账户连接那台远程服务器.  

NDB 存储引擎是MySQL的集群(cluster)存储引擎. MySQL服务器的作用是帮助其它进程访问NDB数据表. 从整个集群的高度看, 其行为像是一个客户. 各集群节点上的进程通过彼此通信来管理内存中数据表. 为了减少冗余, 数据表在集群中被复制. 内存存储提供了高性能, 集群机制提供了高度可用性. 即使个别节点发生故障, 系统也不会崩溃.  

ARCHIVE 存储引擎对数据进行归档, 它最适合用来大批量地保存那些 "写了就不改" 的数据行. 只支持很有限的几条SQL语句. INSERT 和 SELECT 语句没问题, 但 REPLACE 语句的行为却永远像是 INSERT 语句, 而 DELETE 或 UPDATE 语句根本不起作用. 每个 ARCHIVE 数据表最多也只能有一个带索引的 AUTO_INCREMENT 数据列, 其它数据列还是不能带索引.  

BLACKHOLE 存储引擎创建的数据表有这样的行为特点, 写操作其实是删除数据, 而读操作是返回空的记录.  

CSV 存储引擎在存储数据时以逗号作为数据项之间的分隔符. 它会在数据库子目录里为每个数据表创建一个 .csv 文件, 这是一种普通文本文件, 每个数据行占用一个文本行, CSV存储引擎不支持索引.  

EXAMPLE 存储引擎是用来演示如何编写存储引擎的最小化样板. 它的存在价值在于让开发人员通过查看其源代码去学习怎样才能正确地把存储引擎加载到服务里.  




存储引擎的可移植性, 从某种意义上将, 任何一个MySQL服务器所管理的任何数据表都可以移植到另一台服务器上去. 先用mysqldump工具把它备份出来, 然后把备份文本文件放到另一台服务器主机. 并通过加载备份文件的办法重新创建该数据表.  
可移植性概念还有另一层含义, 即二进制可移植性 (binary portablity). 指的是你可以直接把代表某个数据表的硬盘文件复制到另一台机器, 并把它们安装到数据子目录下的相应地点, 然后那台机器上的MySQL服务器就可以使用该数据表了.  

数据表具备二进制可移植性的一项基本条件是源服务器和目标服务器的有关功能必须要兼容. 比如说, 目标服务器必须支持用来管理数据表的存储引擎.  

各个存储引擎的二进制可移植性总结:  
> (1) MyISAM和InnoDB数据表的存储格式与机器无关, 它们具备二进制可移植性 -- 前提条件是你的处理器使用的是二进制补码整数算法和IEEE浮点格式.  
> (2) MERGE 数据表的可移植性取决于其成员MyISAM数据表, 只要那些MyISAM数据表是可移植的, MERGE数据表就是可移植的.  
> (3) MEMROY 数据表不具备二进制可移植性, 因为它们的内容都存储在内存里而不是硬盘上.  
> (4) CSV 数据表是二进制可移植的, 因为 .CSV文件本质上都是普通的文本文件.  
> (5) BLACKHOLE 数据表是二进制可移植的, 因为它们根本不包含任何内容.  
> (6) FEDERATED 存储引擎, 可移植性概念与之无关, 因为 FEDERATED数据表的内容都是存储在另一个服务器.  
> (7) Falcon 日志和表空间文件的存储格式与机器有关. 它们只在两台机器的硬件特性完全相同时才是二进制可移植的. 比如说, 不能把一台低字节优先的机器里的Falcon文件移植到一台高字节优先的机器.  


"浮点数" 指的是FLOAT和DOUBLE类型, 不包括DECIMAL类型. DECIMAL数据列里的数据值的小数点位置是固定的.   

对InnoDB存储引擎而言, 二进制可移植的另外一个条件是数据库和数据表的名字应该由小写字母组成. InnoDB存储引擎在其数据字典里把这些名字统一保存为小写字母格式. 但 .frm 文件名里的字母却是与你在 create table 语句使用的大小写情况完全一样.  

如果服务器意外关闭, 在它关闭后得到的副本将无法确保数据的完整性不受到影响.  

存储引擎的名字不区分大小写.  

如果不想让MySQL在指定的存储引擎不可用时, 使用默认的存储引擎, 需要激活 NO_ENGINE_SUBSTITUTION SQL 模式.  

show crate table tableName 可以查看数据表的存储引擎, 存储引擎还可以通过 show table status 或 information_schema.tables 查看.  

MAX_ROWS 和 AVG_ROW_LENGTH 选项可以控制MyISAM数据表大小.  
想把mytable数据表使用的存储引擎改为InnoDB, 使用:  
` alter table mytable engine = InnnoDB  `  

MySQL中if not exist 短句执行时, 不会去比较create table 语句里的数据表的结构与已存在的那个数据表是否一致. 如果不想冒险, 可以先执行drop table if exist, 再执行一条不带 if not exist 短句的 create table语句.  

在创建数据表语句里加上 temporary 关键字, 服务器将创建出一个临时的数据表, 它在你与服务器的连接断开时自动消失.  
` created temporary table tableName; `  

一个temporary数据表只对创建该数据表的客户是可见的. 因为每个客户端只能看到它自己创建的数据表, 所以不同的客户可以创建一个名字相同的temporary数据表不会发生冲突.  
一个temporary数据表的名字允许与一个现有的永久性数据表相同.  
如果客户程序会在与服务器的连接意外断开时自动重建连接, 上次创建的temporary数据表在你重新连接上服务器时将不复存在. temporary数据表只对创建它们的连接是可见的, 所以如果使用了某种连接池机制, 它们的用处就没有多大了, 因为连接池机制不能保证你发出的每一条语句使用都是同一个连接. 如果使用了连接池或永久连接, 创建的temporary数据表不一定会在你的应用程序结束时自动消失.  



从其它的数据表或是查询结果创建新的数据表:  
> (1) create table ... like 将创建一个新的数据表作为原始数据表的一份空白副本. 数据列所有属性都会得到保留, 索引结构也一模一样.  
如果要填充数据, 需要使用insert into ... select.  不能从原始数据表的数据列子集创建一个新的数据表, 也不能使用原始数据表以外的任何其他数据表的数据列.  
> (2) create table ... select 语句可以从任意一条select语句的查询结果创建新的数据表. 不会复制所有列的数据列属性, 如 AUTO_INCREMENT 等.  
因为结果集本身不带索引, 也不会自动复制原始数据表的索引. 还可以用原始数据表的一个子集创建一个数据表, 并包括来自其它数据表的数据列作为表达式结果而被创建出来的数据列.  



```  
create table new_tableName like tableName;  

insert into new_tableName select * from tableName;  


create temporary table new_tableName like tableName;  

insert into new_tableName select * from tableName;    
insert into new_tableName select * from tableName where sex = 'f';  


create table new_tableName select * from tableName where sex = 'f';  

```

可以使用别名替代表达式本文:  
` create table mytable select PI() * 2 as myCloumn;  `  

select部分使用cast()函数的办法, 在新数据表里强制使用特定的属性:  
```  
mysql> create table mytable select  
    -> cast(1 as unsigned) as i,  
    -> cast(curtime() as time) as t,  
    -> cast(pi() as decimal(10,5)) as d; 

mysql> describe mytable;	
  
```

允许投射的类型是binary(二进制), char, date, datetime, time, signed, signed integer, unsigned, unsigned integer 和 decimal.  
也可以在cteate table时明确数据列定义:  
```  
mysql> create table mytable (i int unsigned, t time, d decimal(10,5))  
    -> select  
	-> 1 as i,  
    -> cast(curtime() as time ) as t,  
    -> cast(pi() as decimal(10,5)) as d;

mysql> describe mytable;	
```  

使用MERGE数据表, 构成一个MERGE数据表的各成员MyISAM数据表必须具有完全一样的结构. 每一个成员数据表里的数据列必须按照同样的顺序定义同样的名字和类型. 索引也必须按照同样的顺序和同样的方式定义.  
假设有几个日志数据表, CC代表世纪, YY代表年份:  
```  
create table log_CCYY (
  dt datetime not null,
  info varchar(100) not null,
  index (dt) 
  ) engine = MyISAM;
  
```  
MERGE数据表把它们归拢为一个逻辑单元:  
```  
create table log_merge (
  dt datetime not null,
  info varchar(100) not null,
  index (dt)
  ) engine = MERGE union = (log_2004, log_2005, log_2006, log_2007, log_2008);

```  

engine选项的值必须是merge, union选项列出了被收录在这个merge数据表里的有关数据表, 查询总行数:  
` select count(*) from log_merge; `  



使用分区表, MySQL 5.1及更高版本支持分区表(partitional table). 分区表与MERGE的区别是:  每个分区表都是一个货真价实的数据表, 而不是一个用来列出各成员的逻辑构造. 分区表可以使用MyISAM以外的存储引擎, 而MERGE数据表只能用MyISAM数据表来构成.  
分区函数把新数据行分配到不同分区的依据可以是: 取值范围, 值的列表 或 散列值.  
分区函数必须具备这样一种确定性, 同样的输入永远会把数据行分配到同一个分区. 按照这一要求, 诸如 RAND() 和 NOW() 之类的函数显然不适合做分区函数.  
根据年份来把数据行分配到一个给定的分区:  
```  
create table log-partition (
  dt datetime not null,
  info varchar(100) not null,
  index (dt)
  ) partition by rang (year(dt)) (
  partition p0 values less than (2005),
  partition p1 values less than (2006),
  partition p2 values less than (2007),
  partition p3 values less than (2008),
  partition p4 values less than MAXVALUE 
  );

```  
根据上面的定义, 2008年和以后的日志项将被分配到MAXVALUE分区.  
2009年时可以对这个分区进行划分, 把2008年的日志记录转移到他们自己的一个分区里. 把2009年和以后的日志记录项仍然保留在MAXVALUE分区里:  
```  
alter table log-partition reorganize partion p4 into (
  partition p4 values less than (2009),
  partition p5 values less than MAXVALUE);
  
```  
若想将存储分配到其它位置(如另外一个物理设备), 需要使用 DATA_DIRECTORY 和 INDEX_DIRECTORY 分区选项.  




使用FEDERATED数据表, FEDERATED存储引擎可以访问其它主机由另一台MySQL服务器实际管理的数据库.  
在本地服务器上创建一个相应的DEFERATED数据表必须使用同样的定义, 还必须把engine选项设置为FEDERATED. 再通过connection连接选项给出建立连接所必须的信息. (MySQL 5.0.13之前的版本需要用comment选项替代connection选项). 例如:  
```  
create table federated_student (
  name varchar(20) not null,
  sex enum('F', 'M') not null,
  student_id int unsigned auto_increment,
  primary key (student_id)
  ) engine = FEDERATED 
  connection = 'mysql://sampadm:secret@demo.net/sampdb/student';
  
```  
可以通过对federated_student数据表进行insert, update和delete操作的办法去改变远程的student数据表的内容.  
整个connection字符串(包括用户名和口令)对可以使用show create table或类似语句去查看FEDERATED数据表的任何人来说都是可见的. 从MySQL 5.1.15版本开始, 可以避免这个问题.  
提前使用create server 语句创建一个存储服务器定义(需要super权限), 然后在connection选项写出该服务器的名字即可. 定义名为sampdb-server的存储服务器:
``` 
create server sampdb-server 
  foreign data wrapper mysql 
  options (
     USER 'sampadm',
     PASSWORD 'secret',
     HOST 'demo.net',
     DATABASE 'sampdb'
  );
  
```  
MySQL服务器将把这个定义保存为mysql数据库中servers数据表的一个数据行.  
引用服务器定义的数据表:  
```  
create table federated_student (
  name carchar(20) not null,
  sex enum('F','M') not null,
  student_id int unsigned not null auto_increment,
  primary key (student_id)
  ) engine = FEDERATED 
  connection = 'sampdb-server/student';

```  
与把连接参数直接写在connection选项里的作法相比, 使用服务器定义可以提高安全性. 因为这样定义只有那些有访问mysql数据库的用户才能查看到.  



删除数据库  
```
drop table tableName;
drop table tableName1, tableName2;

drop table is exists tableName;

```  
if exist 子句适合用在通过mysql客户工具去运行的脚本里. 在默认情况下, mysql客户工作在有错误发生时退出运行, 而删除一个并不存在的数据表是一个错误.  

删除临时数据表, 加上temporary关键字即可    
` drop temporary table tableName; `  


 
 

使用FULLTEXT或SAPTIAL索引, 就必须使用MyISAM数据表. 对一个TEXT数据列编索引, 就必须使用MyISAM或InnoDB数据表.  
MySQL索引:
> (1) 唯一索引  不允许索引本身出现重复的值. 对涉及一个数据列的索引来说, 意味着数据列不能包含重复的值. 对涉及多个数据列的索引(复合索引)来说, 意味着那几个数据的值的组合在整个数据表的范围内不能出现重复.  
> (2) 普通索引  优点是允许索引值出现重复.  
> (3) FULLTEXT索引  只适用于MyISAM数据表.  
> (5) SPATIAL索引  只适用于MyISAM数据表和空间(SPATIAL)数据类型.  
> (6) HASH索引  这是 MEMORY数据表 的默认索引类型, 可以改用BTREE索引来代替这个默认索引.  

用alter table 或 create index 语句可以给现有数据库添加索引. (MySQL内部把create index 语句映射为alter table操作)  
```  
alter table tableName add index indexName (indexColumns);
alter table tableName add unique indexName (indexColumns);
alter table tableName add primary key (indexColumns);
alter table tableName add FULLTEXT indexName (indexColumns);
alter table tableName add spatial indexName (indexColumns);

```  
如果索引由多个数据列构成, 要用逗号把它们分开.  
primary key或spatial索引的数据列必须具备 not null 属性, 其它用途的索引都允许包含 null 值.  
限制某个索引只包含独一无二的值, 可以把该索引创建为primary key或unique. 两者区别:  
> (1) 每个数据表只能有一个 primary key.  
> (2) primary key 不可以包含null值, 而unique索引可以. (MySQL无法比较null值)  

除了primary key, 其它类型的索引几乎都可以用create index语句添加:  
```  
create index indexName on tableName (indexColumns);
create unique index indexName on tableName (indexColumns);
create fulltext index indexName on tableName (indexColumns);
create spatial index indexName on tableName (indexColumns);

```  
使用create table语句创建新数据表的同时创建索引, 需要在定义各数据列的基础上再增加一些索引创建子句:  
```  
create table tableName (
   ...
   index indexName (indexColumns),
   unique indexName (indexColumns),
   primary key (indexColumns),
   fulltext indexName (indexColumns),
   spatial indexName (indexColumns),
   
   ...
   );
   
```  

MEMORY数据表的默认索引类型是HASH, 创建一个BTREE索引来替代它的具体做法是在索引定义里增加一条 using BTREE子句:  
```  
create table namelist (
   id int not null,
   name varchar(100),
   index using BTREE (id)
  ) engine = MEMORY;
  
```  

前缀索引, 对char数据列前10个字符和binary数据列前15个字节编索引:  
```  
create table addresslist (
   name char(30) not null,
   address binary(60) not null,
   index (name(10)),
   index (address(15))
  ); 

```  
前缀编索引时, 二进制字符串以字节为单位, 非二进制字符串以字符为单位.  
BLOB和TEXT只能创建前缀索引.  
FULLTEXT索引所涉及的数据列是全部带索引的, 没有前缀索引. 即使为FULLTEXT索引所涉及的某个数据列指定了一个前缀索引长度, MySQL会忽略它.  

删除索引  
` drop index indexName on tableName; `  

删除primary key, 必须以一个带引号的标识符形式给出:  
` drop index 'PRIMARY' on tableName; `  

alter table语句删除索引:  
```  
alter table tableName drop index indexName;
alter table tableName drop primary key;

```  


改变数据列的数据类型:  
```  
alter table tableName modify i mediumint unsigned;
alter table tableName change i i mediumint unsigned;
alter table tableName change i k mediumint unsigned;
alter table tableName modify c char(20) character set ucs2;

```  

让数据表改用另一种存储引擎:  
` alter table tableName engine = engineName `  
engineName是MyISAM, MEMORY或InnoDB之类的名字, 不区分字母大小写. 取决于新老两种存储引擎的功能是否兼容.  


MEMORY引擎不支持BLOB数据列, MyISAM数据表包含FULLTEXT或SPATIAL索引, 不能转换为另一种引擎. InnoDB数据表可以被转换为另一种引擎, 如果InnoDB数据表定义了外键的约束条件, 那些约束条件在转换后将不复存在. 因为只有InnoDB才支持外键.  



重命名数据表  
```  
alter table oldName to newName;
rename table oldName to newName;
rename table o1 to n1, o2 to n2, o3 to n3;

```  
alter table 语句每次只能重命名一个数据表, 而rename table语句可以一次重命名多个数据表.  
如果在重命名一个数据表时在它的名字前面加了数据库名前缀, 就可以把它从一个数据库移动到另一个数据库.  
```  
alter table sampdb.t to test.t;
rename table sampdb.t to test.t;

```  
如果重新命名的某个MyISAM数据表是某个MERGE数据表的成员, 必须重新定义那个MERGE数据表, 让它使用那个MyISAM数据表的新名字.  




用show语句获取元数据:  
```  
show databases;
show create database databaseName;

show tables;
show tables from databaseName;
show create table tableName;

show columns from tableName;
show index from tableName;

show table status;
show table status from databaseName;

```  
有几种show语句可以带有一条like子句, 这种模式允许包含"%"和"\_"通配符. 如果需要在like模式里实际使用某个通配符, 必须在该字符的前面加上一个反斜线进行转移. 这种情况常见于需要匹配"\_"(下划线)字符时. 该字符经常出现在数据库, 数据表和数据列的名字里.  
show tables语句的输出里不包含temporary数据表.  
```  
show columns from student where 'Key' = 'PRI';
show table like 'tableName';
show * from tablName where false;

```  
下面两条语句的一条去检查某个给定数据表是否存在, 它们可以把temporary数据表也找出来:  
```  
select count(*)  from tableName;
select * from tableName where false;

```  
如果数据表存在, 这两条语句都将执行成功. 如果不存在, 两条语句都将失败.  


获取数据库元信息的另一种办法是访问information_schema数据库. 访问需要通过select语句来进行. select语句可以选取特定的数据列输出报告. where子句可以让你通过各种表达式挑选你真正需要的信息. 还可以使用联结或子查询, 可以使用create table ... select 或 insert into ... select 语句把检索结果保存到另一个数据表做进一步处理.  
` mysql> show tables in information_schema; `  

对各个information_schema数据表的简要说明:  
> (1) SCHEMATA, TABLES, VIEWS, ROUTINES, TRIGGERS, EVENTS, PARTITIONS, COLUMNS  关于数据库, 数据表, 视图, 存储例程(stored routine), 触发器, 数据库里的事件, 数据表分区 和数据列的信息.  
> (2) FILES 关于NDB硬盘数据文件的信息.  
> (3) TABLE_CONSTRAINS, KEY_COLUMN_USAGE  关于数据表和数据列上的约束条件的信息, 如唯一化索引, 外键等.  
> (4) STATISTICS  关于数据表索引特性的信息.  
> (5) REFERENTIAL_CONSTRAINS  关于外键的信息.  
> (6) CHARACTER_SETS, COLLATIONS, COLLATION_CHARACTER_SET_APPLICABILITY  关于所支持的字符集, 每种字符集的排序方式, 每种排序方式与它的字符集的映射关系的信息.  
> (7) ENGINES, PLUGINS  关于存储引擎和服务器插件的信息.  
> (8) USER_PRIVILEGES, SCHEMA_PRIVILEGES, TABLE_PRIVILEGES, COLUMN_PRIVILEGES  全局(注:应该是用户), 数据库, 数据表和数据列的权限信息. 这些信息分别来自mysql数据库里的user, db, tables_priv, column_priv数据表.   
> (9) GLOBAL_VARIABLES, SESSION_VARIABLES, GLOBAL_STATUS, SESSION_STATUS 全局和会话级系统变量, 状态变量的值.  
> (10) PROCESSLIST  关于服务器内执行的线程信息.  

查看某给定information_schema数据表都包含哪些数据列, 可以使用show columns 或 describe语句查询.  



mysqlshow命令行提供的信息与相应的show语句相同或近似:  
```  
% mysqlshow
% mysqlshow databaseName
% mysqlshow databaseName tableName
% mysqlshow --keys databaseName tableName
% mysqlshow --status databaseName
% mysqlshow --no-data databaseName 

```  
使用mysqlshow和mysqldump, 需要连接参数选项. 如 --host, --user 或 --password.  






在select语句中的from子句里列出了多个数据表, 并且用 inner join将它们的名字隔开, MySQL将执行内联结(inner join)操作:  
```  
select * from t1 inner join t2;
select t1.*, t2.* from t1 inner join t2;

```  

根据某个数据表里的每一个数据行与另一个数据表里的每一个数据行得到全部可能的组合的联结操作叫做生成笛卡尔积(cartesian product).  

让联结操作只对数据表里的特定数据列里的值进行匹配.  
` mysql> select t1.*, t2.* from t1 inner join t2 where t1.i1 = t2.i2; `  

cross join和join联结类型类似于 inner join, 比如说, 下面的语句是等价:  
```  
select t1.*, t2.* from t1 inner join t2 where t1.i1 = t2.i2;
select t1.*, t2.* from t1 cross join t2 where t1.i1 = t2.i2;
select t1.*, t2.* from t1 join t2 where t1.i1 = t2.i2;

```  

","(逗号)关联操作符的效果与inner join相似:  
` select t1.*, t2.* from t1, t2 where t1.i1 -t2.i2; `  
逗号操作符的优先级和其它联结类型不一样, 有时还会导致语法错误. 而其它联结类型没有这些问题. 作者认为应该尽量避免使用逗号操作符.  

inner join, cross join和join(注意, 不包括逗号操作符)还支持另外几种用来表明如果对数据表里的数据列进行匹配的语法变体.  
> (1) 用一条on子句替代where子句  
` select t1.*, t2.* from t1 inner join t2 on t1.i1 = t2.i2; `  
不管被联结的数据库是否同名, on子句都可以使用.  
> (2) 另一种语法是使用一个using()子句, 它在概念上类似于on语句. 但要求被联结的数据列必须是同名的.  
` select table1.*, table2.* from table1 inner join table2 using (col1); `  


为消除在引用数据列时可能产生歧义问题, 给该数据表的一个实例分配了一个别名.  
```  
 select mytable .col1, m.col2 from mytable inner join mytable as m 
   where mytable .col1 > m.col1   
```  


内联结显示在两个数据表里都能找到匹配的数据行. 外联结除了显示同样的匹配结果, 还可以把其中一个数据表在另一个数据表没有匹配的数据行也显示出来. 外联结分左联结和右联结两种. 
大多数例子使用的是left join, 意思是把左数据表在右数据表里没有匹配的数据行也显示出来. right join与此刚好相反, 它将把右侧数据表在左侧数据表里没有匹配的数据也显示出来. 数据表的角色调换了一下.  
left join对某个数据进行匹配时, 那两个数据行的内容就会被选取为一个输出数据行; 找不到匹配时, 也会选取为一个输出数据行. 此时与它联结的是一个来自右数据表的"假"数据行, 这个"假"数据行的所有数据列都包含NULL值.  
` select t1.*, t2.* from t1 left join t2 on t1.i1 = t2.i2; `  

使用left join时需要注意这样一个问题: 如果右数据列没有全部定义成NOT NULL, 结果集里的数据行就可能不能反映真实情况. 如果右数据表里的某个数据列允许取值为NULL并被收录在结果集里, 用这个数据列里的NULL值来判断"在右数据表里没有匹配"就可能出问题.  
下面两条语句是等价的:  
```  
select t1.*, t2.* from t1 left join t2 on t1.i1 = t2.i2;
select t1.*, t2.* from t2 right join t1 on t1.i1 = t2.i2; 

```  

left join很有用, 尤其是在你只想找出右数据表里没有匹配的左数据表的行时:  
```  
select t1.*, t2.* from t1 left join t2 on t1.i1 = t2.i2 where t2.i2 is NULL; 
select t1.* from t1 left join t2 on t1.i1 = t2.i2 where t2.i2 is NULL; 

```  

left join也可以写成使用一个on子句或一个using()子句来给出匹配条件. 与inner join的情况相同, on子句不管被联结的数据列是否同名都可以使用, using()子句要求被联结的数据列必须有同样的名字.  
left join还有几种同义词和变体, left outer join是left join的一个同义词. MySQL还支持一种ODBC风格的left join表示法. 该表示法使用了花括号和OJ(outer join):  
` select t1.* from { OJ t1 left outer join t2 on t1.i1 = t2.i2 } where t2.i2 is null; `  
natural left join类似与left join, 它将按照left join规则对左, 右数据表里所有同名的数据列进行匹配(也就是说, 它不需要你给出任何on或using()子句).  




MySQL支持子查询(subquery), 也就是把一条select语句用括号括起来嵌入另一个select语句.  
```  
  select * from score 
     where event_id in ( select event_id from grade_event where categroy = 'T' );
```  

子查询返回以下不同类型的信息:  
> (1) 标量子查询将返回一个值.  
> (2) 数据列子查询将返回一个由一个或多个值构成的数据列.  
> (3) 数据行子查询将返回一个由一个或多个值构成的数据行.  
> (4) 数据表子查询将返回一个由一个或多个数据行构成的数据表, 数据行可以由一个或多个数据列构成.  


子查询的结果可以用以下不同的办法进行测试:  
> (1) 标量子查询的结果可以用诸如 "=" 或 "<" 之类的相对比较操作符进行求值.  
> (2) 可以用IN或NOT IN操作符测来测试某给定值是否包含在子查询的结果集里.  
> (3) 可以用ALL, ANY和SOME操作符把某给定值与子查询的结果集进行比较.  
> (4) 可以用EXISTS和NOT EXISTS 操作符来测试子查询的结果集是否为空.  

标量子查询是最严格的, 它只产生一个值, 也正是因为如此. 标量子查询的适用范围最大.  
子查询不允许引用正在被修改的数据表.  
有时候, 为了确保子查询返回且只返回一个值, 有必要用limit 1 对子查询的结果加以限制.  

查询最早的出生日期:  
```  
select * from president where birth = 
 ( select min(birth) from president );

```  

高于平均分数的分数:  
```  
select * from score where event_id = 5
  and score > ( select avg(score) from score where event_id = 5 );

```  


查询将返回多个数据行, 可以使用IN或NOT IN操作符来构造主查询的检索条件. IN和NOT IN操作符的用途是测试一个给定的比较值有没有出现在一个集合里.  
只要主查询里的数据行与子查询所返回的任何一个数据行匹配, IN操作符的结果就将是true. 如果主查询里的数据行与子查询所返回的所有数据行都不匹配, NOT IN操作符的比较结果将是true.  
```  
select * from student where student_id in ( select student_id from absence );
select * from student where student_id not in ( select student_id from absence );

```  


ALL和ANY操作符的常见用法是结合一个相对比较操作符对一个数据列子查询的结果进行测试. 它们测试比较值是否与子查询所返回的全部或部分值匹配. 如果比较值小于或等于子查询所有返回的每一个值, <= ALL将是true. 只要比较值小于或等于子查询所返回的任何一个值, <= ANY将是true. SOME是ANY的一个同义词.  
```  
select * from president where 
  birth <= ALL ( select birth from president );
  
select * from president where 
  birth <= ANY ( select birth from president );  

```  
IN和NOT IN操作符是 = ANY 和 <> ALL 的简写. IN操作符的含义是"等于子查询所返回的某个数据行", NOT IN操作符的含义是"不等于子查询所返回的任何数据行".  



EXISTS和NOT EXISTS操作符只测试某个子查询是否返回了数据行, 如果是, EXISTS将是true, NOT EXISTS将是false.  
```  
select exists ( select * from absence );
select not exists ( select * from absence );
  
```  

确保子查询成功时返回一个true值的话, 写成select 1 比写成select * 更保险.  

子查询可以与主查询相关, 也可以与主查询无关.  
> (1) 与主查询无关的子查询不引用主查询里的任何值.  
` select i from t2 where j in ( select i from t1 ); `  
> (2) 与主查询相关的子查询需要引用主查询的值, 所以必须依赖于主查询.  
` select j from t2 where ( select i from t1 where i = j );  `  

与主查询相关的子查询通常在EXISTS和NOT EXISTS子查询里.  
EXISTS子查询用来确定数据表之间的匹配, 也就是在两个数据表里都有的值.  
NOT EXISTS操作符用来寻找不匹配的值, 在一个数据表里有但在其它数据表里没有的值.  

子查询可以在from子句里生成一些值.  
` select * from ( select 1, 2) as t1 inner join ( select 3, 4) as t2; `  


有时候, 联结查询要比子查询的执行效率更高. 所以把子查询改写为联结查询是个不坏的注意.  
子查询和关联查询可能返回不同的结果, 防止重复, 在编写联结查询命令时就要用select distinct来代替select.  
子查询语句的另一种常见用法是检索在一个数据表里有, 另一个数据表里没有的值. 与"哪些值没有出现"有关的问题通常可以用left join来解决.  
与left join相比, 子查询具有比较直观和容易理解的优点. 绝大多数人都可以毫无困难地理解"没有包含在...里面"的含义. 它不是数据库编程带来的新概念. "左联结"这个概念就不同了, 就算是数据库方面的专业人士也不见得都能解释透彻.  




如果想把多个查询的结果合并在一起创建一个结果集, 可以使用union语句来做这件事.  
` select i from t1 union select i from t2 union select i from t3; `  

union结果集里的数据列名字来自第一个select语句里的数据列的名字. union中的第二个和再后面的select语句必须选取个数相同的数据列. 但各有关数据项不必有同样的名字或数据类型. (一般来讲, 同一条union语句里的各有关数据列会是相同的数据类型. 但这并不是一项要求, 如果它们不一样, MySQL会进行必要的类型转换.) 数据列根据位置而不是根据名字进行匹配的. 这也正是下面两条语句返回不同结果的原因.  
```  
select i, c from t1 union select i ,d from t3;
select i, c from t1 union select d, i from t3;

```  

在默认情况下, union将从结果集里剔除重复的数据行.  
union distinct是union的同义词, 它们都是只保留不重复的数据行. 想保留重复的数据行, 需要把每个union都改为union all.  
` select * from t1 union all select * from t2 union all select * from t3; `  

如果把union(或union distinct)和union all混杂在一起使用, 每个union(或union distinct)操作将优先于它左边的任何union all操作.  

如果将union结果作为一个整体进行排序, 需要使用括号把每一个select语句括起来并在最后一个select子句的后面加上一个order by子句. 因为union的结果数据列的名字来自第一个select语句, 在order by 子句里必须引用那些名字而不是引用来自最后一个select语句的数据列名字.  
` ( select i ,c from t1 ) union ( select i, d from t3 ) order by c; `  
如果某个排序数据列有别名, 位于union语句末尾的order by子句必须引用那个别名.  

限制union语句所有输出的数据行的个数, 可以在语句末尾加上一个limit子句.  
```  
( select * from t1 ) union ( select * from t2 ) union ( select * from t3 ) limit 2; 

```  

union语句中, order by和limit还可以用在被括号括起来的各条"子句"里, 此时, 它们将只作用于那条select语句:  
```  
( select * from t1 order by i limit 2 ) 
  union ( select * from t2 order by i limit 1 ) 
  union ( select * from t3 order by d limit 2 );

```  

MERGE数据表上的select语句相当于union all(不剔除重复的数据行), select distinct相当于union或union distinct(剔除重复的数据行).  





视图是一种虚拟的数据表, 它们的行为和数据表一样, 但是并不真正包含数据. 它们是用底层(真正的)数据表或其它视图定义出来的"假"数据表. 用来提供查看数据表数据的另一种方法, 这通常可以简化应用程序.  
```  
create view viewName as
  select c1, c2, c3, c4 from tableName;
  
select * from viewName;  

```  

查询视图还可以使用order by, limit子句. 其效果与查询一个真正的数据表的情况一样.  
想明确地改用另外的数据列名字, 需要在定义视图时在视图名字的后面用括号列出那些新的名字:  
```  
create view viewName (colName1, colName2) as 
  select c1, c2 from tableName;
  
select colName1, colName2 from viewName;  
  
```  

同一个视图可以涉及多个数据表, 这使得联结查询的编写和运行变得更容易.  

要想一个视图是可更新的, 它必须直接映射到另一个数据表上. 它选取的数据列只能是数据表里数据列的简单引用(不能是表达式). 视图里的单行操作必须对应其底层数据表的一个单行操作.  



这条语句将从数据表t1里把其id值可以在另一个数据表t2里找到的数据行全部删除掉:  
` delete t1 from t1 inner join t2 on t1.id = t2.id; `  

delete语句有一种写法可以让我们一次删除多个数据表里的数据行.  
` delete t1, t2 from t1 left join t2 on t1.id = t2.id; `  

删除不匹配的数据行:  
` delete t1 from t1 left join t2 on t1.id = t2.id where t2.id is null; `  

使用一个using子句来联结各有关数据表以确定哪些数据行需要被删除:  
```  
delete from t1 using t1 inner join t2 on t1.id = t2.id;
delete from t1, t2 using t1 inner join t2 on t1.id = t2.id;
delete from t1 using t1 left join t2 on t1.id = t2.id where t2.id is null;

```  

编写涉及多个数据表的update语句的基本步骤与编写涉及多个数据表的delete语句很相似.  
对InnoDB数据表进行多数据表删除和刷新操作, 你不必非得使用刚才介绍的语法, 更好的办法是在数据表之间建立一个外键关系并给它加上on delete cascade或on update cascade约束条件.  



事务(transaction) 是作为一个不可分割的逻辑单元而被执行的一组SQL语句. 如有必要, 它们的执行效果可以被撤销. 事务处理是通过提交(commit)和回滚(rollback)功能实现的. 如果某个事物里的所有语句都执行成功了, 提交该事务将把那些语句的执行效果永久地记录到数据库里. 如果在事务过程中发生错误, 回滚该事务将把错误之前已经执行的语句全部取消, 数据库将恢复到开始这次事务之前的状态.  

事务机制的特性通常被概括为"ACID"原则. ACID是 Atomic(原子性), Consistent(稳定性), Isolated(孤立性) 和 Durable(可靠性)的首字符缩写. 它们分别代表事务机制应该具有的一个属性.  
> (1) 原子性.  构成一个事务的所有语句应该是一个独立的逻辑单元, 要么全部执行成功, 要么一个都不成功. 你不能只执行它们当中的一部分.    
> (2) 稳定性.  数据库在事务开始执行之前和事务执行完毕之后都必须是稳定的. 换句话说, 事务不应该把你的数据库弄得一团糟.  
> (3) 隔离性.  事务不应该相互影响.  
> (4) 可靠性.  如果事务执行成功, 它的影响将被永久地记录到数据库里.  

MySQL提供了几种具备事务安全性的存储引擎(如InnoDB和Falcon)和一些不具备事务安全性的存储引擎(如MyISAM和MEMORY).  
在默认情况下, MySQL从自动提交(autocommit)模式运行, 这种模式会在每条语句执行完毕后把它作出的修改立刻提交给数据库并使之永久化.  
执行事务的常用办法是发出一条start transaction(或begin)语句挂起自动提交模式, 然后执行构成本次事物的各条语句, 最后一条commit语句结束事务并把它们作出的修改永久性地计入数据库. 万一在事务过程中发生错误, 用一条rollback语句撤销事务并把数据库恢复到事务开始之前的状态.  

演示:
```  
mysql> create table t ( name char(20), unique (name) ) engine = InnoDB;

mysql> start transaction;
mysql> insert into t set name = 't1';
mysql> insert into t set name = 't2';
mysql> commit;
mysql> select * from from t;

```  

```  
mysql> start transaction;
mysql> insert into t set name = 't3';
mysql> insert into t set name = 't1';
ERROR 1062(23000): Duplicate entry

mysql> rollback;
mysql> select * from t;

```  

执行事务的另一个办法是利用set语句直接改变自动提交模式的状态.  
```  
set autocommit = 0;
set sutocommit = 1;
```  
把autocommit变量设置为0将禁用自动提交模式, 其效率是随后的任何语句都成为当前事务的一部分, 直到你发出一条commit或rollback语句来提交或撤销它为止.  

set autocommit, start transaction, begin, commit和rollback语句会明确地对事物产生影响, 除它们以外, 还有一些语句会对事物产生隐式影响, 因为它们不能成为事务的一部分. 用来创建, 改变或删除数据库或其中的对象的DDL(Data Definition Language, 数据库定义语言)语句以及锁定有关的语句都不可能成为事务的一部分.  
在事务过程中发出了下面这些语句之一, 服务器在执行该语句之前提交事务:  
```  
alter table
create index
drop database
drop index
drop table
lock tables
rename table
set autocommit = 1 (if not already set to 1)
truncate table
unlock tables (if tables currently are locked)

```  
正在使用的MySQL版本有哪些语句会隐式地提交当前事务, 请查阅`<<MySQL参考手册>>`.  

如果客户程序在与服务器连接意外断开后再自动重建连接, 新建的连接将被重置为激活自动提交模式的默认状态.  
MySQL使你能够对一个事务进行部分回滚. 这需要你在事务过程中使用savepoint语句设置一些称为保存点(savepoint)的标记, 在后续的事务里, 如果你想回滚到某个特定的保存点, 在rollback语句里给出该保存点的名字就可以了. 演示:  
```  
mysql> create table t ( i int ) engine = InnoDB;
mysql> start transaction;
mysql> insert into t values (1);
mysql> savepoint my_savepoint;
mysql> insert into t values (2);
mysql> rollback to savepoint my_savepoint;
mysql> insert into t values (3);
mysql> commit;

mysql> select * from t;

```  

MyISAM之类的存储引擎使用了数据表级别的锁定机制来保证不同的客户端不能同时修改同一个数据表, 但这种做法在更新量比较大的系统上会导致并发性能的下降. InnoDB存储引擎采用了另一种策略, 它使用了数据行级的锁定机制为客户对数据表的访问提供了更细致的控制. 先锁定该数据行的那个客户将可以先修改它. 这比数据表级的锁定机制提供了更好的并发性能.  

InnoDB存储引擎实现的事务隔离级别机制能够让客户控制他们想看到其他事物作的哪些修改. 提供了多种不同的隔离级别以允许或预防在多个事务同时运行时可能发生的各种各样的问题.
> (1) 脏读 (dirty read).  指某个事务所作出的修改在它尚未被提交时就可以被其它事务看到. 其它事务会认为数据行已经修改了, 但对数据行作出修改的那个事务还有可能回滚, 这将导致数据库里的数据发生混乱.  
> (2) 不可重复度 (nonrepeatable read).  指同一个事物使用同一条select语句每次读取到的结果不一样. 比如说, 如果有一个事务执行了两次同一个select语句, 但另一个事物在这条select语句的两次执行之间修改了一些数据行, 就会发生这种问题.  
> (3) 幻影数据行 (phantom row).  指某个事物突然看到了一个它以前没有见过的数据行. 比如说, 如果某个事物刚执行完一条select语句就有另外一个事务插入了一个新数据行, 前一个事务再执行同一条select语句时就可能看到一个新的数据行, 那就是一个幻影数据行.  

为了解决这些问题, InnoDB存储引擎提供了4种隔离级别, 这些隔离级别用来确定允许某个事务看到与之同时执行的其它事务所作出的哪些修改.  
> (1) READ UNCOMMITTED.  允许某个事物看到其它尚未提交的数据行改动.  
> (2) READ COMMITTED.  只允许某个事物看到其它事务已经提交的数据行改动.  
> (3) REPEATABLE READ.  如果某个事物两次执行同一个select语句, 其结果是可重复的. 换句话说, 即使有其它事务在同时插入或修改数据行, 这个事务所看到的结果也是一样的.  
> (4) SERIALIZABLE.  这个隔离级别与REPEATABLE READ很相似. 但对事物的隔离更彻底. 某个事物正在查看的数据行不允许其它事务修改, 直到该事务完成为止. 换句话说, 如果某个事务正在读取某些数据行, 在它完成之前, 其它事务将无法对那些数据行修改.  

InnoDB 4种隔离级别以及它们是否允许脏读, 不可重复度或幻影数据行等问题, 只适用于InnoDB存储引擎 -- REPEATABLE READ隔离级别不能容忍幻影数据行. 有些数据库系统的REPEATABLE READ隔离级别允许出现幻影数据行.  

<table>
<tr>
 <th>隔离级别</th>  <th>脏读</th>  <th>不可重复度</th>  <th>幻影数据行</th>
</tr>

<tr>
 <td>READ UNCOMMITTED</td>  <td>是</td>  <td>是</td>  <td>是</td>
</tr>

<tr>
 <td>READ COMMITTED</td>  <td>否</td>  <td>是</td>  <td>是</td>
</tr>

<tr>
 <td>REPEATABLE READ</td>  <td>否</td>  <td>否</td>  <td>否</td>
</tr>

<tr>
 <td>SERIALIZABLE</td>  <td>否</td>  <td>否</td>  <td>否</td>
</tr>

</table>


InnoDB存储引擎默认使用的隔离级别是REPEATABLE READ. 可以通过在启动服务器时使用 --transaction-isolation 选项或在服务器运行时使用 SET TRANSACTION语句来改变, 该语句有3种形式:  
```  
SET GLOBAL TRANSACTION ISOLATION LEVEL levelName;
SET SESSION TRANSACTION ISOLATION LEVEL levelName;
SET TRANSACTION ISOLATION LEVEL levelName;

```  
super权限的客户可以直接使用SET TRANSACTION语句改变全局隔离级别的设置. 该设置将作用于此后连接到服务器的任何客户. 此外, 任何客户都可以修改它自己的事务隔离级别. 用SET SESSION TRANSACTION语句做出的修改将作用于在与服务器的本次会话后续的所有事务. 用SET TRANSACTION语句做出的修改只作用于下一条事务, 客户在修改它自己的隔离级别时不需要任何特殊的权限.  

Falcon和InnoDB存储引擎在这方面的主要区别是: Falcon不支持READ UNCOMMITTED隔离级别, 它目前也不支持SERIALIZABLE隔离级别 (但Falcon开发团队正在为此而努力着).  

把多条语句用LOCK TABLES和UNLOCK TABLES语句括起来就可以把他们当作一个单元来执行: 锁定需要使用的所有数据表, 发出你的语句, 解除锁定. 这可以防止其他人在你锁定有关数据表期间修改它们.  

"读操作"锁允许其它客户在你使用被锁定数据表时读取它, 但不允许对之进行写操作.  

锁定机制需要你锁定和释放数据表.  

事务机制可以把一组语句当作一个不可分割的单元来执行, 并防止客户之间彼此干扰. 从而有效地管理好并发问题. 它提供的回滚功能还可以在发生意外时避免尚未全部完成的操作损坏数据库, 还可以自行判断并获得必要的锁操作.  

在某次事务中混和使用事务数据表和非事务数据表是允许的, 但最终结果不一定符合你的期望. 对非事务表进行操作的语句总是立即生效. 即使自动提交模式处于禁用状态也是如此. 非事务表永远待在自动提交模式下. 每条语句都会在执行完毕后立即提交. 如果你在一个事务里修改了一个非事务数据表, 那么这个修改无法撤消.  




利用外建(foreign key)关系可以在某个数据表里声明与另一个数据表里的某个索引相关联的索引.  

外键不仅在数据行的插入操作中很有用, 在删除和更新操作中也很有用. 数据表里有关的所有数据行也将自动被删除, 被称为级联删除(cascaded delete), 因为删除操作的效果就像瀑布(cascade)那样从一个数据表"流淌"到另外一个数据表. 级联更新也是可能的.  

InnoDB存储引擎提供了外键支持, 术语:  
> (1) 父表, 包括原始键值的数据表.  
> (2) 子表, 引用父表的键值的相关数据表.  

父表中的键值用来关联两个数据表, 具体来说, 子表中的某个索引引用父表中的某个索引. 子表的索引值必须匹配父表中的索引值或是被设置为NULL, 以表明在父表里不存在与之对应的数据行. 子表里的索引就是所谓的"外键". 因为它们存在父表的外部, 但包含指向父表的值. 外键关系可以被设置成不允许使用NULL值, 此时所有的外键值都必须匹配父表里的某个值.  

InnoDB存储引擎通过这些规则来保证在外键关系里不会有不匹配的东西, 这被称为引用完整性(referential integrity).  

在子表里定义外键的语法:  
```  
[ constraint constraintName]
foreign key [fkName] (indexColumns)
  references tableName (indexColumns)
    [on delete actionName]
    [on update actionName]
    [match full | match partial | match simple]	
	
	
```  

InnoDB存储引擎目前还没有实现所有的子句: 它目前还不支持match子句. 即使给出一条match子句, 它也会被忽略. 有几种action的值目前只能被识别出来, 但不会有任何效果.(除InnoDB以外的存储引擎在遇到foreign key定义时不会报任何错误, 但会把它整个忽略掉)  

InnoDB存储引擎能够识别和支持以下外键定义语法成分.  
> (1) constraint子句. 如果给出, 这个子句用来给外键约束关系起一个名字. 如果省略, InnoDB存储引擎将创建一个名字.  
> (2) foreign key子句. 列出子表里的被索引数据列, 它们必须匹配父表里的索引值. fkName是外键的ID. 如果给出了一个fkName, 在InnoDB存储引擎能够为外键自动创建一个索引的情况下, 它将成为那个索引的名字. 否则, 它将被忽略.  
> (3) references子句. 列出父表和父表中的索引数据列的名字. 子表里的外键值将引用这个子句所列出的父表数据列. 在references子句的indexColumns部分列出的数据列的个数必须与在foreign key子句的indexColumns部分列出的数据列的个数相同.  
> (4) on delete子句. 用来设定在父表里的数据行被删除时子表应该做什么事. 如果没有on delete子句, 默认行为是拒绝从父表里删除仍有子表数据行在引用它们的数据行. 明确指定一种action, 请使用以下子句:  
>> (4.1) on delete no action 和 no delete restrict子句. 它们含义与省略on delete子句一样. (有些数据库提供了延迟检查的功能, no action是一种延迟检查. 在MySQL里, 外键约束都是立刻被检查的. 所以no action和restrict的含义完全一样).  
>> (4.2) on delete cascade子句. 在删除父表数据行时, 子表里与之相关联的数据行也将被删除. 只需要删除父表的数据, 就可以完成一个涉及多个数据表的删除操作了.  
>> (4.3) on delete set null子句. 在删除父表数据行时, 子表里与之相关联的索引列将被设置为null. 使用这个选项, 就必须把外键定义里列出的所有被编制索引的子表数据列定义为允许null值. (这个动作的隐含限制是不能把外键定义为primary key, 因为主键不允许为null)  
>> (4.4) on delete set default子句. 这个子句可以被识别出来, 但目前尚未实现. InnoDB存储引擎遇到这个子句时将报告一个错误.  

> (5) on update子句. 用来设定父表里的数据行更新时子表应该发生什么事. 如果没有on update子句, 默认行为是拒绝插入或更新其外键值在父表索引里没有任何匹配的子表数据行. 并阻止仍有子表数据行仍在引用着它们的父表索引值被更新. 可供选用的action值及效果与on delete子句相同.  

如果你想建立一个外键关系, 请遵守以下指示.  
> (1) 子表必须有这样的索引, 在定义该索引时, 必须首先列出外键数据列. 父表必须有这样一个索引, 在定义索引时必须首先列出references子句里的数据列. (换句话说, 外键里的数据列在外键所涉及的两个数据表里都必须有索引.) 由InnoDB存储引擎自动创建的这种索引将是一个非唯一性的索引, 并且只包含外键数据列.
> (2) 父表和子表索引里的对应数据列必须是兼容的数据类型. 不能让一个INT数据列去匹配一个CHAR数据列. 对应的字符数据列必须是同样的长度. 对应的整数数据列必须是同样的尺寸. 并且必须要么都带符号, 要么都被定义成unsigned.  
> (3) 不能对外键关系里的字符串数据列的前缀编制索引.  

```  
create table parent (
  par_id int not null,
  primary key (par_id)
) engine = InnoDB;

create table child (
  par_id int not null,
  child_id int not null,
  primary key (par_id, child_id),
  foreign key (par_id) references parent (par_id)
    on delete cascade
	on update cascade
) engine = InnoDB;	
    	  
```  
定义外键使用了on delete cascade子句, 指定当parent数据表里的某个数据行被删除时, MySQL将自动地从child数据表里把有匹配par_id的值的数据行也删除. on update cascade子句表名, 如果parent数据表里的某个数据行的par_id值改变了, MySQL将自动地把child数据表里的所有匹配的par_id值也改成新值.  

查看某个InnoDB数据表都有哪些外键关系, 可以使用show create tables或show table status语句.  
创建一个带有外键关系的数据表时遇到问题, 可以使用show engine innodb status语句查看完整的出错信息.  







MySQL具备全文搜索的能力, 全文搜索引擎可以在不使用模板匹配操作的情况下查找单词或短语. 全文搜索有三种模式:  
> (1) 自然语言模式.  把搜索字符串解释为一系列单词并查找包含这些单词的数据行.  
> (2) 布尔模式.  把搜索字符串解释为一系列单词, 但允许使用一些操作符来"修饰"这些单词以表明特定的要求, 如某给定单词必须出现(或不出现)在匹配数据行里. 某个数据行必须包含一个精确的短语, 等等.  
> (3) 查询扩展模式.  这种搜索分两个阶段进行. 第一阶段是自然语言搜索. 第二阶段是使用原来的搜索字符串加上在第一次搜索中找到的相关度最高的匹配数据行再进行一次搜索. 这扩大了搜索范围, 它可以把与原来的搜索字符串相关, 但用原来的搜索字符串匹配不到的数据行也找出来.  


要想对某个数据表进行全文搜索, 必须事先为它创建一个fulltext索引. 这种索引具有以下特点:  
> (1) 全文搜索的基础是fulltext索引, 这种索引只能在MyISAM数据表创建. fulltext索引只能由char, varchar和text这几种类型的数据构成.  
> (2) 全文索引将忽略"常见的"单词. 而"常见"在这里的含义是"至少在一半的数据行里出现过". 至少要在测试表里插入3个数据行. 如果那个数据表只有一个或两个数据行, 它里面的每个单词将至少有50%的出现几率, 所以对它进行全文搜索将不会有任何结果.  
> (3) 全文搜索还将忽略一些常见单词, 如"the", "after"和"other"等. 这些单词被称为"休止单词", MySQL在进行全文搜索时总是会忽略他们.  
> (4) 太短的单词也将被忽略. 在默认的情况下, "太短"指少于4个字符串, 可以通过重新配置服务器的办法把这个最小长度设置为其它值.  
> (5) 全文搜索对"单词"的定义是, 由字母, 数字, 撇号(如 "it's" 中的 "'")和下划线字符构成的字符序列. 这意味着字符串"full-blood"将被解释为包含"full"和"blood"两个单词. 全文搜索匹配整个单词, 而不是单词的一部分.  
> (6) fulltext索引可以为一个或多个数据列而创建. 如果它涉及多个数据列, 基于该索引的搜索将在所有数据列上同时进行. 反过来说, 在进行全文搜索时, 你给出的数据列清单必须和某个fulltext索引所匹配的那些数据列精确匹配. 比如说, 需要分别搜索col1, col2以及 "col1, col2", 需要创建3个索引: col1 和 col2 各有一个, "col1和col2"有一个.  



fulltext索引  
```  
create table tableName (
  attribution varchar(40),
  phrase text
) engine = MyISAM;


load data local infile 'demo.txt' into table tableName;

alter table tableName  
   add fulltext (attribution),
   add fulltext (phrase),
   add fulltext (phrase, attribution);   

```  


对它进行自然语言模式的全文搜索: 用match操作符列出被搜索的数据列, 用against()给出搜索字符串.  
```  
select * from tableName where match(attribution) against('roosevelt');
select * from tableName where match(phrase) against('time');
select * from tableName where match(phrase, attribution) against('bell'); 

```  

看某个搜索可以匹配多少行, 使用count(\*):  
` select count(*) from tableName where match(phrase) against('time'); `  

在where子句里使用match表达式时, 自然语言模式的全文搜索的输出数据行按照相关程度递减的顺序排序. 相关度以非负浮点数来表示, 0代表"毫不相关".  
` select phrase, match(phrase) against('time') as relevance from tableName; `  

查询多个单词:  
` select * from tableName where match(phrase) against('hard soft'); `  





全文搜索的布尔模式可以让我们控制多单词搜索操作中的许多细节. 要想进行这种模式的搜索, 需要在against()函数里在搜索字符串的后面加上 in boolean mode短语. 布尔模式特点:  
> (1) "50规则"不再起作用, 即使是在超过一半的数据行里出现过的单词也可以被这种搜索匹配出来.  
> (2) 查询结果不再按照相关度排序.  
> (3) 在搜索一个短语时, 可以要求所有单词必须按照某种特定的顺序出现. 搜索一个短语, 需要把构成该短语的所有单词用双引号括起来; 如果数据行包含的单词按照给定的顺序排列, 才被认为是一个匹配.  
` select * from tableName  where match(attribution, phrase) against('"bell book and candle"' in boolean mode); `  
> (4) 布尔模式的全文搜索还可以在没被包括在fulltext索引里的数据列上进行, 但这要比搜索有fulltext索引的数据列慢很多.  


布尔模式搜索时, 还可以给搜索字符串里的单词加上一些修饰符. 在单词的前面加上一个加号表示该单词必须出现在匹配数据行里, 而加上一个减号表示该单词不得出现在匹配数据行里. 
字符串'+bell -candle'只匹配包含单词"bell", 不包含单词"candle"的数据行.  
` select * from tableName where match(attribution, phrase) against('+bell -candle' in boolean mode); `  

后缀的星号"\*"将被解释为一个通配符, 带星号后缀的搜索单词将匹配以它开头的所有单词. 比如'soft\*'将匹配"soft", "softly", "softness"等.  
` select * from tableName where match(phrase) against'soft*' in boolean mode); `  
在附录C里, 你可以在介绍match操作符的部分查到全部的布尔模式修饰符.  

和自然语言模式的全文搜索情况相似, 布尔模式的全文搜索也将忽略所有的休止单词. 就算把它们标为"必须出现"也是如此.  




全文搜索的扩展模式将进行两个阶段的搜索, 第一遍搜索和普通的自然语言搜索一样, 在这次搜索里找到的相关度最高的数据行里的单词将被用在第二个阶段. 这些数据行里的单词加上原来那些搜索单词将被用来进行第二遍搜索. 因为搜索单词的集合变大了, 所以在最终结果里往往会多出一些在第一阶段没找到, 但与第一阶段的检索结果有一定关系的数据行.  


进行这种搜索, 需要在搜索字符串后面加上with query expansion短语.  
` select * from tableName where match(attribution, phrase) against('bell book' with query expansion); `



为fulltext索引设置单词最小和最大长度的参数是ft_min_word_len和ft_max_word_len. 长度超出这两个参数所定义的范围的单词在创建fulltext索引时将被忽略. 默认的最小值和最大值分别是4个和84个字符串.  



把最小单词长度从4改成3, 请按以下步骤进行.  
> (1) 把 ft_min_word_len 变量设置为3, 重启服务器. 如果想让这个设置在服务器每次重启都能生效, 最好的办法是把这个设置放到某个选项文件里, 如 /etc/my.cnf文件:  

```  
[mysqld]
ft_min_word_len=3

```  
> (2) 对于那些已经有fulltext索引的现有数据表, 必须重新建立那些索引. 可以先删除, 再重新创建. 但更简便且同样有效的办法是进行一次快速修复操作:  
` repair table tableName quick; `  
> (3) 在改变参数后创建的新fulltext索引将自动使用新值.  

如果某个数据表有fulltext索引, 在使用myisamchk工具程序为该数据表重建索引的时候就必须注意一些与fulltext索引有关的事项.  





 

  
  



  


 





 
 





































