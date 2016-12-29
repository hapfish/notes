
# mongodb_the_definitive_guide_second_edition.md  

[美] Kristina Chodorow 著  
邓强  王明辉 译  

与传统的关系型数据库不同, MongoDB是一种面向文档的数据库.  

# 目录 

- 第一部分 MongoDB介绍  1 - 4章  
- 第二部分 设计应用  5 - 8 章  
- 第三部分 复制  9 - 12 章  
- 第四部分 分片 13- 16 章  
- 第五部分 应用管理  17 - 19 章  
- 第六部分 服务器管理  20 - 23 章   


# 序  

MongoDB具有支撑今天主流Web应用的关键功能: 索引, 复制, 分片, 丰富的查询语法, 特别灵活的数据模型. 与此同时还不牺牲效率.  


# 前言  

` <<MongoDB权威指南(第二版)>> ` Kristina Chodorow著(O'Reilly, 2013). 版权所有 978-1-449-34468-9.  

Safari在线图书(www.safaribooksonline.com) 是应需而变的数字图书馆, 它同时以图书和视频的形式出版世界顶级技术和商务作家的专业作品.  

中国: 北京市西城区西直门南大街2号成铭大厦C座807室(100035)  

# 第一章 MongoDB简介  

MongoDB是一款强大, 灵活, 且易于扩展的通用型数据库. 它能扩展出非常多的功能, 如二级索引(secondary index), 范围查询(range query), 排序, 聚合(aggregation), 以及地理空间索引(geospatial index).  

MongoDB是一个面向文档(document-oriented)的数据库, 而不是关系型数据库. 不采用关系型数据库主要是为了获得更好的扩展性.  

面向文档的数据库不再有"行"(row)的概念, 取而代之的是更为灵活的"文档"(document)模型. 不再有预定义模式(predefined scheme): 文档的键(key)和值(value)不再是固定的类型和大小.  

纵向扩展(scale up)和横向扩展(scale out)之间的选择: 纵向扩展就是使用计算能力更强的计算机, 而横向扩展就是通过分区将数据分散到更多机器上.  MongoDB的设计采用横向扩展. MongoDB能够自动处理跨集群的数据和负载, 自动重新分配文档, 以及将用户请求路由到正确的机器上.  


MongoDB作为一款通用型数据库, 除了能够创建, 读取, 更新和删除数据之外, 还提供一系列不断扩展的独特功能.  
- 索引(indexing) 支持通用二级索引, 允许多种快速查询, 且提供唯一索引, 复合索引, 地理空间索引, 以及全文索引.  
- 聚合(aggregation) 支持"聚合管道"(aggregation pipeline). 用户能够通过简单的片段创建复杂的聚合, 并通过数据库自动化.  
- 特殊的集合类型 支持在时间有限的集合, 适用于那些将在某个时刻过期的数据, 如会话(session).  
- 文件存储(file storage) 支持一种非常易用的协议, 用于存储大文件和文件元数据.  

MongoDB并不具有一些在关系型数据库中很普遍的功能, 如连接(join)和复杂和多行事务(multirow transaction). 省略这些功能是出于架构上的考虑(为了得到更好的扩展性), 因为分布式系统中这两个功能难以高效地实现.  

MongoDB能对文档进行动态填充(dynamic padding).也能预分配数据文件以利用额外的空间来换取稳定的性能.  MongoDB把尽可能多的内存用作缓存(cache), 试图为每次查询自动选择正确的索引.    


# 第二章  MongoDB基础知识  


基本概念  
- 文档是MongoDB中数据的基本单元, 非常类似于关系型数据库管理系统中的行, 但更具表现力.  
- 类似地, 集合(collection)可以看作是一个拥有动态模式的(dynamic schema)的表.  
- MongoDB的一个实例可以拥有多个互相独立的数据库(database), 每一个数据库都拥有自己的集合.  
- 每一个文档都有一个特殊的键"_id", 这个键在文档所属的集合中是唯一的.  
- MongoDB自带了一个简单但功能强大的JavaScript Shell, 可用于管理MongoDB的实例或数据操作.  


映射(map), 散列(hash), 字典(dictionary)  

MongoDB的文档不能有重复的键.  

集合就是一组文档. 如果将MongoDB中的一个文档比喻为关系数据库中的一行, 那么一个集合就相当于一张表.  

集合是动态模式的, 这意味着一个集合里面的文档可以是各式各样的.  

没有必要区分不同类型的模式, 使用多个集合的原因思考:  
- 如果把各种各样的文档不加区分地放在同一个集合里, 无论对开发者还是对管理员来说都是噩梦.  
- 在一个集合里查询特定类型的文档在速度上也很不划算, 分开查询多个集合要快得多. 
- 把同种类型的文档放在一个集合里, 数据会更加集中.  
- 创建索引时, 需要使用文档的附加结构(特别是创建唯一索引时).  


集合名可以是满足下列条件的任意UTF-8字符串.  
- 集合名不能是空字符串("")  
- 集合名不能包括\0字符(空字符), 这个字符表示集合名的结束  
- 集合名不能以"system."开头, 这是为系统集合保留的前缀. system.users保存着数据库的用户信息, system.namespace集合保存着所有数据库集合的信息    
- 用户创建的集合不能在集合名中包含保留字符"$"  


组织集合的一种惯例是使用"."分割不同命名空间的子集合.  

数据库名可以是满足以下条件的任意UTF-8字符串. 
- 不能是空字符串("").  
- 不得含有 / \ . " * < > : | ? $ (一个空格) \0 (空字符串)  . 基本上只能使用ASCII中的字母和数字.  
- 数据库名区分大小写, 即便是在不区分大小写的文件系统中也是如此. 简单起见, 数据库名应全部小写.  
- 数据库名最多为64字节.  

数据库最终会变成文件系统里的文件, 而数据库名就是相应的文件名, 这是数据库名有如此多限制的原因.  

有一些数据库名是保留的, 可以直接访问这些有特殊语义的数据库.  
- admin  从身份验证的角度来看, 这是 "root"数据库. 如果将一个用户添加到admin数据库, 这个用户自动获得所有数据库的权限. 再者, 一些特定的服务器端命令只能从admin数据库运行.  
- local  这个数据库永远都不可以复制, 且一台服务器上的所有本地集合都可以存储在这个数据库.  
- config  MongoDB用于分片设置时, 分片信息会存储在config数据库中.  

把数据库名添加到集合名前, 得到集合名的完全限定名, 即命名空间(namespace). 命名空间的长度不得超过121字节, 且在实际使用中应小于100字节.  

下载 MongoDB http://www.mongodb.org/downloads  

MongoDB在没有参数的情况下会使用默认数据目录 /data/db (Windows系统中为 C:\data\db). 如果数据目录不存在或者不可写, 服务器会启动失败. 因此, 再启动MongoDB前, 先创建数据目录, 以确保对该目录有写权限.  

默认情况下, MongoDB使用27017端口.  mongod还会启动一个非常基本的HTTP服务器, 监听数字比端口号高1000的端口, 也就是28017. 这意味着, 使用浏览器访问 http://localhost:28017, 能获得数据库管理信息.  

使用mongod启动时, 中止mongod的运行, 只须在运行服务器的shell中按下 Ctrl + C.  

使用 ` db ` 命令查看当前指向那个数据库.  

在shell中查看或操作数据库会用到4个基本操作: 创建, 读取, 更新和删除(即通常所说的CRUD).  

` insert `  函数可以将一个文档添加到集合中.  
` find ` 和 ` findOne ` 方法可以用于查询集合里的文档.  
使用 ` update ` 修改. update至少接受两个参数: 第一个是限定条件(用于匹配待更新的文档), 第二是新的文档.  
使用 ` remove ` 方法可以将文档从数据库中永久删除.  

## 数据类型  

在概念上, MongoDB的文档与JavaScript中的对象接近, 因而可以认为它类似于JSON. JSON(http://www.json.org) 是一种简单的数据表示方式: 其规范仅用一段文字就能描述清楚(其官网证明了这一点), 且仅包含6种数据类型. 这样有很多好处: 易于理解, 易于解析, 易于记忆. 然而, 从另一方面说, 因为只有 null, 布尔, 数字, 字符串, 数组和对象这几种类型, 所以JSON的表达能力有一定的局限.  


JSON没有日期类型, 这使原本容易的日期处理变得烦人. 另外, JSON只有一种数字类型, 无法区分浮点数和整数, 更别说32位和64位数字了. 再者, JSON无法表示其它一些通用类型, 如正则表达式或函数.  

MOngoDB在保留JSON基本键/值对特性的基础上, 添加了其他一些数据类型. 在不同的编程语言下, 这些类型的确切表示有些许差异. 下面说明MongoDB支持的其它通用类型, 以及如何在文档中使用它们.  

- null  用于表示空值或者不存在字段.  
- 布尔型  有两个值true和false.  
- 数值  shell默认使用64位浮点型数值.  
- 字符串  UTF-8字符串都可以表示为字符串类型的数据.  
- 日期  日期被存储为自新纪元以来经过的毫秒数, 不存储时区.  
- 正则表达式  查询时, 使用正则表达式作为限定条件, 语法也与JavaScript的正则表达式语法相同.  
- 数组  数据列表或数据集可以表示为数组.  
- 内嵌文档  文档可以嵌套其他文档, 被嵌套的文档作为父文档.  
- 对象id  对象id是一个12字节的ID, 是文档的唯一标识.  
- 二进制数据  二进制数据是一个任意字节的字符串. 它不能直接在shell中使用. 如果要将非UTF-8字符保存到数据库中, 二进制数据是唯一的方式.  
- 代码  查询和文档中可以包括任意JavaScript代码.  


创建日期对象时, 应使用 ` new Date(...) ` , 而非 ` Date(...) `. 如果不注意这一点, 没有始终使用日期(Date)构造函数, 将得到一堆混乱的日期对象和日期字符串. 由于日期和字符串之间无法匹配, 所以执行删除, 更新及查询等几乎所有操作时会导致很多问题.  

关于JavaScript日期类的完整解释, 以及构造函数的参数格式, 参见ECMAScript规范(http://www.ecmascript.org).  

文档可以作为键的值, 这样的文档就是` 内嵌文档 `. 使用内嵌文档, 可以使数据组织方式更加自然, 不用非得存成扁平结构的键. MongoDB这样做的坏处就是会导致更多的数据重复.  

MongoDB中存储的文档必须有一个"_id"键. 这个键的值可以是任何类型的, 默认是个ObjectId对象.  
ObjectId是"_id"的默认类型. 它设计成轻量型的, 不同的机器都能用全局唯一的同种方法方便地生成它. 因为在多个服务器上同步自动增加主键值既费力又费时. 因为设计MongoDB的初衷就是用作分布式数据库, 所以能够在分片环境中生成唯一的标示符非常重要.  

ObjectId使用12字节的存储空间, 是一个由24个十六进制数字组成的字符串(每个字节可以存储两个十六进制数字). 由于看起来很长, 不少人会觉得难以处理. 但关键是要知道这个长长的ObjectId是实际存储数据的两倍.  

ObjectId的12字节按照如下方式生成:  

```  
0-3  时间戳  
4-6  机器  
7-8  PID  
9-11  计数器  

0|1|2|3|4|5|6|7|8|9|10|11
       |     |   |  
时间戳 |机器 |PID| 计数器   
       |     |   |         
	   

```  

ObjectId的前4个字节是从标准纪元开始的时间戳, 单位为秒. 这会带来一些有用的属性.  
- 时间戳, 与随后的5字节组合起来, 提供了秒级别的唯一性.  
- 由于时间戳在前, 这意味着ObjectId大致会按照插入的顺序排列.  
- 这4字节也隐含了文档的创建的时间.
 
在服务器间进行时间同步确实是个好主意, 但是这里其实没有必要. 因为时间戳的实际值并不重要, 只要它总是不停增加就好了.  

接下来的3字节是所在主机的唯一标识符. 通常是机器主机名的散列值(hash).  

为了确保在同一台机器上并发的多个进程产生的ObjectId是唯一的, 接下来的两字节来自产生ObjectId的进程的进程标识符(PID).  

前9字节保证了同一秒钟不同机器进程产生的ObjectId是唯一的. 最后3字节是一个自动增加的计数器, 确保相同进程同一秒的ObjectId也是不一样的. 一秒钟最多允许每个进程拥有256^3(16 777 216)个不同的ObjectId.  

## Mongo Shell  

启动mongo shell时不连接到任何mongod有时很方便, 通过 ` --nodb ` 参数启动shell, 启动时就不会连接任何数据库.  

shell脚本的输出管道给另一个使用 ` --quiet ` 选项的命令, 就可以让shell不打印"MongoDB shell version ..."提示. 
使用 ` load() ` 函数,从交互shell中运行脚本.  
可以使用脚本将变量注入到shell. 例如, 可以在脚本中简单地初始化一些常用的辅助函数.  
除了添加辅助函数, 还可以使用脚本将通用的任务和管理活动自动化.  
默认情况下, shell会在运行shell时所处的目录中查找脚本(可以使用run("pwd")命令查看). 如果脚本不在当前目录中, 可以为shell指定一个相对路径或者绝对路径.  
load 函数无法解析 ` ~ `符号 . 

如果某些脚本会被频繁加载, 可以将它们添加到mongorc.js文件中. mongorc.js 最常见的用途之一是移除那些比较"危险"的shell辅助函数.  
将prompt变量设为一个字符串或者函数, 就可以重写默认的shell提示.  

可以使用 ` db.collectionName ` 获取一个集合的内容, 但是, 如果集合名称中包含保留字或者无效的JavaScript属性名称, db.collectionName 就不能正常工作了.  

# 第3章  创建,更新和删除文档  

  
 

 



 
 




 
 
  


  
 


























