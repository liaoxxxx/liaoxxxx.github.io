### 使用 laravel-s扩展 (swoole)

#### 环境：

> 1. `laravel 5.8.38`

> 2. `php 7.4.4`

> 3. `swoole 4.4` 

> 4. `Intel Xeon(r) Gold 6148 1 Core 1T @2.4Ghz `

> 5. `Mysql 8.0.17 docker`


#### 2. 注册 laravel-s扩展


> 注册到服务  `/config/app.php`, 

````
	'providers' => [
    	//...
    	Hhxsv5\LaravelS\Illuminate\LaravelSServiceProvider::class,
	],
````


> 创建 配置文件  `/config/laravels.php` 和 命令行工具   `/bin`

````
 $ php artisan laravels publish
````

> 运行提示

```
root@VM-0-9-ubuntu:/www/wwwroot/live# php artisan laravels publish
Copied file [/www/wwwroot/live/vendor/hhxsv5/laravel-s/config/laravels.php] To [/www/wwwroot/live/config/laravels.php]
Linked file [/www/wwwroot/live/vendor/hhxsv5/laravel-s/bin/laravels] To [/www/wwwroot/live/bin/laravels]
Linked file [/www/wwwroot/live/vendor/hhxsv5/laravel-s/bin/fswatch] To [/www/wwwroot/live/bin/fswatch]
Linked file [/www/wwwroot/live/vendor/hhxsv5/laravel-s/bin/inotify] To [/www/wwwroot/live/bin/inotify]
```

