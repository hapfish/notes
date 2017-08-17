
# centos7_install_elasticsearch.md  

https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html  

https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html   

https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/index.html  
https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/index.html  

# Java  

```  
$ java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)

$ sudo yum -y install wget

$ sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.1.rpm sha1sum elasticsearch-5.5.1.rpm

$ sudo rpm --install elasticsearch-5.5.1.rpm

$ sudo chkconfig --add elasticsearch

$ sudo systemctl start elasticsearch.service

$ sudo service elasticsearch status
● elasticsearch.service - Elasticsearch
   Loaded: loaded (/usr/lib/systemd/system/elasticsearch.service; disabled; vendor preset: disabled)
   Active: active (running) since Sat 2017-08-05 18:55:55 CST; 45s ago
   
$ sudo ls /var/log/elasticsearch
$ sudo tail -n 500 -f /var/log/elasticsearch/elasticsearch-20178-05.log
$ sudo tail -n 500 -f /var/log/elasticsearch/elasticsearch.log
$ sudo tail -n 500 -f /var/log/elasticsearch/elasticsearch_depration.log


$ sudo ls /etc/elasticsearch/
elasticsearch.yml  jvm.options  log4j2.properties  scripts
$ sudo cat /etc/elasticsearch/elasticsearch.yml
$ sudo cat /etc/elasticsearch/elasticsearch.yml
$ sudo cat /etc/elasticsearch/log4j2.properties


$ sudo vim /var/log/elasticsearch/elasticsearch.log
$ sudo vim /etc/elasticsearch/elasticsearch.yml
#network.host: 192.168.0.1
network.host: 0.0.0.0


http://192.168.1.107:9200/
   
```  

# API  

```  
$ curl http://127.0.0.1:9200
{
  "name" : "kBPaWLD",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "weEXrZLRTSKCgYziowGB0w",
  "version" : {
    "number" : "5.5.1",
    "build_hash" : "19c13d0",
    "build_date" : "2017-07-18T20:44:24.823Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.0"
  },
  "tagline" : "You Know, for Search"
}


```  

