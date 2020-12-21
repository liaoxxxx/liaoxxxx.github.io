##各种错误的解决方案

###1.  $in needs an array , $in 操作符需要

>* 参照 `https://www.cnblogs.com/smallrookie/p/7203346.html`
>　在mongodb执行批量查询操作时，抛出异常 Exception : $in needs an array。  

```php
 
    $orderIds=[
        0=> 12345601,
        2=> 12345601
    ];   
   
    $filter = [
        'order_id' => [
            '$in' =>(array) $orderIds
        ],
     ];
```

>* 原因:  查询的 orderIds 虽然 是array  (int) key  => (object) item ,但是key 不是连贯排序的索引，  （mongodb的驱动 将其转换成 map ？）

>*  解决方案 ：使用 array_value() ,sort()  ，将其转换成 有序的连贯数组即可
````php
    $orderIds=[
        0=> 12345601,
        2=> 12345601
    ];  
    $orderIDs=array_value($orderIds);           
````

