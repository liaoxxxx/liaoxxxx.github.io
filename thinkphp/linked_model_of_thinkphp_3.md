
### 模型   非 主键一对一关联 

#### 1.0  [搬运]https://www.kancloud.cn/manual/thinkphp/1782

#####1.1 主表模型 继承 RelationModel

#####1.2 自定义 属性 名 与 主表关联，默认该属性名 与附属表 的表名相同(无前缀)

#####1.3  设置 'mapping_type'  类型 ： self::HAS_ONE  | 'mapping_type'=>self::HAS_ONE,

#####1.4  'foreign_key'  为 主模型的表 的字段(映射的表的字段名称) ；'foreign_key'  为 附属模型的表 的字段(映射的表的字段名称) ；


#### 2.0 代码实例

````php
<?php
namespace Home\Model;

use Think\Model\RelationModel;

class OrderModel extends RelationModel {
    protected $_link = array(
        "list"=>array(          					//自定义 属性 名 与 主表关联
            'mapping_type'=>self::HAS_ONE,
            //'mapping_name'      =>  'list',
            'foreign_key'=>'order_id',  			//主表   关联字段
            'parent_key'  =>  'order_id',			//附属表 关联字段
        )
    );
}

````


####3.0 查询结果

```text
array(22) {
    ["id"] => string(4) "2929"
    ["apiorder_id"] => string(20) "HF202003050513660426"
    ["order_id"] => string(20) "15833995960010295362"
    ["hb_order_id"] => string(24) "12621186_1855_1583387196"
    ["order_price"] => string(6) "200.00"
    ["order_money"] => string(6) "200.00"
    ["service"] => string(5) "0.000"
    ["type"] => string(6) "alipay"
    ["user_id"] => string(4) "1003"
    ["polling_id"] => string(2) "16"
    ["polling_user"] => string(8) "hf100316"
    ["sub_time"] => string(10) "1583399783"
    ["applydate_time"] => string(10) "1583399603"
    ["com_time"] => string(3) "---"
    ["remark"] => NULL
    ["notice"] => string(1) "2"
    ["status"] => string(1) "2"
    ["user"] => array(11) {
      ["id"] => string(4) "1003"
      ["phone"] => string(11) "17607885885"
      ...
      ["time"] => string(10) "1583221549"
      ["status"] => string(1) "1"
    }
    ["polling"] => array(41) {
      ["id"] => string(2) "16"
      ["user_id"] => string(4) "1003"
      ["remark"] => string(1) "3"
      ["status"] => string(1) "1"
    }
    ["list"] => array(11) {
      ["id"] => string(2) "22"
      ["mobile_id"] => string(7) "hf10016"
      ["mark"] => string(16) "1583215349278_22"
      ["money"] => string(5) "50.00"
      ["orderno"] => string(23) "12621186_915_1583215349"
      ["qunid"] => string(9) "661710424"
      ["order_id"] => string(0) ""
      ["state"] => string(1) "0"
      ["create_time"] => string(10) "1583215376"
      ["update_time"] => NULL
    }
```



####4.0 实验证明 以上是错误的


