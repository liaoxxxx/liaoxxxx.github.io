###一些常见的mysql错误及解决方案




####2020-02-18
####1。SQLSTATE[HY000]: General error: 1364 Field 'uid' doesn't have a default value   即：一般错误:1364字段“uid”没有默认值

#####原因：

> 1。例子 数据表中的 uid 字段 设置为 notnull,但是没有设置默认值，

> 2。插的入数据 uid 字段没有为其赋值；

> 3。数据库的  sql-mode模式 使用了严格模式


#####解决方案：
> 一 ：直接把sql-mode模式改变下，如下：   （不推荐）

`````text	
sql-mode=NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
`````

> 2.为uid字段 设置默认值；


> 3。严格检查插入的数据，保证 uid 字段的数据不为空






*****************************************

####2020-02-26 

参照 https://stackoverflow.com/questions/8062496/how-to-change-max-allowed-packet-size

#### mysql 服务环境变量 max_allowed_packet 初始值 过小

> 1.启动spring boot  报错

`````text
2020-02-26 20:24:52.970 ERROR 4012 --- [           main] o.h.engine.jdbc.spi.SqlExceptionHelper   : Packet for query is too large (5,287 > 1,024). You can change this value on the server by setting the 'max_allowed_packet' variable.
`````

> 2.解决方案 ：第一步先确认 环境变量的值的大小  $mysql: show variables like '%max_allowed_packet%';

> 3. 编辑 /etc/mysql/my.cnf 文件  , max_allowed_packet变量的值根据实际情况修改， 新增或修改 行：max_allowed_packet =10M   



**************************************
####2020-03-24   解决mysql登录错误：'Access denied for user 'root'@'localhost'
###1.跳过权限登录mysql

停止mysql服务~$ sudo service mysql stop

以安全模式启动MySQL~$ sudo mysqld_safe --skip-grant-tables &




