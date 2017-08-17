# centos7_install_nginx.md  

http://nginx.org/  
http://nginx.org/en/docs/http/load_balancing.html


http://www.nginx.cn/  

http://nginx.org/en/linux_packages.html#stable  

# 安装Nginx  

```  
$ sudo ls /etc/yum.repos.d/

$ sudo vim /etc/yum.repos.d/nginx.repo
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/7/$basearch
gpgcheck=0
enabled=1

$ sudo yum search nginx
nginx.x86_64 : High performance web server

$ sudo yum -y install nginx

$ sudo service nginx status
$ sudo service nginx start
Redirecting to /bin/systemctl start  nginx.service


$ curl http://127.0.0.1
$ curl http://192.168.1.108


$ sudo ls /etc/nginx/
$ sudo cat /etc/nginx/nginx.conf



```  

# 打开防火墙  
```  
$ sudo firewall-cmd --state
$ sudo firewall-cmd --get-zones
work drop internal external trusted home dmz public block

$ sudo firewall-cmd --get-active-zones
public
  interfaces: enp0s3
  
$ sudo firewall-cmd --zone=public --list-ports

$ sudo firewall-cmd --zone=public --add-port=22/tcp --permanent
success
$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
success
$ sudo firewall-cmd --reload
success

$ sudo firewall-cmd --zone=public --list-ports
80/tcp 22/tcp



```  

# 负载均衡   

http://nginx.org/en/docs/http/load_balancing.html#nginx_load_balancing_methods  

使用Java Spring Boot服务, 启动三个端口. 
https://github.com/highill-practice/highill-practice-spring-boot/blob/master/highill-practice-spring-boot/markdown/spring_boot_document.md  

提供GET接口 http://127.0.0.1:10001/practice/normal?name=DemoName&info=GoodMessage  



```  

$ cd /etc/nginx/conf.d/

$ sudo vim default.conf
upstream java_load_balancing {
   server 127.0.0.1:10001;
   server 127.0.0.1:10002;
   server 127.0.0.1:10003;
}
server {
    listen       80;
    # server_name 127.0.0.1;
    # server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    # java load balance
    # location / {
    #    root   /usr/share/nginx/html;
    #    index  index.html index.htm;
    # }
    location / {
        proxy_pass http://java_load_balancing;
    }
}

$ sudo service nginx status
$ sudo service nginx restart


-- 错误 
2017/08/17 18:25:36 [crit] 4435#4435: *3 connect() to 127.0.0.1:10001 failed (13: Permission denied) while connecting to upstream, client: 127.0.0.1, server: 127.0.0.1, 
$ sudo setsebool -P httpd_can_network_connect 1


-- 测试  
$ curl "http://127.0.0.1/practice/normal?name=MyName&info=GoodMessage"
http://192.168.1.108/practice/normal?name=MyName&info=MyInfo


```  





