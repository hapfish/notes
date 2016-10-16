
# java_mock.md  


## EasyMock
EasyMock 是采用 MIT license 的一个开源项目，可以在 Sourceforge 上下载到。
http://sourceforge.net/projects/easymock/files/EasyMock/
 
## mockito
https://code.google.com/p/mockito/

 
http://www.mvnrepository.com/artifact/org.mockito/mockito-all/1.10.19

```  
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>1.10.19</version>
    <scope>test</scope>
</dependency>

```  

## PowerMock
这个工具是在EasyMock和Mockito上扩展出来的，目的是为了解决EasyMock和Mockito不能解决的问题，比如对static, final, private方法均不能mock  
http://www.mvnrepository.com/artifact/org.powermock/powermock-module-junit4/1.6.2  

```  
<dependency>
    <groupId>org.powermock</groupId>
    <artifactId>powermock-module-junit4</artifactId>
    <version>1.6.2</version>
</dependency>
```  



## Jmockit 

Jmockit功能和PowerMock类似，某些功能甚至更为强大，但个人感觉其代码的可读性并不强。 
http://www.mvnrepository.com/artifact/org.jmockit/jmockit/1.17  

```  
<dependency>
    <groupId>org.jmockit</groupId>
    <artifactId>jmockit</artifactId>
    <version>1.17</version>
</dependency>
 
```  


