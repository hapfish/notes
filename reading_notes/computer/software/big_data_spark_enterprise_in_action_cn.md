
Big Data Spark Enterprise in action

Spark亚太研究院  王家林  编著  
电子工业出版社  


# 前言 #

诞生于伯克利大学AMPLab的Spark. 基于RDD, 成功构建了一体化, 多元化的大数据处理体系.  

DataBricks和AWS联合所做的Sort Benchmark测试表明, 在任意大小的数据规模下, Spark在性能和扩展性上都更具优势.  





# 第1章 Spark编程模型 #

在"One Stack to rule them all"思想的引领下, Spark成功使用Spark SQL, Spark Streaming, MLlib, GraphX近乎完美地解决了大数据中Batch Processing, Streaming Processing, Ad-hoc Query等三大核心问题. 在Spark中, Spark SQL, Spark Streaming, MLlib, GraphX四大子框架和库之间可以无缝地共享数据和操作.  

Apache Spark官方的定义为: Spark是一个通用的大规模数据快速处理引擎.  

Spark提供的基于RDD的一体化解决方案, 将MapReduce, Streaming, SQL, Machine Learning, Graph Processing等模型统一到一个平台下, 并以一致的API公开且提供相同的部署方案.  

Hadoop本身的计算模型决定了Hadoop上所有的工作都需要转化成Map, Shuffle和Reduce等核心阶段, 由于每次计算都要从磁盘读或者写数据, 同时整个计算模型需要网络传输, 这就导致了不能忍受的延迟性, 同时在前一个任务运行完之前, 任何一个任务都不可以运行, 这直接导致了其无力支持交互式应用.  

基于Spark内存的计算速度比Hadoop MapReduce快100倍以上, 基于磁盘的计算速度也要快10倍以上. 原因是Spark有一个Directory Acyclic Graph(DAG 有向无环图) 执行引擎, 支持循环数据流和内存计算.  

Spark可以运行在第二代Hadoop集群管理之上, 这使得Spark可以读取Hadoop的任何数据, 如果已经有第二代Hadoop集群, 无需安装即可直接运行Spark, Spark很容易运行在standalone或者EC2或者Mesos上. 它能读取HDFS, HBase, Cassandra以及任何Hadoop数据源.  


Spark的几种运行模式  

<table>
  <tr>
    <td>local</td>  <td>本地模式</td>  <td>常用于本地开发测试, 本地还分为local和local-cluster. local-cluster其实是多线程, 能够在单机上处理一定的并发</td>
  </tr>
  <tr>
    <td>standalone</td>  <td>集群模式</td>  <td>典型的Master/Slave模式, 不过也能看出Master是有单点故障的; Spark支持ZooKeeper来实现HA</td>
  </tr>
  <tr>
    <td>on yarn</td>  <td>集群模式</td>  <td>运行在Yarn资源管理器框架之上, 由Yarn负责资源管理, Spark负责任务调度和计算</td>
  </tr>
  <tr>
    <td>on mesos</td>  <td>集群模式</td>  <td>运行在Mesos资源管理器框架之上, 由Mesos负责资源管理, Spark负责任务调度和计算</td>
  </tr>
  <tr>
    <td>on cloud</td>  <td>集群模式</td>  <td>比如AWS的EC2, 使用这个模式能很方便地访问Amazon的S3. Spark支持多种分布式存储系统, HDFS和S3</td>
  </tr>
</table>  
  
  
  
