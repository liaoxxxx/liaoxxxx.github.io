##各种错误的解决方案

### Laravel 更换内置服务器监听端口

##### 正常情况下，使用 php artisan serve 启动 Laravel 内置服务器，将会监听在 localhost 的 8000 端口上

```shell
$ php artisan serve
```

> Laravel development server started on http://localhost:8000

##### 假如我们希望更换监听的接口地址或端口号，可以使用 --host 参数与 --port 参数

````shell
$ php artisan serve --host=0.0.0.0 --port=8080
````

> Laravel development server started on http://0.0.0.0:8080


***********************

### 提交数据出现参数  's' 

> 如  url :   127.0.0.1:8000/admin/member/0?page=1&type=1  ，则参数  `s` = `/admin/member/0`  即 参数 `s`  =  uri.

> nginx.config  该 server{ } rewrite 语句块中:

```` config
location / {
   if (!-e $request_filename) {
   rewrite  ^(.*)$  /index.php?s=/$1  last;
   break;
    }
 }
````
> 更换为：

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```



*******************
### 更新laravel 5.5 到5.8  出现  (Undefined class constant 'HEADER_CLIENT_IP')

>   https://stackoverflow.com/questions/52984601/getting-error-while-updating-laravel-from-5-5-to-5-7-undefined-class-constant


#### 解决方案 



> 在 composer.json  从 `"fideloper/proxy": "~3.3",` 切换到版本 `"fideloper/proxy": "^4.0",`  , 运行 `composer updata`


```json
 "require": {
        "fideloper/proxy": "^4.0",             
        "laravel/framework": "5.8.*"
    }

```




