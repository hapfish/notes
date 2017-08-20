
# concept_dictionary.md   


- [Lucene](#Lucene)  
- [Spring](#Spring)  
- [ZooKeeper](#ZooKeeper)  



<span id = "Lucene" ></span>  

# Lucene  

创建索引时, 将分档分成一个一个单词, 去掉标点符号和停词.  将单词变为小写, 缩减为词根形式或者转变为词根形式. 利用单词和文档标识简历字典, 再按照单词字母顺序排序. 合并相同的词(Term)成为文档倒排(Posting List)链表.  



<span id = "Spring"></span>  

# Spring  

IoC - Inversion of control控制反转. 控制权由对象本身转向容器, 由容器根据配置创建实例并维护实例之间的依赖关系.  
AOP - Aspect Oriented Programming 面向切面编程  

http://www.cnblogs.com/zrtqsk/p/3735273.html  
Spring Bean生命周期  
实例化BeanFactoryPostProcessor实现类, 执行BeanFactoryPostProcessor的postProcessBeanFactory方法, 实例化BeanPostProcessor实现类,  
实例化InstantiationAwareBeanPostProcessorAdapter实现类, 执行InstantiationAwareBeanPostProcessor的postProcessBeforeInstantiation方法,  
执行Bean的构造器, 执行InstantiationAwareBeanPostProcessor的postProcessPropertyValues方法, 
为Bean注入属性, 调用BeanNameAware的setBeanName方法, 调用BeanFactoryAware的setBeanFactory方法,  
执行BeanPostProcessor的postProcessBeforeInitialization方法, 调用InitializingBean的afterPropertiesSet方法,  
调用bean的init-method属性指定的初始化方法, 执行BeanPostProcessor的postProcessAfterInitialization方法,  
执行InstantiationAwareBeanPostProcessor的postProcessAfterInitialization方法,  
容器初始化成功, 执行正常调用,  
调用DiposibleBean的destroy方法, 调用bean的destroy-method属性指定的方法  


  




<span id = "ZooKeeper" ></span>  

# ZooKeeper  

http://zookeeper.apache.org/  
http://zookeeper.apache.org/doc/r3.4.10/  

ZooKeeper是一个分布式的, 开源的分布式应用的协调服务. 可以在ZooKeeper基础上实现同步, 配置管理, 命名服务.  
ZooKeeper角色包含 Leader, Learner, Client. 其中Learner分为Follower和Observer.  

ZooKeeper使用Zab协议保证各个Server之间的同步. Zab协议有两种模式恢复模式(选主)和广播模式(同步).  
ZooKeeper采用了递增的事务id号(zxid)来标识事务, 所有提议(proposal)都在被提出的时候加上zxid. zxid是一个64位的数字, 高32位是epoch来标识leader关系是否改变, 每个新的leader选出来, 会有一个新的epoch. 低32位用于递增计数.  

ZooKeeper Server工作在三种状态: 
- LOOKING  当前Server正在搜寻Leader  
- LEADING  当前Server为Leader  
- FOLLOWING 与Leader同步  




  