常见术语  
<table>
  <tr>
    <td>Application</td>  <td>Spark的应用程序, 包含一个Driver program和若干个Executor</td>
  </tr>
  <tr>
    <td>SparkContext</td>  <td>Spark应用程序的入口, 负责调度各个运算资源, 协调各个Worker Node上的Executor</td>
  </tr>
  <tr>
    <td>Driver Program</td>  <td>运行Application的main()函数并且创建SparkContext</td>
  </tr>
  <tr>
    <td>Executor</td>  <td>是Application运行在Worker node上的一个进程, 该进程负责运行Task, 并且负责将数据存在内存或者磁盘上. 每个Application都会申请各自的Executors来处理任务</td>
  </tr>
  <tr>
    <td>Cluster Manager</td>  <td>在集群上获取资源的外部服务(例如: Standalone, Mesos, Yarn)</td>
  </tr>
  
  <tr>
    <td>Worker Node</td>  <td>集群中任何可以运行Application代码的节点, 运行一个或多个Executor进程</td>
  </tr>  
  <tr>
    <td>Task</td>  <td>运行在Executor上的工作单元</td>
  </tr>
  <tr>
    <td>Job</td>  <td>SparkContext提交的具体Action操作, 常和Action对应</td>
  </tr>
  <tr>
    <td>Stage</td>  <td>每个Job会被拆分很多组任务(task), 每组任务被称为Stage, 也称TaskSet</td>
  </tr>
  <tr>
    <td>RDD</td>  <td>Resilient distributed datasets的简称, 中文为弹性分布式数据集, 是Spark最核心的模块和类</td>
  </tr>
  
  <tr>
    <td>DAGScheduler</td>  <td>根据Job构建基于Stage的DAG, 并提交Stage给TaskScheduler</td>
  </tr>  
  <tr>
    <td>TaskScheduler</td>  <td>将Taskset提交给Worker node集群运行并返回结果</td>  
  </tr>
  <tr>
    <td>Transformations/Action</td>  <td>Spark API的两种类型. Transformation返回值还是一个RDD. Action的返回值不是一个RDD, 而是一个Scala集合. <br/>
	所有Transformation都是采用的懒策略, 如果只是将Transformation提交是不会执行计算的, 计算只有在Action被提交时才被触发
  </tr>
  
</table>



Spark Core包含Spark的基本功能, 这些功能包括任务调度, 内存管理, 故障恢复以及存储系统的交互等, 重要概念, RDD, Stage, DAG. Spark的核心思想就是将数据集缓存到内存里, 并用Lineage机制来进行容错.  

RDD是弹性分布式数据集(Resilient Distributed Dataset)的简称, 是分布式只读的且已分区的集合对象. 这些集合是弹性的, 如果数据集的一部分丢失, 则可以对它们进行重建. 具有自动容错, 位置感知调度和可伸缩性, 而容错性是最难实现的. 大部分分布式数据集的容错性有两种方式: 数据检查点和记录数据的更新. RDD也只支持粗粒度的转换.  

RDD包含了2类API:  
> (1) Transformations  转换操作, 返回值还是一个RDD, 如map, filter, union.  
> (2) Actions  行动操作, 返回结果或是把RDD持久化起来, 如count, collect, save.  

Transformation采用懒策略, 如果只是将transformation提交是不会提交任何任务来执行, 任务只有在action被提交时才被触发.  

RDD之间的依赖有以下两种:  
> (1) 窄依赖(Narrow Dependency)  一个父RDD最多被一个子RDD引用, 如map, filer, union操作.  
> (2) 宽依赖(Wide Dependency)  一个父RDD被多个子RDD引用, 如groupByKey操作.  

Spark提交Job之后会把Job生成多个Stage, 多个Stage之间是有依赖的. Stage之间的依赖关系构成了DAG(有向无环图).  

Spark Streaming是一个对实时数据流进行高通量, 容错处理的流式处理系统, 可以对多种数据源(如Kafka, Flume, Twitter, Zero和TCP套接字)进行map, reduce, join, window等复杂操作, 并将结果保存到外部文件系统, 数据库, 或应用到实时仪表盘.  

构建在Spark上处理Stream数据的框架, 基本的原理是将Stream数据分成小的时间片段(几秒), 以类似batch批量处理的方式来处理这一小部分数据. Spark Streaming构建在Spark上, 一方面是因为Spark的低延迟执行引擎(100ms+)可以用于实时计算, 另一方面相比基于Record的其他处理框架(如Storm), RDD数据集更容易做高效的容错处理.  


  
  




