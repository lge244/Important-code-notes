### 多表联查

##### 根据顾客查询相对的所有订单
* HelloController.php 控制器下的文件
```PHP
namespace app\controllers;

use yii\web\Controller;
use app\model\Test;
use app\model\Customer;
use app\model\Order;

class HelloController extends Controller
{
    public function actionIndex()
    {
        //根据顾客查询他/她的订单的信息
        $customer = Customer::find()->where(['name' => 'zhangsan'])->one();

        //引用Customer文件下的getOrders方法
        $orders = $Customer->getOrders();

        //调用Order文件下的getCustomer方法
        $orders = $Order->getCustomer();
    }
}
```

* Customer.php  model层下的文件
```PHP
namespace app\models;

use yii\db\ActiveRecord;

class Customer extends ActiveRecord
{
    public function getOrders()
    {
        //查询customer表中的id和order表中的id相等的所有数据
        $orders = $this->hasMany(Order::className(), ['customer_id' => 'id'])->asArray()->all();
        return $orders;
    }
}
```
* hasMany 查询不止一条的数据

##### 根据一个订单查询顾客的信息

* Order.php
```PHP
namespace app\models;

use yii\db\ActiveRecord;

class Order extends ActiveRecord
{
    public function getCustomer()
    {
        //查询order表中的id和customer表中的id相等的一个数据
        $orders = $this->hasOne(Customer::className(), ['customer_id' => 'id'])->asArray()->all();
        return $orders;
    }
}
```
[多表查询参考](http://blog.sina.com.cn/s/blog_88a65c1b0101ixy7.html)
