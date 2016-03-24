
web_analysis_and_mining_with_r_cn  
Web Analysis and Mining With R  
**R语言与网站分析**  
李明/著  


个人博客:   www.bassary.com (无法访问)   
QQ邮箱:   568677413@qq.com  


# 第一章  统计思维与网站分析 #

现代统计学的很多理论均是在正态分布的基础上发展起来的. 一旦某一变量服从正态分布, 就可以使用对应的统计学理论来预测该不确定性变量的预测估值及其范围, 并可以给出该预测值的可信度.  

在统计学中除了正态分布以外, 还有很多典型分布. 网站统计分析的基础是: 找到一个服从某种分布(主要是正态分布)的变量, 用以衡量网站绝大部分用户(而非某一特定用户)的整体性能.  

## 统计分析方法 ##

网站分析一般有以下几种基础的思维方法, 也是大多数统计的方法.  
> (1) 细分分析.  网站分析的基础是找出服从正态分布(或其他分布)的变量, 细分分析是最主要的方式.  
> (2) 对比分析.  在细分分析后, 进一步使用对比分析来找到指标变量变化的真实原因.  
> (3) 趋势分析.  主要是研究指标变量的上升下降, 以及周期性等趋势性分析指标的异常变动.  


引流指标包括  浏览量(PV), 访问次数, 访客数(UV), 新访客数, 新访客比率 等等.  
黏性指标涉及  跳出率, 平均访问时长, 平均访问页数 等等.  
产出指标包含了  转化次数, 转化率 两个指标.  


# 第二章  R语言数据操作基础 #

R涉及两个含义, 它即表示一种用于数据分析建模以及绘图的语言. 又指一个有着统计分析功能及强大作图功能的软件系统. R语言是由新西兰奥克兰大学的Ross Ihaka和Robert Gentleman共同创建的. 因为它们把该语言称为R. 该语言在GUN协议 General Public Licence 4免费发行, 现在由"R开发核心团队"负责开发. 由于R语言是由AT&T贝尔实验室所创的S语言发展出的一种语言, 因此, 通常情况下使用S语言编写的代码都可以不做任何修改地在R环境下运行.  

R软件是一个免费的自由软件, 在R的安装程序中只包括8个基础模块. 其他扩展的算法包可以通过CRAN获得. 全球有超过100个CRAN镜像站.  

