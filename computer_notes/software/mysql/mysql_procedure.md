
# mysql_procedure.md  

# 创建数据库和数据表  

```  
# mysql -uroot -p

l> select version();
+------------+
| version()  |
+------------+
| 5.7.11-log |
+------------+
1 row in set (0.00 sec)


> show databases;

> create database note_demo default character set utf8;
Query OK, 1 row affected (0.00 sec)

> show create database note_demo;

> use note_demo;


-- 创建外网访问用户
> grant all privileges on *.* to note@'%' identified by 'GitHub2016!';
Query OK, 0 rows affected, 1 warning (0.04 sec)

> select host, user, authentication_string, password_expired, account_locked from mysql.user;


```  

# 创建测试表  

```  

-- 使用SQL Workbench  
-- 为了测试性能, 创建一个多列表  

drop table if exists procedure_demo;
create table procedure_demo (
 id bigint primary key not null auto_increment comment '主键',
 code varchar(128) comment '编码',
 info varchar(128) comment '信息',
 int_test_1 int comment '整数1',
 int_test_2 int comment '整数2',
 int_test_3 int comment '整数3',
 int_test_4 int comment '整数4',
 int_test_5 int comment '整数5',
 bigint_test_1 bigint comment '长整数1',
 bigint_test_2 bigint comment '长整数2',
 bigint_test_3 bigint comment '长整数3',
 bigint_test_4 bigint comment '长整数4',
 bigint_test_5 bigint comment '长整数5',
 varchar_test_1 varchar(64) comment '字符1',
 varchar_test_2 varchar(64) comment '字符2',
 varchar_test_3 varchar(64) comment '字符3',
 varchar_test_4 varchar(64) comment '字符4',
 varchar_test_5 varchar(64) comment '字符5',
 varchar_test_6 varchar(64) comment '字符6',
 varchar_test_7 varchar(64) comment '字符7',
 varchar_test_8 varchar(64) comment '字符8',
 varchar_test_9 varchar(64) comment '字符9',
 varchar_test_10 varchar(64) comment '字符10',
 varchar_test_11 varchar(64) comment '字符11',
 varchar_test_12 varchar(64) comment '字符12',
 varchar_test_13 varchar(64) comment '字符13',
 varchar_test_14 varchar(64) comment '字符14',
 varchar_test_15 varchar(64) comment '字符15',
 varchar_test_16 varchar(64) comment '字符16',
 varchar_test_17 varchar(64) comment '字符17',
 varchar_test_18 varchar(64) comment '字符18',
 varchar_test_19 varchar(64) comment '字符19',
 varchar_test_20 varchar(64) comment '字符20',
 varchar_test_21 varchar(64) comment '字符21',
 varchar_test_22 varchar(64) comment '字符22',
 varchar_test_23 varchar(64) comment '字符23',
 varchar_test_24 varchar(64) comment '字符24',
 varchar_test_25 varchar(64) comment '字符25',
 varchar_test_26 varchar(64) comment '字符26',
 varchar_test_27 varchar(64) comment '字符27',
 varchar_test_28 varchar(64) comment '字符28',
 varchar_test_29 varchar(64) comment '字符29',
 varchar_test_30 varchar(64) comment '字符30',
 index index_code (code)
 );

```  

# 创建存储过程  

```  

-- 删除存在的存储过程  

drop procedure if exists insert_procedure;

delimiter //

create procedure insert_procedure (in insertNum bigint) 
  begin
  declare insertSize bigint;
  
  set insertSize = 1;
  while insertSize <= insertNum do  
    insert into procedure_demo (code, info, 
      int_test_1, int_test_2, int_test_3, int_test_4, int_test_5, 
	  bigint_test_1, bigint_test_2, bigint_test_3, bigint_test_4, bigint_test_5, 
	  varchar_test_1, varchar_test_2, varchar_test_3, varchar_test_4, varchar_test_5, 
	  varchar_test_6, varchar_test_7, varchar_test_8, varchar_test_9, varchar_test_10,
	  varchar_test_11, varchar_test_12, varchar_test_13, varchar_test_14, varchar_test_15, 
	  varchar_test_16, varchar_test_17, varchar_test_18, varchar_test_19, varchar_test_20, 
	  varchar_test_21, varchar_test_22, varchar_test_23, varchar_test_24, varchar_test_25,
	  varchar_test_26, varchar_test_27, varchar_test_28, varchar_test_29, varchar_test_30
	  ) 
    values (1, 'info', 
	  insertSize, insertSize, insertSize, insertSize, insertSize,
	  insertSize * 1, insertSize * 2, insertSize * 3, insertSize * 4, insertSize * 5,
	  concat('test1_', insertSize), concat('test2_', insertSize), concat('test3_', insertSize), concat('test4_', insertSize), concat('test5_', insertSize), 
	  concat('test6_', insertSize), concat('test7_', insertSize), concat('test8_', insertSize), concat('test9_', insertSize), concat('test10_', insertSize),  
	  concat('test11_', insertSize), concat('test12_', insertSize), concat('test13_', insertSize), concat('test14_', insertSize), concat('test15_', insertSize), 
	  concat('test16_', insertSize), concat('test17_', insertSize), concat('test18_', insertSize), concat('test19_', insertSize), concat('test20_', insertSize), 
	  concat('test21_', insertSize), concat('test22_', insertSize), concat('test23_', insertSize), concat('test24_', insertSize), concat('test25_', insertSize), 
	  concat('test26_', insertSize), concat('test27_', insertSize), concat('test28_', insertSize), concat('test29_', insertSize), concat('test30_', insertSize)
	  );
	
    	
    set insertSize = insertSize + 1; 
     	
  end while;
  end //


delimiter ;



-- 调用存储过程 

call insert_procedure(10);


select * from procedure_demo order by id desc limit 20000;

-- mysql 5.7 function
http://dev.mysql.com/doc/refman/5.7/en/func-op-summary-ref.html


> call insert_procedure(150000);
Query OK, 1 row affected (3 min 32.72 sec)


> select count(*) from procedure_demo;




```  

# 导出测试数据  

```  
# date
Sun Oct 16 14:58:56 CST 2016

# mysqldump -h192.168.1.105 -unote -pGitHub2016!  --single-transaction --add-drop-table --complete-insert  note_demo procedure_demo > procedure_demo_20161016_1.sql
mysqldump: [Warning] Using a password on the command line interface can be insecure.



# date
Sun Oct 16 14:59:20 CST 2016




```  


