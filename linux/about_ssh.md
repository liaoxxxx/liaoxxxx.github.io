### 用SSH客户端连接linux服务器时,经常会出现与服务器会话连接中断现象,造成这个问题的原因便是SSH服务有自己独特的会话连接机制。
解决方案:

##### 1、设置服务器向SSH客户端连接会话发送频率和时间


> vim /etc/ssh/sshd_config,添加如下两行
 
```
ClientAliveInterval 60
ClientAliveCountMax 86400
```

> 注:ClientAliveInterval选项定义了每隔多少秒给SSH客户端发送一次信号;ClientAliveCountMax选项定义了超过多少秒后断开与ssh客户端连接

##### 2、重新启动系统SSH服务

```
service sshd restart
```