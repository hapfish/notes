
CentOS 安装 Solr

[Solr官网](http://lucene.apache.org/solr/)  

[Solr资源](http://lucene.apache.org/solr/resources.html)  

[Solr指导文档](http://lucene.apache.org/solr/guide/)  

[Solr下载](http://mirror.bit.edu.cn/apache/lucene/solr/)  

[Solr安装文档](http://lucene.apache.org/solr/quickstart.html)  

http://lucene.apache.org/solr/guide/6_6/using-zookeeper-to-manage-configuration-files.html  



# 安装Solr #

下载`solr.zip` 到 `/soft`目录.  

安装Java, 如果存在则不需要  
  
```  
-- 上传安装包  
$ scp jdk-8u144-linux-x64.rpm root@192.168.1.101:/soft
$ scp solr-6.6.0.zip root@192.168.1.101:/soft

-- 安装Java  
# useradd lidongxu
# passwd lidongxu
# vim /etc/sudoers
lidongxu    ALL=NOPASSWD:ALL



$ sudo cd /soft/
$ sudo rpm -ivh jdk-8u144-linux-x64.rpm

$ sudo java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)

```  


安装Solr  

```  
$ sudo cd /soft/
$ sudo ls solr*



# Need unzip
$ sudo yum -y install zip unzip



$ sudo unzip -q solr-6.6.0.zip
$ sudo cp -R /soft/solr-6.6.0 /usr/local/


```  

# 启动Solr #

```  
root用户启动需要 -force参数  
$ sudo /usr/local/solr-6.6.0/bin/solr start -cloud -force

$ /usr/local/solr-6.6.0/bin/solr create -c demo
$ /usr/local/solr-6.6.0/bin/solr create -c gettingstarted


```  

Solr界面:   http://192.168.1.101:8983/



# 索引数据 #

```  
$ sudo /usr/local/solr-6.6.0/bin/post -c gettingstarted /usr/local/solr-6.6.0/example/exampledocs/*.xml

-- XML
$ sudo /usr/local/solr-6.6.0/bin/post -c gettingstarted /usr/local/solr-6.6.0/example/exampledocs/*.xml
-- json
$ sudo /usr/local/solr-6.6.0/bin/post -c gettingstarted /usr/local/solr-6.6.0/example/exampledocs/books.json
-- csv  CSV(Comma/Column Separated Values) 
$ sudo /usr/local/solr-6.6.0/bin/post -c gettingstarted /usr/local/solr-6.6.0/example/exampledocs/books.csv

-- 其它索引技术
**DIH (Data Import Handler)**  

**SolrJ** 或其它Solr客户端  

Admin UI **core-specific** Documents 页签粘贴文档  

```  


# 更新数据 # 

重新post即可更新数据.  




# 删除数据 #

```  
$ sudo /usr/local/solr-6.6.0/bin/post -c gettingstarted -d "<delete><id>/usr/local/solr-6.6.0/docs/solr-core/org/apache/solr/security/AuthorizationResponse.html</id></delete>"


```  


# 搜索 #
` q=value ` 匹配全部的索引数据, ` q=field:value ` 匹配指定field的value 

```  
wt: 格式  
q: 查询条件 
fl: 显示字段  

http://192.168.1.101:8983/solr/demo/select?q=a

http://192.168.1.101:8983/solr/gettingstarted/select?q=video 
http://192.168.1.101:8983/solr/gettingstarted/select?q=video&fl=id,name,price
http://192.168.1.101:8983/solr/gettingstarted/select?q=price:479.95

http://192.168.1.101:8983/solr/gettingstarted/select?wt=json&indent=true&q=foundation

```  




# 组合搜索 #

` + `做前缀表示要求出现单词, ` - `做前缀表示不允许出现的单词.  
`+` URL encoding后为 `%2B`  

```  
# both one, three
$ curl "http://192.168.1.101:8983/solr/gettingstarted/select?wt=json&indent=true&q=%2Bone+%2Bthree"


# need "two", but don't need "one", +two -one
$ curl "http://192.168.1.101:8983/solr/gettingstarted/select?wt=json&indent=true&q=%2Btwo+-one"



```  

[More Solr Search options](https://cwiki.apache.org/confluence/display/solr/Searching)  




# Faceting #

Solr最受欢迎的特性是Faceting. Faceting允许搜索结果被整理为子集(bucket桶或categories类别), 提供每个子集的计算. 有几种Faceting类型: field values, numeric and date ranges, pivots(decision tree, 中心点, 决策树), and arbitrary query faceting(任意查询faceting).  


## Field facets ##

打开Faceting ` facet=true `  
通过 ' facet.field ' 参数指定字段到facet.  

```  

$ curl "http://192.168.1.101:8983/solr/gettingstarted/select?wt=json&indent=true&q=*:*&rows=0&facet=true&facet.field=manu_id_s"

```  


## Range facets ##

数值或日期, 相比丢弃数据, 经常适合划分facet count到一个范围.  

```  

$ curl "http://192.168.1.101:8983/solr/gettingstarted/select?q=*:*&wt=json&indent=on&rows=0&facet=true&facet.range=price&f.price.facet.range.start=0&f.price.facet.range.end=600&f.price.facet.range.gap=50&facet.range.other=after"


```  

 
## Pivot facets ##

"decison tree", 允许两个或多个字段嵌套成多种多样的可能的组合.  

```  
$ curl "http://192.168.1.101:8983/solr/gettingstarted/select/?q=*:*&rows=0&wt=json&indent=on&facet=on&facet.pivot=cat,inStock"


```  

 
## More faceting options ##

[The full scoop on Solr Facting](https://cwiki.apache.org/confluence/display/solr/Faceting)  


# Spatial #


[Spatial Search](https://cwiki.apache.org/confluence/display/solr/Spatial+Search)  




# 清除cleanup #

```  
$ sudo /usr/local/solr-6.6.0/bin/solr stop -all



rm -Rf example/cloud/

```  


















