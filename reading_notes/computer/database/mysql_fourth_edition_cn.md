

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
alter table tableName add primary key (indexColumn);
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
   primary key (indexColumn),
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











 








 






 

  






 
 

 







  
  
  



  





