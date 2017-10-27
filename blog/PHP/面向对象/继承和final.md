### 继承

* extends 继承关键字
* final  不允许继承的关键字
```PHP
/**
 *父类
 */
final class Dad //一个类加上final 关键字就不允许被继承了
{

  public  function __construct(argument)
  {
    echo "父类的构造";
  }
  public function kangfu()
  {
    echo "降龙十八掌";
  }
}

/**
 * 子类 继承 父类
 */
class ClassName extends Ded //extends 继承上面的父类，继承之后父类的方法之类都可以使用
{

  public  function __construct(argument)
  {
    parent::__construct() //引入父类的构造函数
    echo "子类的构造";
  }
  public function jineng() {
    $this->kangfu();
  }
}
