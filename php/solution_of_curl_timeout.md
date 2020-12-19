###  处理curl超时问题 

#####curl调用一个接口,在代码没有后任何设置的情况下,本地环境是10秒，最后拿到的数据是null,

> 1. 这种情况肯定不要针对处理, 代码内部的逻辑是redis队列， 所以对数据进行处理下，重新入队就好了;
> 2.  但是也是对curl 超时进行设置，将它控制起来

####分析


#####PHP官网的解释是主要是两个选项,而且没有默认值

> 1. CURLOPT_CONNECTTIMEOUT  在尝试连接时等待的秒数。设置为0，则无限等待。CURLOPT_CONNECTTIMEOUT 用来告诉 PHP 在成功连接服务器前等待多久（连接成功之后就会开始缓冲输出），这个参数是为了应对目标服务器的过载，下线，或者崩溃等可能状况

> 2. CURLOPT_TIMEOUT 允许 cURL 函数执行的最长秒数。CURLOPT_TIMEOUT 用来告诉成功 PHP 从服务器接收缓冲完成前需要等待多长时间，如果目标是个巨大的文件，生成内容速度过慢或者链路速度过慢，这个参数就会很有用



####解决

```php
        $curl = curl_init();
        //在http 请求头加入 gzip压缩
        curl_setopt($curl, CURLOPT_HTTPHEADER, array('Accept-Encoding:gzip'));
        //curl返回的结果，采用gzip解压
        curl_setopt($curl, CURLOPT_ENCODING, "gzip");
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_HEADER, false);
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        // 在尝试连接时等待的秒数
        curl_setopt($curl, CURLOPT_CONNECTTIMEOUT , 3);
        // 最大执行时间
        curl_setopt($curl, CURLOPT_TIMEOUT, 5);

        $gray_info = curl_exec($curl);

        // curl debug
        if (curl_errno($curl)) {
            // ＴＯ DO Something
        }
        
```



#### Curl的毫秒超时的一个"Bug"  搬运 https://www.laruence.com/2014/01/21/2939.html

> 最近我们的服务在升级php使用的libcurl, 期望新版本的libcurl支持毫秒级的超时, 从而可以更加精细的控制后端的接口超时, 从而提高整体响应时间.
但是, 我们却发现, 在我们的CentOS服务器上, 当你设置了小于1000ms的超时以后, curl不会发起任何请求, 而直接返回超时错误(Timeout reached 28).
原来, 这里面有一个坑, CURL默认的, 在Linux系统上, 如果使用了系统标准的DNS解析, 则会使用SIGALARM来提供控制域名解析超时的功能, 但是SIGALARM不支持小于1s的超时, 于是在libcurl 7.28.1的代码中(注意中文注释行):

````php
int Curl_resolv_timeout(struct connectdata *conn,
                        const char *hostname,
                        int port,
                        struct Curl_dns_entry **entry,
                        long timeoutms)
{
.......
.......
#ifdef USE_ALARM_TIMEOUT
  if(data->set.no_signal)
    /* Ignore the timeout when signals are disabled */
    timeout = 0;
  else
    timeout = timeoutms;
  if(!timeout)
    /* USE_ALARM_TIMEOUT defined, but no timeout actually requested */
    return Curl_resolv(conn, hostname, port, entry);
  if(timeout < 1000) //如果小于1000, 直接超时返回
    /* The alarm() function only provides integer second resolution, so if
       we want to wait less than one second we must bail out already now. */
    return CURLRESOLV_TIMEDOUT;
  ....
  ....
````  

> 可见, 当你的超时时间小于1000ms的时候, name解析会直接返回CURLRESOLV_TIMEOUT, 最后会导致CURLE_OPERATION_TIMEDOUT, 然后就Error, Timeout reached了...
这....太坑爹了吧? 难道说, 我们就不能使用毫秒超时么? 那你提供这功能干啥?
还是看代码, 还是刚才那段代码, 注意这个(中文注释行):

````php
#ifdef USE_ALARM_TIMEOUT
  if(data->set.no_signal)  //注意这行
    /* Ignore the timeout when signals are disabled */
    timeout = 0;
  else
    timeout = timeoutms;
  if(!timeout)
    /* USE_ALARM_TIMEOUT defined, but no timeout actually requested */
    return Curl_resolv(conn, hostname, port, entry);
  if(timeout < 1000)
    /* The alarm() function only provides integer second resolution, so if
       we want to wait less than one second we must bail out already now. */
    return CURLRESOLV_TIMEDOUT;
````

> 看起来, 只要set.no_signal 这个东西为1, 就可以绕过了... 那这个玩意是啥呢?
这就简单了, grep一下代码, 发现:

```php
  case CURLOPT_NOSIGNAL:
    /*
     * The application asks not to set any signal() or alarm() handlers,
     * even when using a timeout.
     */
    data->set.no_signal = (0 != va_arg(param, long))?TRUE:FALSE;
    break;
```

哈哈, 原来是这货:

````php
<?php
   curl_setopt($ch, CURLOPT_NOSIGNAL, 1);
?>
````

> 加上这个OPT以后, 一切终于正常了!
后记:
这样一来, 就会有一个隐患, 那就是DNS解析将不受超时限制了, 这在于公司内部来说, 一般没什么问题, 但是万一DNS服务器hang住了, 那就可能会造成应用超时.
那么还有其他办法么?
有, 那就是Mike提醒的, 我们可以让libcurl使用c-ares(C library for asynchronous DNS requests)来做名字解析. 具体的可以在config curl的时候:

```
./configure --enable-ares[=PATH]
```

> 这样就可以不用设置NOSIGNAL了 🙂
PS, 为什么冠以"Bug", 我只是好奇, 他们为什么不用setitimer?
参考: http://stackoverflow.com/questions/7987584/curl-timeout-less-than-1000ms-always-fails

