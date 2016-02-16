

# 高性能MySQL_读书笔记 #

###### 值得优化的查询 ######
1. 占总响应比时间很少的不值得优化
2. 优化成本大于收益, 应当停止优化

#### New Relic 作者推荐的一款好工具 ####
MySQL企业级监控软件 Enterprise Monitor

MySQL 5.1及更新的版本, 可以通过long_query_time=0捕获所有查询,

show profile # 未来可能被Performance Schema取代

mysql> set profiling =1;

mysql> show profile;

show status 命令返回一些句柄计数器

show global status

show innodb status

show processlist

Threads_connected 设置触发条件



权限不足时,有两种替代方案, 一种是 --processlist, 另一种是通过tcp抓包. 都已经集成在Percona Tool中的py-query-digest.

pt-stalk触发条件收集日志.

pt-collect一般通过pt-stalk调用,需要root权限.

pt-sift会轮流导航到所有数据的脚本.

pt-mysql-summary都会输出mysql状态和配置信息, 以及操作系统和硬件信息.

strace工具可以调查系统调用的情况, 用法和oprofile有点像, oprofile可以剖析程序的内部符号. strace附加上去后, mysqld会变得缓慢.


Domas Mituzas开发了著名的穷人剖析工具"poor man's profiler". 他目前在facebook工作, http://www.poormansprofiler.org


LOAD DATA INFILE 将Apache日志载入MySQL数据库中, 然后通过SQL查询.


## 介绍的软件 ##

###### Web测试 ######
ab  http://www.apache.org/docs/2.0/programs/ab.html

http_load  http://www.acme.com/software/http_load

JMeter  http://jmeter.apache.org

###### MySQL基准测试 ######
sql-bench mysql自带, 默认在 /usr/share/mysql/sql-bench

sysbench https://launchpad.net/sysbench/   https://github.com/akopytov/sysbench


###### 绘图工具 ######

gnuplot

R

###### MySQL工具 ######
Percona Toolkit

pt-index-usage  pt-duplicate-key-checker  pt-query-digest

myisamchk 可修复MyISAM索引

rsync 备份可能导致InnoDB数据问题



pt-upgrade 检查新版本中运行的SQL是否与老版本一样. 返回相同的结果.

快速, 精确, 实现简单三者永远只能满足其二, 必须舍弃掉其中一个.

pt-query-advidsor 能够解析查询日志, 分析查询模式, 然后给出所有可能存在潜在问题的查询. 并给出足够详细的建议.

UDF 用户自定义函数  UDF仓库  http://www.mysqludf.org

后台插件 handler-socket 监听新网络端口, 使用简单协议访问MySQL.

认证插件可实现MySQL认证功能, 如PAM和LDAP认证.

只有当缓存带来的资源节约大于其本身的资源消耗时才会给系统带来提升. 
Qcache_hits / (Qcache_hits + Conn_select) 为缓存命中率.  命中和写入比率 Qcache_hits : Qcache_inserts 

innotop监控

max_length_for_sort_data 索引最大长度  
max_sort_length  BLOB或TEXT前缀

排序方法  single-pass和two-pass

pt-pmp  通过堆栈跟踪来诊断竞争问题  
一般磁盘每秒100个随机I/O, 每秒50MB顺序读取. 顺序读是随机读的5000倍.  
内存随机访问速度比磁盘快2500倍. 内存顺序访问只有磁盘的10倍.  

P417  
多路由流量绘图 Multi Router Traffic Grapher  
MRTG  http://oss.oetiker.ch/mrtg  
网络性能监控工具  Smokeping http://oss.oetiker.ch/smokeping  
cacti  http://www.cacti.net  

vmstat  isstat  mpstat  sar  netstat  
dstat  http://dag.wieers.com/home-made/dstat/  
collectl  http://collectl.sourceforge.net  

pt-diskstats

xtrabackup 是一款开源的热备份工具  


### 备份 ###
一个MySQL备库只能有一个主库. 每个备库必须有一个唯一的服务器ID. MySQL不支持多主库复制.  
(1) 一主库多备库  
(2) 主动-主动模式下的主-主复制  
(3) 主动-被动模式下的主-主复制  
(4) 拥有备库的主-主结构  
(5) 环形复制(脆弱, 应该避免. 可增加节点备库)  
(6) 主库 分发主库 备库(可以使用多个分发主库, 在主库满负载时引入)  
(7) 树形或金字塔形  

pt-heartbeat可以创建一个粗糙的全局事务ID, 方便在多个服务器上寻找二进制日志的位置.

heartbeat record  

pt-heartbeat 监控延迟, 维护元数据.  
pt-table-checksum  比较数据.  
pt-table-sync  解决重新同步主库, 结合pt-table-checksum使用.  
pt-slave-restart  忽略备库重启产生的错误.  
pt-archiver  把大命令分成小命令, 使其尽可能简短.  


OLTP  在线事务处理  
OLAP  在线数据分析  

mk-slave-prefetch  是meatkit的一款工具, 实现了预取缓存.  
slavereadahead  预取工具, Anders Karlsson, http://sourceforge.net/projects/slavereadahead/   

Hibernate Shards  http://shards.hibernate.org  是一个支持分片的数据库抽象层  
HiveDB  分片系统  

###### 负载均衡 ######
(1) 可扩展性  
(2) 高效性  
(3) 可用性  
(4) 透明性  
(5) 一致性  

wackamole  http://www.backhand.org/wackamole/  基于端点(perr-based)实现  
LVS  (Linux Virtual Server)  http://www.linuxvirtualserver.org  

TCP代理  
MySQL Proxy  (用的不多)  
硬件负载均衡  
HAProxy  http://haproxy.1wt.eu    http://www.haproxy.com/  
TCP代理  Pen  http://siag.nu/pen

niux故障转移 High Availability Linux  http://linux_ha.org(连接不可用)  http://www.oracle.com/us/technologies/linux/product/linux-high-availability/overview/index.html  

scalr http://scalr.net  是一个流式的开源服务, 用于在云中进行MySQL复制自动扩展.  http://www.scalr.com/  

从表空间的数据文件直接抽取数据.   
InnoDB Recovery Toolkit  http://www.percona.com/software  
备份工具  Percona XtraBackup, mysqldump

  


  

 


 






 

 


 




