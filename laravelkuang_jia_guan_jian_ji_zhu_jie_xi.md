# Laravel框架关键技术解析

# 1. 组件化开发与composer使用

主要手把手教 Route Controller Model View 逐个安装和使用

# 2. Laravel框架安装与调试环境建立

额..怎么说..这里教了怎么装Laravel..

# 3. Laravel框架中常用的PHP语法

## 3.1 组件化开发语法条件

### 3.1.2 文件包含

1. include和require基本相同,只是处理失败方式不同,require会抛出E_COMPILE_ERROR语法错误,脚本会终止,而include只会E_WARNING错误,程序会继续执行

## 3.3 PHP特殊语法

### 3.3.1 魔术方法

1. __construct()
2. __destruct()
3. __set()
4. __get()
5. __isset(): 对不可访问属性调用isset()或empty()时,__isset被触发
6. __unsset(): 当对不可访问属性调用unsset()时
7. __sleep(): serialize()检查类中是否有魔术方法__sleep(),如果存在,该函数将在任何序列化之前运行,它可以清除对象并返回一个包含该对象呗序列化的所有变量名的数组,如果该函数没有返回什么数据，则不会有什么数据被序列化，并且会发出一个 E_NOTICE 错误。
8. __wakeup(): unserialize()检查是否有wakeup(),如果存在,此函数可以用于重建对象
9. __toString(): 当一个类被当作字符串处理时
10. __invoke(): 当尝试以调用函数的方式访问一个对象时,例如$obj();
11. __clone(): 对对象调用clone复制时,__clone()会被触发,这里可以用于深度复制
12. __call(): 当访问一个不可访问的方法时
13. __callStatic(): 以静态方式调用不可访问的方法时
14. __autoload(): 她会在试图调用未定义的类时调用

### 3.3.2 魔术变量

1. `__LINE__`: 文件中当前的行数
2. `__FILE__`: 文件的完整路径和文件名
3. `__DIR`: 文件所在目录,等价于dirname(`__FILE__`),除非在根目录,或者目录中不包含末尾的斜杠
4. `__FUNCTION__`: 函数名字
5. `__CLASS__`: 当用在trait时,`__CLASS__`指调用trait的类
6. `__TRAIT__`: 
7. `__METHOD__`: 类的方法名
8. `__NAMESPACE__`

## 3.5 后期静态绑定

简单来说,在类中有两种方式调用静态方法

1. self::静态方法()
2. static:: 静态方法()

static只有在运行时才能确定调用的是谁的方法,static调用的可以是静态方法...也可以是非静态方法

```php
class A {
  public static function call() {
    echo 'A ';
  }
  public static function test() {
    self::call();
    static::call();
  }
}
class B extends A {
  public static function call() {
    echo 'B';
  }
}
//调用B::test()
输出 A B
```

Laravel常用的模式

```php
class A {
  public static function create() {
    $self = new self();
    $static = new static();
    return array($self,$static)
  }
}

class B extends A {
  
}

//调用
$arr = B::create();
// $arr 为 object(A)[1] object(B)[2]
```

## 3.6 Laravel中使用的其他新特性

### 3.6.1 trait

trait特性

1. 优先级: 当前类方法覆盖trait特性,trait的方法会覆盖基本的方法
2. 多个trait组合,一个use逗号分隔多个trait
3. 冲突解决: 当两个trait中插入一个同名方法,如果没有明确声明解决会报错,解决时使用insteadof表示用冲突方法的哪一个.也可以使用as操作费对其中一个冲突的方法1️⃣另一个名称来引入
4. 修改方法的访问控制: 使用as语法调整方法的访问控制
5. trait抽象方法: 在类中必须实现这个抽象方法
6. trait静态成员: 可以使用
7. trait属性定义: 可以使用

# 4. Laravel框架中使用HTTP协议基础

这部分讲解了TCP和HTTP一些比较基础和重要的内容,还可以吧

