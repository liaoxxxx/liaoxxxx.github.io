### ab压力测试工具

####1.安装

> 安装:
```
sudo apt-get install apache2-utils
```

> 测试安装完成:

```
 ab -V
```

####2.压力测试

> ab的命令参数比较多，我们经常使用的是-c和-n参数。

```
   ab -c 10 -n 100 http://127.0.0.1/index.php/　：  同时处理100个请求并运行10次index.php
```
>　　-c10表示并发用户数为10  

>　　-n100表示请求总数为100


####3. 错误:ab: invalid URL

```
ab -c 10 -n 100 http://127.0.0.1/index.php/   //务必保证 url参数 以  /  结尾
```
