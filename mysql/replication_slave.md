###MySQL主从复制

1.mysql配置
````text
主机 127.0.0.1 ： 3306      apt install		root  root  8.0.17
从机 127.0.0.1 :  33060     docker 容器 	root  root  8.0.17
````

####2.1 docker 从机 允许外部访问  
````bash
	ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';   
````

####主机

####2.2#创建账户,赋予权限

```bash
#创建账
create user 'root'@'ip' identified by  'password'

#赋予权限，with grant option这个选项表示该用户可以将自己拥有的权限授权给别人
grant all privileges on *.* to 'root'@'ip' with grant option

#flush privileges 命令本质上的作用是将当前user和privilige表中的用户信息/权限设置从mysql库(MySQL数据库的内置库)中提取到内存里
flush privileges;
```

> mysql 8 设置允许远程连接 You are not allowed to create a user with GRANT [ https://blog.csdn.net/mxskymx/article/details/88765072 ]

2.3修改主库配置文件，开启 binlog ，并设置 server-id ，每次修改配置文件后都要重启 MySQL 服务才会生效

vi /etc/my.cnf
修改的文件内容如下：
```
[mysqld]
[ replication ]
log-bin=mysql-bin
server-id = 1
#要同步的数据库
binlog-do-db =spring
binlog-do-db =swoole_demo
```

2.4 查看： show master status;
````
mysql> show master status;
+------------------+----------+--------------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB       | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------------+------------------+-------------------+
| mysql-bin.000025 |      155 | spring,swoole_demo |                  |                   |
+------------------+----------+--------------------+------------------+-------------------+
1 row in set (0.00 sec)

````










```text
log-bin：同步的日志路径及文件名，一定注意这个目录要是 MySQL 有权限写入的（我这里是偷懒了，直接放在了下面那个datadir下面）。
binlog-do-db：要同步的数据库名，当从机连上主机后，只有这里配置的数据库才会被同步，其他的不会被同步。
server-id: MySQL 在主从环境下的唯一标志符，给个任意数字，注意不能和从机重复。
```

> 现象

> 在配置文件中想当然地配置成binlog_do_db=test,xx,jj，以为是三个库。结果无论什么操作都没有binlog产生

> 原因

> mysql内部将“test,xx,jj”当成一个数据库了，结果因为我们没有这个db，自然就啥binlog都没写入了。

> binlog_do_db=dbname   等号左右不能有空字符串 

> 处理方法正确的配置方法应该是这样

```ini
binlog_do_db=test

binlog_do_db=xx

binlog_do_db=jj
```




### 3.0从机配置

##### 3.1配置server-id

```
[mysqld]
server-id=2 #设置server-id，必须唯一
```




####3.2 

````text

mysql> show master status
    -> ;
+------------------+----------+--------------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB       | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------------+------------------+-------------------+
| mysql-bin.000025 |      155 | spring,swoole_demo |                  |                   |
+------------------+----------+--------------------+------------------+------------------
````

#####3.3运行
```mysql
CHANGE MASTER TO
        MASTER_HOST='182.92.172.80',
        MASTER_USER='rep1',
        MASTER_PASSWORD='slavepass',
        MASTER_PORT=3306,
        MASTER_LOG_FILE='mysql-bin.000003',
        MASTER_LOG_POS=73;
```

#### 3.4  重启 从机mysqld  服务  #重要


#### 3.5 show slave status;





````

mysql> show slave status \G
*************************** 1. row ***************************
               Slave_IO_State: 
                  Master_Host: 127.0.0.1
                  Master_User: root
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000025
          Read_Master_Log_Pos: 155
               Relay_Log_File: a0444ee5ce3a-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000025
             Slave_IO_Running: No
            Slave_SQL_Running: Yes
              Replicate_Do_DB: 
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
  Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 155
              Relay_Log_Space: 155
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 13117
                Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server ids; these ids must be different for replication to work (or the --replicate-same-server-id option must be used on slave but this does not always make sense; please check the manual before using it).
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
                  Master_UUID: 
             Master_Info_File: mysql.slave_master_info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
           Master_Retry_Count: 86400
                  Master_Bind: 
      Last_IO_Error_Timestamp: 200320 09:10:00
     Last_SQL_Error_Timestamp: 
               Master_SSL_Crl: 
           Master_SSL_Crlpath: 
           Retrieved_Gtid_Set: 
            Executed_Gtid_Set: 
                Auto_Position: 0
         Replicate_Rewrite_DB: 
                 Channel_Name: 
           Master_TLS_Version: 
       Master_public_key_path: 
        Get_master_public_key: 0
            Network_Namespace: 
````







