## DNS反解析错误   MySQL [Warning]: IP address 'xxxx' could not be resolved: Name or service not known
>*  https://www.cnblogs.com/digdeep/p/4906423.html


出现该错误的原因：

MySQL 数据库服务器没有配置 /etc/hosts，也没有DNS服务，导致mysqld线程解析IP对应的主机名时，解析失败。

解决方法：

使用--skip-name-resolve 参数禁用DNS的主机名解析功能，禁用该功能后，在MySQL授权表里面，你只能使用IP地址。

所以我们修改MySQL的my.cnf中的参数：

[mysqld]
skip_host_cache
skip-name-resolve=1
could not be resolved: Temporary failure in name resolution 的警告，可以使用 skip_host_cache 来搞定；
而 could not be resolved: Name or service not known 的警告，可以通过 skip_name_resolve=1 来搞定。禁止了域名解析，显然就不会出该警告了。
注意：skip_host_cache=1 会报错。直接 skip_host_cache 或者 skip-host-cache 就行了。

修改之后，需要重启。

注意：mysql中的权限表，就不能使用域名了，必须使用IP地址来设置。

 


 

--------------------------------分割线---------------------------------------

Mysql域名解析(转自：http://www.jb51.net/article/70893.htm)：

当一个新的客户端尝试跟mysqld创建连接时，mysqld产生一个新线程来处理这个请求。新线程会先检查请求建立连接的主机名是否在Mysql的主机名缓冲中，如果不在，线程会尝试去解析请求连接的主机名。

解析的逻辑如下：

a. Mysql线程通过gethostbyaddr()把获取的IP地址解析成主机名，然后通过gethostbyname()把获取的主机名解析成IP地址，保障主机名和IP地址对应关系的准确；

b. 如果操作系统支持使用安全进程的gethostbyaddr_r()和gethostbyname_r() 调用，Mysqld线程可以用它俩来优化主机名解析；

c. 如果操作系统不支持安全线程调用，Mysqld进程先做一个互斥锁，然后调用gethostbyaddr()和gethostbyname()解析主机 名。此时，在第一个进程释放掉主机名缓冲池的主机名之前，其它进程无法再次解析这个主机名; <-------MySQL手册里面在此处说的host name ，意思应该是指同一个IP地址和对应的第一个主机名关系。

在启动mysqld进程是，可以使用 --skip-name-resolve 参数禁用DNS的主机名解析功能，禁用该功能后，在MySQL授权表里面，你只能使用IP地址。

如果你所处环境的DNS非常慢 或者 有很多主机， 你可以通过禁用DNS解析功能--skip-name-resolve 或者 提高 HOST_CACHE_SIZE大小 来提升数据库的响应效率。

禁用主机名缓冲的发方法： 使用--skip-host-cache 参数； 刷新主机名缓冲区： 执行 flush hosts 或者执行mysqladmin flush-hosts；

 

=============================

启用 skip_name_resolve=1 之后，可能会有新的Warining:

[Warning] 'proxies_priv' entry '@ root@localhost.localdomain' ignored in --skip-name-resolve mode.

解决方法，删除表 mysq.proxies_priv 中值为 localhost.localdomain 的行

参见：http://blog.itpub.net/14184018/viewspace-1061224/