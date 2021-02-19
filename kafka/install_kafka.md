

###安装    `作者：zane`  链接：https://zhuanlan.zhihu.com/p/55680941

>1.安装java 环境 
 ```
 sudo yum install java-11-openjdk -y
 ```


>2.下载解压 kafka 的binary文件

```
// 下载包
wget http://mirror.bit.edu.cn/apache/kafka/2.1.0/kafka_2.11-2.1.0.tgz
// 解压
tar -xzf kafka_2.11-2.1.0.tgz
// 重命名为kafka
mv kafka_2.11-2.1.0 kafka
```

>3.启动Zookeeper 和 kafka 

```
bin/zookeeper-server-start.sh config/zookeeper.properties 1>/dev/null  2>&1  &

bin/kafka-server-start.sh config/server.properties 1>/dev/null  2>&1  &
```

>4.监控面板 