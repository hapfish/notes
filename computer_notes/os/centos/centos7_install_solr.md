
CentOS 安装 Solr

[Solr下载](http://mirror.bit.edu.cn/apache/lucene/solr/5.5.0/)  

[Solr安装文档](http://lucene.apache.org/solr/quickstart.html)  


# 安装Solr #

下载`solr-5.5.0.zip` 到 `/soft`目录.  

```  
$ cd /soft/
$ ls solr*
solr-5.5.0.zip


# Need unzip
$ sudo yum -y install zip unzip


$ sudo unzip -q solr-5.5.0.zip
$ sudo cp -R /soft/solr-5.5.0 /usr/local/

```  

# 启动Solr #

```  
$ sudo /usr/local/solr-5.5.0/bin/solr start -e cloud -noprompt

Welcome to the SolrCloud example!

Starting up 2 Solr nodes for your example SolrCloud cluster.

Creating Solr home directory /usr/local/solr-5.5.0/example/cloud/node1/solr
Cloning /usr/local/solr-5.5.0/example/cloud/node1 into
   /usr/local/solr-5.5.0/example/cloud/node2

Starting up Solr on port 8983 using command:
/usr/local/solr-5.5.0/bin/solr start -cloud -p 8983 -s "/usr/local/solr-5.5.0/example/cloud/node1/solr"

NOTE: Please install lsof as this script needs it to determine if Solr is listening on port 8983.
Started Solr server on port 8983 (pid=2270). Happy searching!


Starting up Solr on port 7574 using command:
/usr/local/solr-5.5.0/bin/solr start -cloud -p 7574 -s "/usr/local/solr-5.5.0/example/cloud/node2/solr" -z localhost:9983

NOTE: Please install lsof as this script needs it to determine if Solr is listening on port 7574.

Started Solr server on port 7574 (pid=2373). Happy searching!


Connecting to ZooKeeper at localhost:9983 ...
Uploading /usr/local/solr-5.5.0/server/solr/configsets/data_driven_schema_configs/conf for config gettingstarted to ZooKeeper at localhost:9983

Creating new collection 'gettingstarted' using command:
http://localhost:8983/solr/admin/collections?action=CREATE&name=gettingstarted&numShards=2&replicationFactor=2&maxShardsPerNode=2&collection.configName=gettingstarted

{
  "responseHeader":{
    "status":0,
    "QTime":12678},
  "success":{"":{
      "responseHeader":{
        "status":0,
        "QTime":12075},
      "core":"gettingstarted_shard1_replica1"}}}

Enabling auto soft-commits with maxTime 3 secs using the Config API

POSTing request to Config API: http://localhost:8983/solr/gettingstarted/config
{"set-property":{"updateHandler.autoSoftCommit.maxTime":"3000"}}
Successfully set-property updateHandler.autoSoftCommit.maxTime to 3000


SolrCloud example running, please visit: http://localhost:8983/solr

```  

Solr界面:   http://192.168.1.100:8983/  
Solr配置API:   http://192.168.1.100:8983/solr/gettingstarted/config  
搜索界面:  http://192.168.1.100:8983/solr/gettingstarted_shard2_replica1/browse?q=ok  



# 索引数据 #

```  
$ sudo /usr/local/solr-5.5.0/bin/post -c gettingstarted /usr/local/solr-5.5.0/docs/
Indexing directory /usr/local/solr-5.5.0/docs/solr-velocity/resources (0 files, depth=2)
3937 files indexed.
COMMITting Solr index changes to http://localhost:8983/solr/gettingstarted/update...
Time spent: 0:01:57.604



```  

# 索引XML #

```  
$ sudo /usr/local/solr-5.5.0/bin/post -c gettingstarted /usr/local/solr-5.5.0/example/exampledocs/*.xml
java -classpath /usr/local/solr-5.5.0/dist/solr-core-5.5.0.jar -Dauto=yes -Dc=gettingstarted -Ddata=files org.apache.solr.util.SimplePostTool /usr/local/solr-5.5.0/example/exampledocs/gb18030-example.xml /usr/local/solr-5.5.0/example/exampledocs/hd.xml /usr/local/solr-5.5.0/example/exampledocs/ipod_other.xml /usr/local/solr-5.5.0/example/exampledocs/ipod_video.xml /usr/local/solr-5.5.0/example/exampledocs/manufacturers.xml /usr/local/solr-5.5.0/example/exampledocs/mem.xml /usr/local/solr-5.5.0/example/exampledocs/money.xml /usr/local/solr-5.5.0/example/exampledocs/monitor2.xml /usr/local/solr-5.5.0/example/exampledocs/monitor.xml /usr/local/solr-5.5.0/example/exampledocs/mp500.xml /usr/local/solr-5.5.0/example/exampledocs/sd500.xml /usr/local/solr-5.5.0/example/exampledocs/solr.xml /usr/local/solr-5.5.0/example/exampledocs/utf8-example.xml /usr/local/solr-5.5.0/example/exampledocs/vidcard.xml
SimplePostTool version 5.0.0
Posting files to [base] url http://localhost:8983/solr/gettingstarted/update...
Entering auto mode. File endings considered are xml,json,jsonl,csv,pdf,doc,docx,ppt,pptx,xls,xlsx,odt,odp,ods,ott,otp,ots,rtf,htm,html,txt,log
POSTing file gb18030-example.xml (application/xml) to [base]
POSTing file hd.xml (application/xml) to [base]
POSTing file ipod_other.xml (application/xml) to [base]
POSTing file ipod_video.xml (application/xml) to [base]
POSTing file manufacturers.xml (application/xml) to [base]
POSTing file mem.xml (application/xml) to [base]
POSTing file money.xml (application/xml) to [base]
POSTing file monitor2.xml (application/xml) to [base]
POSTing file monitor.xml (application/xml) to [base]
POSTing file mp500.xml (application/xml) to [base]
POSTing file sd500.xml (application/xml) to [base]
POSTing file solr.xml (application/xml) to [base]
POSTing file utf8-example.xml (application/xml) to [base]
POSTing file vidcard.xml (application/xml) to [base]
14 files indexed.
COMMITting Solr index changes to http://localhost:8983/solr/gettingstarted/update...
Time spent: 0:00:07.462


```  


# 索引JSON #

```  
$ sudo /usr/local/solr-5.5.0/bin/post -c gettingstarted /usr/local/solr-5.5.0/example/exampledocs/books.json
java -classpath /usr/local/solr-5.5.0/dist/solr-core-5.5.0.jar -Dauto=yes -Dc=gettingstarted -Ddata=files org.apache.solr.util.SimplePostTool /usr/local/solr-5.5.0/example/exampledocs/books.json
SimplePostTool version 5.0.0
Posting files to [base] url http://localhost:8983/solr/gettingstarted/update...
Entering auto mode. File endings considered are xml,json,jsonl,csv,pdf,doc,docx,ppt,pptx,xls,xlsx,odt,odp,ods,ott,otp,ots,rtf,htm,html,txt,log
POSTing file books.json (application/json) to [base]/json/docs
1 files indexed.
COMMITting Solr index changes to http://localhost:8983/solr/gettingstarted/update...
Time spent: 0:05:01.633


```  


# 索引CSV(Comma/Column Separated Values) #

```  
$ sudo /usr/local/solr-5.5.0/bin/post -c gettingstarted /usr/local/solr-5.5.0/example/exampledocs/books.csv
java -classpath /usr/local/solr-5.5.0/dist/solr-core-5.5.0.jar -Dauto=yes -Dc=gettingstarted -Ddata=files org.apache.solr.util.SimplePostTool /usr/local/solr-5.5.0/example/exampledocs/books.csv
SimplePostTool version 5.0.0
Posting files to [base] url http://localhost:8983/solr/gettingstarted/update...
Entering auto mode. File endings considered are xml,json,jsonl,csv,pdf,doc,docx,ppt,pptx,xls,xlsx,odt,odp,ods,ott,otp,ots,rtf,htm,html,txt,log
POSTing file books.csv (text/csv) to [base]
1 files indexed.
COMMITting Solr index changes to http://localhost:8983/solr/gettingstarted/update...
Time spent: 0:00:00.578


```  

# 其它索引技术 #

**DIH (Data Import Handler)**  

**SolrJ** 或其它Solr客户端  

Admin UI **core-specific** Documents 页签粘贴文档  


# 更新数据 # 

重新post即可更新数据.  




# 删除数据 #

```  
$ sudo /usr/local/solr-5.5.0/bin/post -c gettingstarted -d "<delete><id>/usr/local/solr-5.5.0/docs/solr-core/org/apache/solr/security/AuthorizationResponse.html</id></delete>"


java -classpath /usr/local/solr-5.5.0/dist/solr-core-5.5.0.jar -Dauto=yes -Dc=gettingstarted -Ddata=args org.apache.solr.util.SimplePostTool <delete><id>/usr/local/solr-5.5.0/docs/solr-core/org/apache/solr/security/AuthorizationResponse.html</id></delete>
SimplePostTool version 5.0.0
POSTing args to http://localhost:8983/solr/gettingstarted/update...
COMMITting Solr index changes to http://localhost:8983/solr/gettingstarted/update...
Time spent: 0:00:00.138


```  


# 搜索 #

```  

$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?q=*%3A*&wt=json&indent=true"
{
  "responseHeader":{
    "status":0,
    "QTime":21,
    "params":{
      "q":"*:*",
      "indent":"true",
      "wt":"json"}},
  "response":{"numFound":3983,"start":0,"maxScore":1.0,"docs":[
  
  ]
}}


  
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?q=*:*&wt=json&indent=true"

{
  "responseHeader":{
    "status":0,
    "QTime":26,
    "params":{
      "q":"*:*",
      "indent":"true",
      "wt":"json"}},
  "response":{"numFound":3983,"start":0,"maxScore":1.0,"docs":[

   ]
}}

```  


搜索单词"foundation"  

` $ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=foundation" `  


fl参数支持使用逗号分隔过滤字段  
```  
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=foundation&fl=id"
{
  "responseHeader":{
    "status":0,
    "QTime":7,
    "params":{
      "q":"foundation",
      "indent":"true",
      "fl":"id",
      "wt":"json"}},
  "response":{"numFound":3772,"start":0,"maxScore":0.37132,"docs":[
      {
        "id":"0553293354"},
      {
        "id":"UTF8TEST"},
      {
        "id":"SOLR1000"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-analysis-extras/org/apache/solr/schema/package-use.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-analytics/deprecated-list.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-clustering/deprecated-list.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-core/org/apache/solr/index/hdfs/package-use.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-core/org/apache/solr/logging/jul/package-use.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-core/org/apache/solr/spelling/suggest/jaspell/package-use.html"},
      {
        "id":"/usr/local/solr-5.5.0/docs/solr-dataimporthandler-extras/deprecated-list.html"}]
  }}
  
  
```  

` q=value ` 匹配全部的索引数据, ` q=field:value ` 匹配指定field的value  

```  

$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=name:foundation"
{
  "responseHeader":{
    "status":0,
    "QTime":10,
    "params":{
      "q":"name:foundation",
      "indent":"true",
      "wt":"json"}},
  "response":{"numFound":0,"start":0,"maxScore":0.0,"docs":[]
  }}



$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=price:0"
{
  "responseHeader":{
    "status":0,
    "QTime":25,
    "params":{
      "q":"price:0",
      "indent":"true",
      "wt":"json"}},
  "response":{"numFound":3,"start":0,"maxScore":7.889082,"docs":[
]

}}
  
  
```  



# 短语搜索 #

```  
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=\"CAS+latency\""

{
  "responseHeader":{
    "status":0,
    "QTime":198,
    "params":{
      "q":"\"CAS latency\"",
      "indent":"true",
      "wt":"json"}},
  "response":{"numFound":3,"start":0,"maxScore":1.7483006,"docs":[
  ]
}}

```  

# 组合搜索 #

` + `做前缀表示要求出现单词, ` - `做前缀表示不允许出现的单词.  
`+` URL encoding后为 `%2B`  

```  
# both one, three
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=%2Bone+%2Bthree"


# need "two", but don't need "one", +two -one
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=%2Btwo+-one"



```  

[More Solr Search options](https://cwiki.apache.org/confluence/display/solr/Searching)  




# Faceting #

Solr最受欢迎的特性是Faceting. Faceting允许搜索结果被整理为子集(bucket桶或categories类别), 提供每个子集的计算. 有几种Faceting类型: field values, numeric and date ranges, pivots(decision tree, 中心点, 决策树), and arbitrary query faceting(任意查询faceting).  


## Field facets ##

打开Faceting ` facet=true `  
通过 ' facet.field ' 参数指定字段到facet.  

```  

$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?wt=json&indent=true&q=*:*&rows=0&facet=true&facet.field=manu_id_s"

{
  "responseHeader":{
    "status":0,
    "QTime":15,
    "params":{
      "q":"*:*",
      "facet.field":"manu_id_s",
      "indent":"true",
      "rows":"0",
      "wt":"json",
      "facet":"true"}},
  "response":{"numFound":3982,"start":0,"maxScore":1.0,"docs":[]
  },
  "facet_counts":{
    "facet_queries":{},
    "facet_fields":{
      "manu_id_s":[
        "corsair",3,
        "belkin",2,
        "canon",2,
        "apple",1,
        "asus",1,
        "ati",1,
        "boa",1,
        "dell",1,
        "eu",1,
        "maxtor",1,
        "nor",1,
        "samsung",1,
        "uk",1,
        "viewsonic",1]},
    "facet_dates":{},
    "facet_ranges":{},
    "facet_intervals":{},
    "facet_heatmaps":{}}}
	

	
```  


## Range facets ##

数值或日期, 相比丢弃数据, 经常适合划分facet count到一个范围.  

```  

$ curl "http://192.168.1.100:8983/solr/gettingstarted/select?q=*:*&wt=json&indent=on&rows=0&facet=true&facet.range=price&f.price.facet.range.start=0&f.price.facet.range.end=600&f.price.facet.range.gap=50&facet.range.other=after"
{
  "responseHeader":{
    "status":0,
    "QTime":36,
    "params":{
      "facet.range":"price",
      "q":"*:*",
      "f.price.facet.range.start":"0",
      "facet.range.other":"after",
      "indent":"on",
      "f.price.facet.range.gap":"50",
      "rows":"0",
      "wt":"json",
      "facet":"true",
      "f.price.facet.range.end":"600"}},
  "response":{"numFound":3982,"start":0,"maxScore":1.0,"docs":[]
  },
  "facet_counts":{
    "facet_queries":{},
    "facet_fields":{},
    "facet_dates":{},
    "facet_ranges":{
      "price":{
        "counts":[
          "0.0",19,
          "50.0",2,
          "100.0",0,
          "150.0",2,
          "200.0",0,
          "250.0",1,
          "300.0",1,
          "350.0",2,
          "400.0",0,
          "450.0",1,
          "500.0",0,
          "550.0",0],
        "gap":50.0,
        "after":2,
        "start":0.0,
        "end":600.0}},
    "facet_intervals":{},
    "facet_heatmaps":{}}}



```  

 
## Pivot facets ##

"decison tree", 允许两个或多个字段嵌套成多种多样的可能的组合.  

```  
$ curl "http://192.168.1.100:8983/solr/gettingstarted/select/?q=*:*&rows=0&wt=json&indent=on&facet=on&facet.pivot=cat,inStock"

{
  "responseHeader":{
    "status":0,
    "QTime":29,
    "params":{
      "q":"*:*",
      "indent":"on",
      "facet.pivot":"cat,inStock",
      "rows":"0",
      "wt":"json",
      "facet":"on"}},
  "response":{"numFound":3982,"start":0,"maxScore":1.0,"docs":[]
  },
  "facet_counts":{
    "facet_queries":{},
    "facet_fields":{},
    "facet_dates":{},
    "facet_ranges":{},
    "facet_intervals":{},
    "facet_heatmaps":{},
    "facet_pivot":{
      "cat,inStock":[{
          "field":"cat",
          "value":"book",
          "count":14,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":12},
            {
              "field":"inStock",
              "value":false,
              "count":2}]},
        {
          "field":"cat",
          "value":"electronics",
          "count":12,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":8},
            {
              "field":"inStock",
              "value":false,
              "count":4}]},
        {
          "field":"cat",
          "value":"currency",
          "count":4,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":4}]},
        {
          "field":"cat",
          "value":"memory",
          "count":3,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":3}]},
        {
          "field":"cat",
          "value":"paperback",
          "count":3,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":3}]},
        {
          "field":"cat",
          "value":"connector",
          "count":2,
          "pivot":[{
              "field":"inStock",
              "value":false,
              "count":2}]},
        {
          "field":"cat",
          "value":"graphics card",
          "count":2,
          "pivot":[{
              "field":"inStock",
              "value":false,
              "count":2}]},
        {
          "field":"cat",
          "value":"hard drive",
          "count":2,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":2}]},
        {
          "field":"cat",
          "value":"search",
          "count":2,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":2}]},
        {
          "field":"cat",
          "value":"software",
          "count":2,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":2}]},
        {
          "field":"cat",
          "value":"camera",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"copier",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"electronics and computer1",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"electronics and stuff2",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"hardcover",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"multifunction printer",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"music",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"printer",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]},
        {
          "field":"cat",
          "value":"scanner",
          "count":1,
          "pivot":[{
              "field":"inStock",
              "value":true,
              "count":1}]}]}}}



```  

 
## More faceting options ##

[The full scoop on Solr Facting](https://cwiki.apache.org/confluence/display/solr/Faceting)  


# Spatial #


[Spatial Search](https://cwiki.apache.org/confluence/display/solr/Spatial+Search)  



# 总结 Wrapping up #

```  
date ;
bin/solr start -e cloud -noprompt ;
  open http://localhost:8983/solr ;
  bin/post -c gettingstarted docs/ ;
  open http://localhost:8983/solr/gettingstarted/browse ;
  bin/post -c gettingstarted example/exampledocs/*.xml ;
  bin/post -c gettingstarted example/exampledocs/books.json ;
  bin/post -c gettingstarted example/exampledocs/books.csv ;
  bin/post -c gettingstarted -d "<delete><id>SP2514N</id></delete>" ;
  bin/solr healthcheck -c gettingstarted ;
date ;

```  


# 清除cleanup #

```  
$ sudo /usr/local/solr-5.5.0/bin/solr stop -all
Sending stop command to Solr running on port 8983 ... waiting 5 seconds to allow Jetty process 3995 to stop gracefully.
Sending stop command to Solr running on port 7574 ... waiting 5 seconds to allow Jetty process 4111 to stop gracefully.
Solr process 4111 is still running; forcefully killing it now.
Killed process 4111


rm -Rf example/cloud/

```  


















