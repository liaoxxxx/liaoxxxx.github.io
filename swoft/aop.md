##切面编程 （AOP）
###概念介绍
>AOP（Aspect-oriented programming）即 面向切面的程序设计，AOP 和 OOP（Object-oriented programming）面向对象程序设计一样都是一种程序设计思想，AOP 只是 OOP 的补充和延伸，可以更方便的对业务代码进行解耦，从而提高代码质量和增加代码的可重用性。后面的篇章会结合实际案例，教会大家理解并学会使用 AOP。

###场景
####我们就日志记录功能入手带领大家理解 AOP 切面编程的优势。首先假设我们有以下需求：

>1.需要对某个接口做调用记录，记录接口的调用详情（调用参数，返回结果等）。

>2.当调用接口出现异常时，做特殊处理，比如记录异常日志、邮件通知运维小伙伴。

>3.当不同的权限级别的管理员 访问管理后台时，需要分配，校验，权限节点，记录操作日志等


###传统 OOP 实现
####现在我们有一个订单服务：
````php
<?php declare(strict_types=1);

/**
 * 商品分类 业务逻辑
 */
class GoodsCategoryLogic
{
     /**
     * 编辑商品分类
     * @param array $post
     * @return bool
     */
    public  function edit(array $post ):bool {
      $Category = $this->editBuild($post);
      return $Category->save();
    }

}
````
####在传统的 OOP 思想下，我们要实现所需的功能，可以创建一个类继承 edit，然后重写 edit 方法，最后在所有使用 GoodsCategoryLogic 的地方替换成新建的类。示例：

````php
class GoodsCategoryExtLogic extends GoodsCategoryLogic
{
    /**
     * 编辑商品分类
     * @param array $post
     * @return bool
     */
    public  function edit(array $post ):bool {
        // TODO: 记录$post 的数据及时间
        try {
            $Category = parent::editBuild($post);
            // TODO: 处理父方法返回结果，根据返回结果进行相关处理
            return parent::save();
        }
        catch (HttpException $exception){
            // TODO: 如有异常，记录异常日志并发送邮件，然后继续将异常抛出
            CLog::info($exception->getMessage());
            return  false;
        } 
        finally {
            //TODO： 最终纪录到日至
        }
    }
}

````
####最后在我们调用 GoodsCategoryLogic 的地方，将 GoodsCategoryLogic 替换为 GoodsCategoryExtLogic：

```php
<?php declare(strict_types=1);

// $order = new OrderService(); // 注释掉旧业务调用
$order = new InheritOrderService();// 替换
$order->generateOrder('MacBook Pro', 1, 10000);
```

####至此，经过上方的调整后，满足了我们的业务需求。现在我们回顾一下，过程似乎非常繁琐，耦合严重，甚至污染了 edit 方法。如果项目中存在 100 处 OrderService 类的调用，我们就得找到这 100 个地方进行修改、替换，这就是 OOP 的思想。

####这时你或许会想到使用中间件、拦截器等类似的方法来解决，其实这些方法本质上也是基于 AOP 的设计思想而来的。前文已经提到，AOP 是基于 OOP 的补充和延伸。


###概念介绍


####Advice（通知）

>通知是织入到目标类连接点上的一段代码，例如上方的权限检查及记录日志。

####Aspect（切面）

>切面由通知和切点组成，通知明确了目的，而切点明确目的地。

####Introduction（引介）

>引介指向一个现有类增加方法或字段属性。引介还可以在不改变现有类代码的情况下实现新的接口。

####Joinpoint（连接点）

>程序执行的某个特定位置，将通知放置的地方。如方法调用前、方法调用后、返回、抛出异常等。允许使用通知的地方都可称为连接点。

####Pointcut（切点）

>切点指需织入目标的方法。假设一个目标对象（类）中拥有 10 个方法，需要在其中 3 个方法中织入通知，这 3 个方法称为切点。

####Proxy（代理）

>应用通知的对象，详细内容参见设计模式里面的代理模式。代理实现了切面的业务， Swoft 使用了 PHP-Parser 库实现 AOP。

####Target（目标对象）

>被通知的对象类。目标含有真正的业务逻辑，可被无感知的织入。

####Weaving（织入）

>将切面应用至目标对象以创建新代理对象的过程。



```php

<?php

namespace App\Aspect;
。。。。
use App\Http\Controller\Admin\GoodsController;

/**
* the test of aspcet
*
* @Aspect(order=1)
*
* @PointBean(
*     include={GoodsController::class},  //切入的类
*     exclude={}
* )

*/

class LogsPointAspect
{


 

    /**
     * @AfterReturning()
     * @param JoinPoint $joinPoint //切入点的变量
     * @return mixed
     */
    public function afterReturn(JoinPoint $joinPoint){  
        //$result = $joinPoint->getReturn();
        /**
         * @var Request $request
         */
        $request =$joinPoint->getArgs()[0];
        $header= $request->getHeaders();
        file_put_contents("/www/wwwroot/swoft-my/runtime/liao.html",print_r($header ,true),FILE_APPEND);
        CLog::info( "afterReturn");
        //var_dump(get_class( ));
        return $joinPoint->getReturn() ; //
    }
}

```
