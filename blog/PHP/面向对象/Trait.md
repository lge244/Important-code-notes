### Trait

* PHP是单继承的语言，在PHP 5.4 Traits出现之前，PHP的类无法同时从两个基类继承属性或方法。php的Traits和Go语言的组合功能类似，通过在类中使用use关键字声明要组合的Trait名称，而具体某个Trait的声明使用trait关键词，Trait不能直接实例化。具体用法请看下面的代码：

```PHP
trait Drive {
    public $carName = 'trait';
    public function driving() {
        echo "driving {$this->carName}\n";
    }
}

class Student {
    use Drive; // use 引入Trait名称即可使用 里面的方法和属性了
    public function study() {
        echo "study\n";
    }
}
$student = new Student();
$student->study(); // study
$student->driving(); //driving trait
```
[参考](http://php.net/manual/zh/language.oop5.traits.php)
