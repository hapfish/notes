
r_object_and_function

R Object and Function


# R对象 #

**基础数据类型**  numeric(数值型), complex(复数型), logical(逻辑型), character(字符型)  

**常用对象类型**  vector(向量), array(数组), matrix(矩阵), data frame(数据框), list(列表), factor(无序因子), ordered(有序因子), ts(时间序列)  

**绘图函数**  

text(文本)    
points(点)  

abline(直线)  
segments(线段)  
arrows(箭头)  
lines(线)  

legend(图例)  
axis(坐标轴)  
grid(网格线)  

plot(散点图, 气泡图, 曲线图, 密度曲线图)  
barplot(柱形图, 条形图)  
pie(饼图)  
ggplot2包(气泡图(ggplot), 堆积面积图(gplot), )  
fms包 radarchart(雷达图, 星状图)  
igraph(关系网络图)  


## 分析 ##

回归分析的目标是连续性变量, 使用lm, step  
分类分析的目标是分类型变量, 使用ctree, J48, randomforest, bnlearn  

回归树(也称分类回归树CART)主要以一种树状结构来表达回归分析模型的回归算法, 该类方法不仅可以应用于回归分析(称为回归树), 也可用于分类分析(称为分类树).  
CART算法的核心包是rpart包的rpart函数.  

贝叶斯分类器(Bayesian classifiers) 是基于贝叶斯定理计算目标变量条件概率的一种建模方法. 朴素贝叶斯的实现方式, e1071中的naiveBayes函数.  

nnet包的nnet函数可以建立一层隐藏的神经网络模型.  

dist函数可以把一个矩阵或数据框转化为距离矩阵.  

hclust函数进行层次聚类.  

kmeans函数进行kmeans算法计算.  

class包的knn函数可以实现knn算法的基本功能.  

arules包的apriori是Apriori算法的核心函数.  

arulesSequences包的cspade函数实现了cSPADE算法.  

init.igraph函数建立无向图和有向图.  

clusters函数发现基于点链接的社群发现.  

igraph包的walktrap.community函数实现随机游走的社群发现.  

igraph包的spingclass.community, edge.betweenness.community, label.propagation.community 函数实现随机游走的社群发现.  

tm包是文本分析的核心  
corpus是tm处理所有文本的集合  
volatile corpus会把语料库对象存储在内存中  
permanent cirpus会把对象存储与外部数据中  

htmlParse函数是XML包加装HTML静态页面的函数  