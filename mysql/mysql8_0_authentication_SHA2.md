##SHA2密码验证PHP错误

###搬运参考 https://www.cnblogs.com/cndavidwang/p/9357684.html

1.验证错误的提示：
```text
[2054] PDOException in Connection.php line 295
SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client
                $this->fetchType = $config['result_type'];
            }
            try {
                if (empty($config['dsn'])) {
                    $config['dsn'] = $this->parseDsn($config);
                }
                if ($config['debug']) {
                    $startTime = microtime(true);
                }
                $this->links[$linkNum] = new PDO($config['dsn'], $config['username'], $config['password'], $params);
                if ($config['debug']) {
                    // 记录数据库连接信息
                    Log::record('[ DB ] CONNECT:[ UseTime:' . number_format(microtime(true) - $startTime, 6) . 's ] ' . $config['dsn'], 'sql');
                }
            } catch (\PDOException $e) {
                if ($autoConnection) {
                    Log::record($e->getMessage(), 'error');
                    return $this->connect($autoConnection, $linkNum);
                } else {
```

> 即:服务器请求客户端未知的身份验证方法

###2.背景：docker 容器 mysql的镜像是 mysql8.0版本 默认使用了新的密码验证插件：caching_sha2_password，

而本地php环境是的mysqld 扩展的Loaded plugins 项没有包含 caching_sha2_password 插件，
````text
mysqlnd
mysqlnd	enabled
Version	mysqlnd 5.0.12-dev - 20150407 - $Id: 38fea24f2847fa7519001be390c98ae0acafe387 $
Compression	supported
core SSL	supported
extended SSL	supported
Command buffer size	4096
Read buffer size	32768
Read timeout	31536000
Collecting statistics	Yes
Collecting memory statistics	No
Tracing	n/a
Loaded plugins	mysqlnd,debug_trace,auth_plugin_mysql_native_password,auth_plugin_mysql_clear_password,auth_plugin_sha256_password
API Extensions	pdo_mysql,mysqli
````


###3.解决方案：
> 如果不能升级PHP，可以在MySQL 8中创建（或修改）使用caching_sha2_password插件的账户，使之使用mysql_native_password，这样先前版本的PHP就>可以连接使用了。

> 在CREATE USER时，使用IDENTIFIED WITH xxx_plugin BY 'password'，比如：

```text
CREATE USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```


> 使用ALTER USER修改已有账户的验证插件：

```text
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password   
```

> 或

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
```

> 采用前一种方式，账户的密码将被清除；BY子句将为账户设置新的密码; 'root'  即要修改的用户，'localhost'即要验证的ip地址，若需要开放所有ip地址都可以联接，可替换成 '%'

> /etc/my.cnf配置文件中，有一行：

```text
# default-authentication-plugin=mysql_native_password
```


> 请删除注释符号“#”并重新启动mysqld使之生效，此后创建的账户均默认使用mysql_native_password。

> 如果您完成MySQL Server的安装之后，在没有启动过mysqld服务的情况下修改/etc/my.cnf配置，那么启动mysqld之后创建的'root'@'localhost'账户也>是使用mysql_native_password插件的。