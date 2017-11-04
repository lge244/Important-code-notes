## 属性（Property）

#### 成员变量和属性的区别与联系在于 :
 * 成员变量是一个“内”概念，反映的是类的结构构成。属性是一个“外”概念，反映的是类的逻辑意义。
 * 成员变量没有读写权限控制，而属性可以指定为只读或只写，或可读可写。
 * 成员变量不对读出作任何后处理，不对写入作任何预处理，而属性则可以。
 * public成员变量可以视为一个可读可写、没有任何预处理或后处理的属性。 而private成员变量由于外部不可见，与属性“外”的特性不相符，所以不能视为属性。
 * 虽然大多数情况下，属性会由某个或某些成员变量来表示，但属性与成员变量没有必然的对应关系， 比如与非门的 output 属性，就没有一个所谓的 $output 成员变量与之对应。

[原文参考](http://www.digpage.com/property.html#id2)

**在Yii中，由 yii\base\Object 提供了对属性的支持，因此，如果要使你的类支持属性， 必须继承自 yii\base\Object 。Yii中属性是通过PHP的魔法函数 __get() __set() 来产生作用的。 下面的代码是 yii\base\Object 类对于 __get() 和 __set() 的定义:**

```PHP

public
function __get($name) //这里的$name 是属性名
{
    $getter = 'get' . $name; //这里的$getter 是函数的函数名
    if (method_exists($this, $getter)) {// 调用了getter函数
        return $this->$getter();
    } elseif (method_exists($this, 'set' . $name)) {
        throw new InvalidCallException('Getting write-only property: '
            . get_class($this) . '::' . $name);
    } else {
        throw new UnknownPropertyException('Getting unknown property: '
            . get_class($this) . '::' . $name);
    }

}

public
function __set($name, $value) // $name是属性名，$value是拟写入的属性值
{
    $setter = 'set' . $name;             // setter函数的函数名
    if (method_exists($this, $setter)) {
        $this->$setter($value);          // 调用setter函数
    } elseif (method_exists($this, 'get' . $name)) {
        throw new InvalidCallException('Setting read-only property: ' .
            get_class($this) . '::' . $name);
    } else {
        throw new UnknownPropertyException('Setting unknown property: '
            . get_class($this) . '::' . $name);
    }
}
```
### 实现属性的步骤

在读取和写入对象的一个不存在的成员变量时，__get() __set() 会被自动调用。 Yii正是利用这点，提供对属性的支持的。从上面的代码中，可以看出，如果访问一个对象的某个属性， Yii会调用名为 get属性名() 的函数。如， SomeObject->Foo ， 会自动调用 SomeObject->getFoo() 。如果修改某一属性，会调用相应的setter函数。 如， SomeObject->Foo = $someValue ，会自动调用 SomeObject->setFoo($someValue)

* 因此，要实现属性，通常有三个步骤：
  - 继承自 yii\base\Object 。
  - 声明一个用于保存该属性的私有成员变量。
  - 提供getter或setter函数，或两者都提供，用于访问、修改上面提到的私有成员变量。 如果只提供了getter，那么该属性为只读属性，只提供了setter，则为只写。

如下的Post类，实现了可读可写的属性title:
```PHP
class Post extends yii\base\Object    // 第一步：继承自 yii\base\Object
{
    private $_title;                 // 第二步：声明一个私有成员变量

    public function getTitle()       // 第三步：提供getter和setter
    {
        return $this->_title;
    }

    public function setTitle($value)
    {
        $this->_title = trim($value);
    }
}
```
从理论上来讲，将 private $_title 写成 public $title ，也是可以实现对 $post->title 的读写的。但这不是好的习惯，理由如下：

* 失去了类的封装性， 一般而言，成员变量对外不可见是比较好的编程习惯。 从这里你也许没看出来，但是假如有一天，你不想让用户修改标题了，你怎么改？ 怎么确保代码中没有直接修改标题？ 如果提供了setter，只要把setter删掉，那么一旦有没清理干净的对标题的写入，就会抛出异常。 而使用 public $title 的方法的话，你改成 private $title 可以排查写入的异常，但是读取的也被禁止了

[原文参考](http://www.digpage.com/property.html#id2)

### Object的其他与属性相关的方法

*除了 __get() __set() 之外， yii\base\Object 还提供了以下方法便于使用属性：*


* __isset() 用于测试属性值是否不为 null ，在 isset($object->property) 时被自动调用。 注意该属性要有相应的getter。

* __unset() 用于将属性值设为 null ，在 unset($object->property) 时被自动调用。 注意该属性要有相应的setter。

* hasProperty() 用于测试是否有某个属性。即，定义了getter或setter。 如果 hasProperty() 的参数 $checkVars = true （默认为true）， 那么只要具有同名的成员变量也认为具有该属性，如前面提到的 public $title 。

* canGetProperty() 测试一个属性是否可读，参数 $checkVars 的意义同上。只要定义了getter，属性即可读。 同时，如果 $checkVars 为 true 。那么只要类定义了成员变量，不管是public， private 还是 protected， 都认为是可读。

* canSetProperty() 测试一个属性是否可写，参数 $checkVars 的意义同上。只要定义了setter，属性即可写。 同时，在 $checkVars 为 ture 。那么只要类定义了成员变量，不管是public， private 还是 protected， 都认为是可写。

[原文参考](http://www.digpage.com/property.html#id2)

### Object和Component

* yii\base\Component 继承自 yii\base\Object ，因此，他也具有属性等基本功能

**Object比较轻量级些，通过getter和setter定义了类的属性（property）。 Component派生自Object，并支持事件（event）和行为（behavior）。因此，Component类具有三个重要的特性**

* 属性（property）
* 事件（event）
* 行为（behavior）


### Object的配置方法

Yii提供了一个统一的配置对象的方式。这一方式贯穿整个Yii。Application对象的配置就是这种配置方式的体现:

```PHP
$config = yii\helpers\ArrayHelper::merge(
    require(__DIR__ . '/../../common/config/main.php'),
    require(__DIR__ . '/../../common/config/main-local.php'),
    require(__DIR__ . '/../config/main.php'),
    require(__DIR__ . '/../config/main-local.php')
);

$application = new yii\web\Application($config);
```

$config 看着复杂，但本质上就是一个各种配置项的数组。Yii中就是统一使用数组的方式对对象进行配置，而实现这一切的关键就在 yii\base\Object 定义的构造函数中:

```PHP
public function __construct($config = [])
   {
       if (!empty($config)) {
           Yii::configure($this, $config);
       }
       $this->init();
   }
```
*所有 yii\base\Object 的构建流程是：*
* 构建函数以 $config 数组为参数被自动调用。
* 构建函数调用 Yii::configure() 对对象进行配置。
* 在最后，构造函数调用对象的 init() 方法进行初始化。

数组配置对象的秘密在 Yii::configure() 中，但说破了其实也没有什么神奇的:

```PHP
public static function configure($object, $properties)
{
    foreach ($properties as $name => $value) {
        $object->$name = $value;
    }

    return $object;
}
```
配置的过程就是遍历 $config 配置数组，将数组的键作为属性名，以对应的数组元素的值对对象的属性赋值。因此，实现Yii这一统一的配置方式的要点有：

* 继承自 yii\base\Object 。
* 为对象属性提供setter方法，以正确处理配置过程。
* 如果需要重载构造函数，请将 $config 作为该构造函数的最后一个参数，并将该参数传递给父构造函数。
* 重载的构造函数的最后，一定记得调用父构造函数。
* 如果重载了 yii\base\Object::init() 函数，注意一定要在重载函数的开头调用父类的 init() 。
