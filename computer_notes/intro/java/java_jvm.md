
Java JVM

[The Java Virtual Machine Specification](http://docs.oracle.com/javase/specs/jvms/se8/html/index.html)  


[JDK8 and JRE8](http://docs.oracle.com/javase/8/docs/)  



# Run-Time Data Areas #
[JVM Run-Time Data Areas](http://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5)  

pc Register  
Java Virtual Machine Stacks  
Heap  
Method Area  
Rum-Time Constant Pool  
Native Method Stacks  


# JVM Options #

[Java HotSpot VM Options](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html)  


[JDK8 Windows Options](http://docs.oracle.com/javase/8/docs/technotes/tools/windows/java.html)  

[JDK8 Linux Options](http://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html#BABDJJFI)  


标准选项用于所有JVM, 在通用动作使用, 例如检查JRE版本, 设置class path, 输出verbose.  
非标准选项是Java HotSpot Vitual Machine特性, 不被所有JVM支持, 选项以 ` -X `开始.  
高级选项不建议随便使用, 这些开发选项是为特定的系统要求和特殊的配置系统参数, 不被所有JVM支持, 以 ` -XX `开始.  

`k` 或 'K' 表示 kilobytes(KB), `m` 或 `M` 表示 megabytes(MB), `g` 或 `G` 表示gigabytes(GB).  



## Standard Options 标准选项 ##

` -agentlib:libname[=options] `  加载指定的本地代理库.  
加载 heap profiling tool(HPROF)库, 并且每20秒获取CPU信息, 栈深度为3:  
` -agentlib:hprof=cpu=samples,interval=20,depth=3 `  
加载Java Debug Wire Protocol(JDWP)库, 并且监听8000端口socket连接, 在JVM主类加载前悬停:  
` -agentlib:jdwp=transport=dt_socket,server=y,address=8000 `


` -agentpath:pathname[=options] ` 加载指定绝对路径的本地代理库.  

` -client` 选择Java HotSpot客户端VM, 64位的JDK目前忽略这个选项.  

` -Dproperty=value `  设置系统属性. 有空格的属性需要使用双引号. ` -Dfoo="foo bar" `  

` -d32 ` 运行32位环境, 不支持报错.  

` -d64 ` 运行64位环境, 不支持报错.  

` -help ` 帮助.  

` -jar filename ` 执行jar文件.  

` -version ` 版本.  






## Non-Standard Options 非标准选项 ##

` -X ` 显示所有 `-X`选项.  

` -Xbootclasspath:path ` 指定目录列表, jar文件, zip文件加载到JDK. 不能包含` rt.jar `.    

` -Xbootclasspath/a:path ` 在启动类class path后加载jar文件, zip文件. 不能包含` rt.jar `.  

` -Xbootclasspath:/p:path ` 在启动类class path前加载jar文件, zip文件. 不能包含 ` rt.jar `.  

` -Xdebug ` 

` -Xloggc:filename ` 重定向GC事件信息的日志. `-Xloggc`会覆盖`-verbose:gc` 

` -Xmaxjitcodesize=size ` JIT编辑代码的最大缓存, 默认48MB, `-Xmaxjitcodesize=240m`  


` -Xmn ` 堆的年轻代初始和最大空间. ` -Xmn256m `   

` -Xms ` 堆的初始化空间. ` -Xms256m `  

` -Xmx ` 内存分配池的最大空间. ` -Xmx512m `  

` -Xss ` 线程栈的空间. ` -Xss1m `  









## Advanced Runtime Options 高级运行选项 ##

` -XX:ErrorFile=filename `  设置错误数据路径和文件名.  

` -XX:+ShowMessageBoxOnError ` JVM错误显示对话框.  

` -XX:ThreadStackSize=size ` 线程栈空间. `-XX:ThreadStackSize=1m`, 等同于'-Xss'  





## Advanced JIT Compiler Options 高级运行编辑选项 ##

JIT  just-in-time





## Advanced Serviceability Options 高级可服务性选项 ##

` -XX:+HeapDumpOnOutOfMemory ` java.lang.OutOfMemoryError异常使用heap profiler(HPROF)打印Java heap信息.  

` -XX:HeapDumpPath=path ` 当 `-XX:+HeapDumpOnOutOfMemory`设置, head profiler(HPROF)输出heap dump的文件.  
` -XX:HeapDumpPath=./java_pid%p.hprof `  
` -XX:HeapDumpPath=/var/log/java/java_heapdump.hprof `  

` -XX:LogFile=path`  日志文件. 默认在当前工作目录, 名称为`hotspot.log`. `-XX:LogFile=/var/log/java/hotspot.log`  







## Advanced Garbage Collection Options 高级垃圾回收选项 ##

` -XX:G1HeapRegionSize=size `  在1MB到32MB.  

` -XX:+G1PrintHeapRegions `  

` -XX:G1ReservePercent=percent `  堆的保留百分比, 防止GC失败. 默认10%, 设置为20% `  -XX:G1ReservePercent=20`  

` -XX:InitialHealSize=size `  

` -XX:MaxGCPauseMillis=time  `  GC最大停顿时间, 毫秒.  

` -XX:MaxHeapSize `  相当于`-Xmx`

` -XX:MaxMetaspaceSize=size ` 本地内存分配给class metadata的最大值.  

` -XX:MaxNewSize ` 堆的年轻代空间.  

` -XX:MetaspaceSize=size ` class metadata分配的内存.  

` -XX:NewRatio ` 年轻代和年老代的比值, 默认为1.  

` -XX:+PrintGC ` 打印GC信息.  

` -XX:+PrintGCApplicationConcurrentTime `  

` -XX:+PrintGCApplicationStoppedTime `  

` -XX:+PrintGCDateStamps `  

` -XX:+PrintGCDetails ` 

` -XX:+PrintGCTaskTimeStamps `  

` -XX:+PrintGCTimeStamps `  




 

