新用户可以通过 [http://cran.r-project.org/mirrors.html](http://cran.r-project.org/mirrors.html) 浏览CRAN镜像站地址, 包含中国大陆地区的镜像地址.  
这里使用[http://mirror.bjtu.edu.cn/cran/](http://mirror.bjtu.edu.cn/cran/)  

getwd() 命令可以读取工作目录的路径.  
setwd() 命令可以设置工作目录.  
```  
> setwd("C:/MyData/R")
> getwd()
[1] "C:/MyData/R"

> setwd(paste(getwd(),"/demo1",sep=""))
> getwd()
[1] "C:/MyData/R/demo1"


```  

R采用的是UNIX路径规则, 对于Windows系统的用户来说, 如果在路径中使用了Windows常用的"/"作为目录层级间隔符, 则会出错.  

可按上下方向键查看之前输入过的历史命令, 输入命令后, 按Enter键即可显示结果.  

sink() 命令可以将所有后续的输出由终端转向一个外部文件.  
```  
> sink("C:/MyData/R/demo1/demo.txt")
> getwd()
> getwd()
> sink()
> getwd()
[1] "C:/MyData/R/demo1"

```  

source 命令可批量执行命令脚本文件.  
```  
# demo1.r
print("Current workdir is " )
print(getwd())
print("")
print("Finish source command test!!! ")
# print("完成source命令测试")


```  
source命令时, 中文有乱码.  
```  
> source("C:/MyData/R/demo1/demo1.r")
[1] "Current workdir is "
[1] "C:/MyData/R/demo1"
[1] ""
[1] "Finish source command test!!! "
 
```

运行R时, 所有变量, 数据, 函数及结果都是以对象(Object)的形式存在计算机的活动内存中. 此时内存中的所有对象数据称为工作空间数据. 可以用save.image()函数把工作空间数据转存于硬盘中.  
` > save.image("C:/MyData/R/demo1/demo1.RData") `  

一般在处理大数据挖掘项目时, 前期的数据处理最为耗时. 此时通常会把项目中有关数据处理的命令单独制成脚本(可命名为init.data.r). 并在脚本文件最后使用save.image()语句把数据存于硬盘.  
在后续的数据分析脚本(可命名为sol.data.r), 可使用load()命令先载入数据处理脚本产生的对象数据. 这样, 在修整分析脚本时, 就不必再在数据处理阶段耗费时间了.  

&lt;R软件安装目录&gt;/doc/html/index.html 是其帮助文件的存储路径, 可以使用 help.start() 命令打开.  
```  
> help.start()
如果什么都不发生的话，你应该自己打开‘http://127.0.0.1:17185/doc/html/index.html’
```  

## R语言介绍 ##

R是一种解释性语言, 而不是编译语言. 也就是说, 输入的命令能够直接执行.  

R函数都存在一个库(library)中, 该库位于R软件的安装目录 /library 目录下. base包是R语言的核心包, 直接嵌于R软件安装包中, 它包含了数据读写, 操作最基本的函数.  

R对大小写很敏感. 即Z和z的含义是不同的变量名, 同一字母的大写和小写分别代表不同的变量对象. 另外, R对象名称不能以数字开始.  

R工作空间可存储变量和函数对象, 但是对于求值及打印命令, 并不会保存输出结果.  

R语言的注释标识是以井号(#)开始, #后面所跟的都是注释, 直到此行结束为止.  

使用 install.packages() 可以在命令行模式下安装包.  

使用 library() 或者 require() 可以在命令行模式下将已经安装好的包加载至工作空间内.  


从变量声明的角度区分, 数据类型可以分为静态类型和动态类型.  
在编译期间就确定数据类型的语言, 叫静态类型语言. 该类语言要求在使用任一变量之前必须先声明其数据类型. 例如, Java和C就是静态类型语言.  
在运行期间才确定数据类型的语言, 叫动态语言语言. 对于该类语言, 不必事先声明其数据类型, 而是通过第一次的赋值来定义其数据类型. R和Python为动态类型语言.  


从不同数据类型是否可混合使用的角度来区分, 语言又可以分为弱类型语言和强类型语言.  
在弱类型语言中, 不同数据类型间的数据可以混合使用. 例如, VBScript中, 可以将字符串'12'和整数3进行连接, 从而得到字符串'123'.  
在强类型语言中则相反, 不同数据类型间的数据不可以混合使用. 必须通过类型转化函数处理后才可以. R和Python就是强类型语言.  


## R数据类型 ##
R的数据类型有 数值型, 字符型, 复数型 和 逻辑型 .  
**数值(numeric)类型**的取值是实数, 在R环境中使用数字来表示.  
```  
> a<-9.111
> mode(a)
[1] "numeric"

> a<-12345
> mode(a)
[1] "numeric"

```  


**字符(character)类型**的取值是字符串, 在R语言中, 字符串需要使用英文双引号括起来表示.  
```  
> a<-"abcd123"
> mode(a)
[1] "character"

> a<-"Hello World!"
> mode(a)
[1] "character"

```  


**复数(complex)类型**的取值可扩展到虚数, 并使用&lt;实部数值&gt;+i&lt;虚部数值&gt;来表示.  
如果有数平方是负数的话, 那个数就是虚数. 所有的虚数都是复数.  
```  
> a<-100+11i
> mode(a)
[1] "complex"

> a<-10+123i
> mode(a)
[1] "complex"

```  

**逻辑(logical)类型**的取值为TRUE(也可以简写为T)和FALSE(也可以简写为F).  
```  
> a<-T
> mode(a)
[1] "logical"

> a<-FALSE
> mode(a)
[1] "logical"

```  

有两种特殊情况不能使用上述4种数据类型来描述, 数据的缺失和数据的未知状态.  
NA 表示数据集中的某数据缺失.  
NULL 表示未知的状态.  


最能说明NULL和NA区别的是, 当读取向量的长度时.  
NULL不占据任何工作空间, length(c(1,2,3,NULL,4))的返回值为4.  
而NA是占据工作空间的, length(c(1,2,3,NA,4))的返回值是5.  

```  
> length(c(1,2,3,NULL,4))
[1] 4

> length( c( 1, 2, 3, NULL, 4 ) )
[1] 4


> length(c(1,2,3,NA,4))
[1] 5

> length( c( 1, 2, 3, NA, 4) )
[1] 5

```  

有时判断一个数据是否为NA或NULL, 这时可以使用is.na和is.null函数.  
```  
> a<-c( 1, 2, 3, NA, 4, NULL, 5)
> is.na(a)
[1] FALSE FALSE FALSE  TRUE FALSE FALSE
> is.null(a)
[1] FALSE

```  

## 对象及其属性 ##

R语言内部数据变量, 函数结果, 产生的图像等, 都是以对象形式存在的. 不过由于R语言并非编程语言, 并且R语言的用户也非软件专业出身. 所以本书将会弱化函数和图像的对象概念, 更多强调的是数据变量的对象概念.  

R语言给出的常用对象有 向量(vector), 数组(array), 矩阵(matrix), 数据框(data frame), 列表(list) 等. 其中, 数据框和列表的内部元素可以使用不同的数据类型.  
vector和list是一维的, matrix和data frame是二维的, array是多维的.  

对象类型是指R语言组织和管理内部元素的不同方式, 数据类型则描述了一个变量内元素取值的类型.  

**不同对象类型元素取值的数据类型**
<table>
  <tr>
    <th>对象类型</th>  <th>数据类型</th>  <th>是否允许出现不同数据类型</th>
  </tr>
  
  <tr>
    <td>向量</td>  <td>数值型, 复数型, 字符型, 逻辑型</td>  <td>不允许</td>
  </tr>
  <tr>
    <td>因子</td>  <td>数值型, 复数型, 字符型, 逻辑型</td>  <td>不允许</td>
  </tr>
  <tr>
    <td>数组</td>  <td>数值型, 复数型, 字符型, 逻辑型</td>  <td>不允许</td>
  </tr>
  <tr>
    <td>矩阵</td>  <td>数值型, 复数型, 字符型, 逻辑型</td>  <td>不允许</td>
  </tr>
  <tr>
    <td>数据框</td>  <td>数据型, 复数型. 字符型, 逻辑型</td>  <td>相同列内元素, 其数据类型必须相同; 不同列之间的数据类型可以不同</td>
  </tr>
  <tr>
    <td>列表</td>  <td>数据型, 复数型, 字符型, 逻辑型</td>  <td>任何元素的数据类型均可不同</td>
  </tr>
  <tr>
    <td>时间序列</td>  <td>数值型, 复数型, 字符型, 逻辑型</td>  <td>不允许</td>
  </tr>
</table>


R语言使用属性来辅助性描述每个对象信息, 使用mode函数可以读取对象的模式(数据类型), 使用 as.&lt;数据类型&gt; 可以改变对象的模式.  
```  
> a<-"100"
> mode(a)
[1] "character"
> a<-as.numeric(a)
> mode(a)
[1] "numeric"


> a<-"100"
> mode(a)
[1] "character"
> a<-as.complex(a)
> mode(a)
[1] "complex"


> a<-10; b<-0
> mode(a)
[1] "numeric"
> mode(b)
[1] "numeric"
> paste("a=", as.logical(a), "b=", as.logical(b))
[1] "a= TRUE b= FALSE"
> mode(a)
[1] "numeric"



```  

使用length函数获取对象的长度属性. 在R中, 对象长度可以是0, 并可以对超过对象长度的下标赋值. 以增加对象的元素. 赋值后对象的长度会自动增长至此下标处, 而中间未被赋值的元素会被默认赋值为缺失值NA.  
```  
> x<-numeric()
> length(x)
[1] 0
> x[2]<-1
> x[4]<-16
> x
[1] NA  1 NA 16
> length(x)
[1] 4

```  

除了固有属性length和mode外, 要了解其余的属性, 可以使用attributes()和attr()函数来查看, 并且可以对其进行修改.  
attributes()函数读取除长度和模式以外的所有属性, 使用attr(&lt;对象&gt;, "names")读取列名属性. 其返回结果为向量, 并可以直接赋值, 以修改该属性.  
```  
> x<-numeric()
> x[3]<-6
> attributes(x)
NULL



> x<-data.frame(name1=c(1:5), name2=c("c1,","c2","c3","c4","c5"))
> attributes(x)
$names
[1] "name1" "name2"

$row.names
[1] 1 2 3 4 5

$class
[1] "data.frame"

> attr(x, "names")
[1] "name1" "name2"
> attr(x, "row.names")
[1] 1 2 3 4 5


> attr(x, "names")[1]<-"name_a"
> attr(x, "names")
[1] "name_a" "name2" 


```  

一旦某个对象过于庞大, 就往往会影响R软件环境的运行速度. 此时需要查看工作空间内现存的对象列表, 并删除其中的某个对象.  
```  
> ls()
[1] "a" "b" "s" "x"
> rm(a)
> a
错误: 找不到对象'a'


> rm(list=ls())
> ls()
character(0)

```  




## 向量 ##

向量是以一维数组的方式管理数据的一种对象类型, 可以说向量是R语言中最基本的对象类型. 很多算法都是以向量的形式输入的.  

R语言使用 c(&lt;元素1&gt;, &lt;元素2&gt;, ..., &lt;元素n&gt;) 来创建向量, 向量内各元素使用逗号分隔.  
```  
> a1<-c(1, 2, 3, 4)
> mode(a1)
[1] "numeric"

> a2<-c("test1", "test2", "test3", "test4")
> mode(a2)
[1] "character"

> a3<-c(T, F, T, F)
> mode(a3)
[1] "logical"



> a1_2<-c(1, 2, "test1", "test2")
> mode(a1_2)
[1] "character"


> a1_3<-c(1, 2, T, F)
> mode(a1_3)
[1] "numeric"


> a2_3<-c("test1", "test2", T, F)
> mode(a2_3)
[1] "character"



```  

R语言下标索引与其他类型语言的最大区别就是, 下标始于1, 而非0. 可使用**向量对象[下标]** 方式读取该下标的元素, 并且可用 **向量对象[-下标]** 方式读取除该下标元素外的其他元素.  
```  
> a<-c("test1", "test2", "test3", "test4")
> 
> a[1]
[1] "test1"
> a[-1]
[1] "test2" "test3" "test4"


> a[c(1, 3)]
[1] "test1" "test3"
> a[-c(1, 3)]
[1] "test2" "test4"


```  
除了使用一个数据作为下标, 还可以使用一个向量.  


使用which函数可以通过向量内元素的数值, 来得到此元素的向量下标. 如果有多个元素符合筛选要求, 则以向量方式返回多个下标.  
```  
> a<-c("test1", "test2", "test3", "test4")
> a
[1] "test1" "test2" "test3" "test4"


> which(a=="test1")
[1] 1
> which(a=="test3")
[1] 3

> which(a==c("test1", "test2"))
[1] 1 2
> which(a==c("test1", "test3"))
[1] 1
> which(a==c("test1", "test4"))
[1] 1 4

> which(a!="test2")
[1] 1 3 4
> 

```
which比较时, 会延长短向量与长向量一致, 再进行比较.  

对于数值类型的向量, 不仅可以使用等于和不等于方式来返回向量下标, 还可以使用大于或小于等方式返回.  
```  
> a<-c(101, 102, 103, 104)
> a

[1] 101 102 103 104
> which(a>102)
[1] 3 4

> which(a<=103)
[1] 1 2 3

> which(a>102 & a<=103)
[1] 3

> which(a>102 | a<=103)
[1] 1 2 3 4


> which.min(a)
[1] 1
> which(a==min(a))
[1] 1


> which.max(a)
[1] 4
> which(a==max(a))
[1] 4


> a[which(a>102 & a<=103)]
[1] 103

```  

which.max和which.min用于返回数值型向量中最大值和最小值元素的下标.  


subset函数可以方便地索引向量, 矩阵以及函数.  
```  
> a<-c(101, 102, 103, 104)
> subset(a, a > 102 & a <= 103)
[1] 103
# same to a[which(a > 102 & a <= 103)]

```  


match可以称作匹配函数, 形式为  
` match(x, table, nomatch = NA_integer_, incomparables = NULL) `  

<table>
  <tr>
    <th>参数>  <th>说明</th>
  </tr>
  
  <tr>
    <td>X</td>  <td>查询的对象</td> 
  </tr>
  <tr>
    <td>table</td>  <td>匹配的数值, 可以是向量形式</td>
  </tr>
  <tr>
    <td>nomatch</td>  <td>如果不匹配, 返回的数值, 默认返回缺失值NA</td>
  </tr>
  <tr>
    <td>incomparables</td>  <td>设置table参数中无效的匹配值, 默认不设置, 即为NULL<td>
  </tr>
</table>

```  
> a<-c(101, 101, 102, 102, 103, 103, 104, 104, NA, 101, NA, 102)
> a
 [1] 101 101 102 102 103 103 104 104  NA 101  NA 102

 
> match(a, c(101, 103))
 [1]  1  1 NA NA  2  2 NA NA NA  1 NA NA
 
> match(a, c(101, 103), nomatch = 0)
 [1] 1 1 0 0 2 2 0 0 0 1 0 0

 
> match(a, c(101, 103), nomatch = 0, incomparables = 1000)
 [1] 1 1 0 0 2 2 0 0 0 1 0 0
 
> match(a, c(101, 103), nomatch = 0, incomparables = 103)
 [1] 1 1 0 0 0 0 0 0 0 1 0 0
 
> match(a, c(101, 103), nomatch = 0, incomparables = 101)
 [1] 0 0 0 0 2 2 0 0 0 0 0 0
 
 
```  


%in%命令可以判定对象是否包含某项数据, 如果存在则返回T, 如果不存在则返回F.  
```  
> a<-c(101, 101, 102, 103, 104, 104, 103, 102)

> c(101, 105, 106)%in%a
[1]  TRUE FALSE FALSE

> c(103, 105, 101)%in%a
[1]  TRUE FALSE  TRUE


```  

在R语言中, 修改向量内某元素很简单, 只要通过索引找出特定的元素. 然后直接使用 <- 进行赋值即可. R语言还提供了扩展向量和删除元素的功能.  

R可以对对象长度进行任意扩展, 例如, c(&lt;向量1&gt;, &lt;向量2&gt;, ..., &lt;向量n&gt;) 即可将n个向量合并.  
```  
> a<-c(1, 2, 3, 4)
> a
[1] 1 2 3 4


> a<-c(a, c(101, 102, 103))
> a
[1]   1   2   3   4 101 102 103

```  

要删除向量内某一元素, 可使用对向量重新赋值的方式实现. 例如, a[-3]表示向量a中除了第三个元素以外的所有元素. 把它重新赋予a后实现删除a[3]的效果.  
```  
> a<-c(101, 102, 103, 104)
> a
[1] 101 102 103 104

> a<-a[-3]
> a
[1] 101 102 104
> length(a)
[1] 3

```  

R语言可以依据数值的大小使用sort进行排序, 使用rev依据下标进行倒排序. 结合sort和rev使用 rev(sort(&lt;向量&gt;)) , 可以对向量依据大小进行倒排序.  
```  
> a<-c(95, 98, 96, 97, 106, 101, 102, 104, 103)
> a
[1]  95  98  96  97 106 101 102 104 103

> rev(a)
[1] 103 104 102 101 106  97  96  98  95

> sort(a)
[1]  95  96  97  98 101 102 103 104 106

> rev(sort(a))
[1] 106 104 103 102 101  98  97  96  95

> rev(rev(sort(a)))
[1]  95  96  97  98 101 102 103 104 106


```  

希望删除向量内重复的元素, 这时可以使用 unique() 来实现.  
```  
> a<-c(95, 97, 96, 95, 94, 99, 98, 97 ,96)
> a
[1] 95 97 96 95 94 99 98 97 96
> unique(a)
[1] 95 97 96 94 99 98

```  

此功能同样可以使用table函数来实现, 不过比较麻烦.  
```  
> a<-c(95, 97, 96, 95, 94, 99, 98, 97 ,96)
> a
[1] 95 97 96 95 94 99 98 97 96
> as.numeric(names(table(a)))
[1] 94 95 96 97 98 99

```  

实际工作中, 数据集很少是完整的, 许多情况下样本都会包括若干缺失值NA. 这在进行数据分析和挖掘时比较麻烦. R语言通过 na.fail 和 na.omit 函数可以很好地处理样本中的缺失值.  

<table>
  <tr>
    <th>函数</th>  <th>用法</th>  <th>说明</th>
  </tr>

  <tr>
    <td>na.fail</td>  <td>na.fail(&lt;向量a&gt;)</td>  <td>如果向量a内包括至少一个NA, 则返回错误; 如果不包括任何NA, 则返回原有向量. </td>
	
  </tr>
  <tr>
    <td rowspan="2">na.omit</td>  <td>na.omit(&lt;向量a&gt;)</td>  <td>返回删除NA后的向量a</td>
  </tr>
  <tr>
    <td>attr(na.omit(&lt;向量a&gt;), "na.action")</td>  <td>返回向量a中元素为NA的下标</td>
  </tr>
</table>

```  
> a0<-c(95, 99, 97, 93, 96)
> a0
[1] 95 99 97 93 96
> a1<-c(95, 99, 97, NA, 96)
> a1
[1] 95 99 97 NA 96
> a2<-c(95, NA, 97, 93, NA)
> a2
[1] 95 NA 97 93 NA


> na.fail(a0)
[1] 95 99 97 93 96
> na.fail(a1)
Error in na.fail.default(a1) : 对象里有遺漏值
> na.fail(a2)
Error in na.fail.default(a2) : 对象里有遺漏值


> na.omit(a0)
[1] 95 99 97 93 96
> na.omit(a1)
[1] 95 99 97 96
attr(,"na.action")
[1] 4
attr(,"class")
[1] "omit"
> na.omit(a2)
[1] 95 97 93
attr(,"na.action")
[1] 2 5
attr(,"class")
[1] "omit"


> attr(na.omit(a0), "na.action")
NULL
> attr(na.omit(a1), "na.action")
[1] 4
attr(,"class")
[1] "omit"
> attr(na.omit(a2), "na.action")
[1] 2 5
attr(,"class")
[1] "omit"


```  

函数na.fail和na.omit不仅可以应用于向量, 也可以应用于矩阵和数据框. 另外, 还可以使用 !x 方式方便地删除NA.  
```  
> t<-c(95, 99, 97, NA, 101, 106, NA, 104)
> t
[1]  95  99  97  NA 101 106  NA 104

> t[!is.na(t)]
[1]  95  99  97 101 106 104

```  


使用 pmin(&lt;向量1&gt;, &lt;向量2&gt;, ..., &lt;向量n&gt;) 可以依次比较向量1至向量n内的各元素, 并把较小的元素组成新的向量. pmax则是把较大的元素组成新的向量.  
```  
> a<-c(1, 2, 3, 4, 5)
> a
[1] 1 2 3 4 5
> b<-c(1, 3, 5, 7, 9)
> b
[1] 1 3 5 7 9
> c<-c(2, 3, 6, 7, 9)
> c
[1] 2 3 6 7 9
> d<-c(2, 3, 2, 4, 10, 12, 13)
> d
[1]  2  3  2  4 10 12 13


> pmin(a, b, c, d)
[1] 1 2 2 4 5 1 2
Warning message:
In pmin(a, b, c, d) : 参数值将数据略微回收

> pmin(d, a, b, c)
[1] 1 2 2 4 5 1 2
Warning message:
In pmin(d, a, b, c) : 参数值将数据略微回收

> pmax(a, b, c, d)
[1]  2  3  6  7 10 12 13
Warning message:
In pmax(a, b, c, d) : 参数值将数据略微回收

> pmin(a, b, c)
[1] 1 2 3 4 5
> pmax(a, b, c)
[1] 2 3 6 7 9


> intersect(a, b)
[1] 1 3 5
> intersect(b, a)
[1] 1 3 5
> intersect(a, d)
[1] 2 3 4


> union(a, b)
[1] 1 2 3 4 5 7 9
> union(b, a)
[1] 1 3 5 7 9 2 4
> union(a, d)
[1]  1  2  3  4  5 10 12 13


> setdiff(a, b)
[1] 2 4
> setdiff(b, a)
[1] 7 9
> setdiff(a, d)
[1] 1 5


```  
除了比较向量的大小外, 在处理样本间的关系时, 还有一组更常用的操作, 即 取交集, 并集, 补集.  
intersect(&lt;向量1&gt;, &lt;向量2&gt;)  返回由既属于向量1, 也属于向量2的元素(交集)所组成的向量.  
union(&lt;向量1&gt;, &lt;向量2&gt;)  返回由属于向量1或者属于向量2的元素(并集)所组成的向量.  
setdiff(&lt;向量1&gt;, &lt;向量2&gt;)  返回由属于向量1而不属于向量2的元素(补集)所组成的向量.  





























 








  





