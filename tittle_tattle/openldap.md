
# opneldap  

LDAP  Lightweight Directory Access Protocol  
LDAP RFC -> LDAPman RFC  


c - countryName  国家  
dc - domainComponent  域名  
o - organization  组织-公司  
ou - organization unit  组织单元-部门  

sn - user name  真实姓名  
cn - common name  常用名称  

dn - distinguished name  

l - location 地名,通产为城市名  
ST 州或省的名称  
street 街道名称  
uid 用户标识  

openldap教程  
http://www.kuqin.com/docs/openldap.html#id2809654  
http://www.kuqin.com/apache2_2/  
http://www.kuqin.com/docs/openldap.html  
http://www.kuqin.com/docs/  

opneldap 默认使用 Berkeley DB数据库  

http://www.jxplorer.org/downloads/index.html  
操作 add, delete, modify, modify dn  
认证 bind, unbind, abandon  

属性名是不区分大小写  
也就是说，objectclass 与 objectClass和 OBJECTCLASS 相同。  
许多人喜欢将每个单词的首字母大写，但是第一个单词除外，比如objectClass、homePhone 和 thisIsAReallyLongAttribute。  

hash 符号 (#)  

静态组和动态组 Static Group 和 Dynamic Group  
组Group  
静态组 Static Group 显示声明集合成员, 适合少量明确的成员集合.  
动态组 Dynamic Group 定义过滤条件, 匹配条件都是组成员  

受管角色 过滤器角色 嵌套角色 (Managed Role, Filtered Role, Nested Role)  
角色 Role, 条目的另一种集任意的集合形式, 与组不同在于, 给定一个成员条目,能立即获得它所属角色.  
Role适合多对一查询, 角色定义对父节点树下面目录有效  
受管角色 Managed Role 等价于Group中的静态组, 不同的是, Role不是把组信息添加到自身属性中,  
而且将自身的DN添加到组成员条目的nsroledn属性中  
过滤器角色 Filtered Role 它与动态组相似, 通过定义条目过滤器来确定组员  
嵌套角色 Nested Role 它是对角色定义的一种嵌套形式.可以嵌套其它的嵌套角色. 嵌套角色的成员,是其包含的所有角色成员的集合.  
嵌套角色通过包含从属其它树下的角色, 可以扩展其搜索的scope.  

服务类  
指针服务类 Pointer Cos   
间接服务类 Indirect Cos   
经典服务类 Classic Cos   



 