

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






 




