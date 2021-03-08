## 致命错误：在construct方法 返回值 (非常愚蠢)




### 1.官方定义


####构造函数 

>* `__construct ( mixed ...$values = "" ) : void`

>* PHP 允许开发者在一个类中定义一个方法作为构造函数。具有构造函数的类会在每次创建新对象时先调用此方法，所以非常适合在使用对象之前做一些初始化工作。

>* 注意: 如果子类中定义了构造函数则不会隐式调用其父类的构造函数。要执行父类的构造函数，需要在子类的构造函数中调用 parent::__construct()。如果 子类没有定义构造函数则会如同一个普通的类方法一样从父类继承（假如没有被定义为 private 的话）。


### 2.错误的代码

>*  在控制器 初始化的时候 返回  `think\Response`

```php
<?php

namespace app\api\controller;

use common\basic\BaseController;
use think\facade\App;

class ApiBasic extends BaseController
{
    public   function __construct(App $app)
    {
       return app('json')->tokenFail('请在header参数 ，或请求参数传入 token');  //构造方法无法返回
     }
}

```

### 3. 错误分析：  在所有的 构造函数都是没有返回值，所以无法返回 响应对象


### 4.正确的方式 :  响应对象直接输出到客户端

```php
<?php

namespace app\api\controller;

use common\basic\BaseController;
use think\facade\App;

class ApiBasic extends BaseController
{
    public   function __construct(App $app)
    {
        app('json')->tokenFail('请在header参数 ，或请求参数传入 token')->send(); //直接响应输出
     }
}
```