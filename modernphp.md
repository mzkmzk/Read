# ModernPHP

# 2. 特性



>use

PHP5.6开始

use导入函数: use func Namespace\functionName;

use导入变量 use constant Namespace\CONST_NAME;

生成器(PHP5.5.0及其以上):

可以用于优化,不用先占用内存

bad:

```php
function makeRange($length) {
  $dataset = [];
  for ($i=0;$i < $length; $i++) {
     $dataset[] = $i;
  }
  return $dataset;
}

$customRange = makeRange(1000000);
foreach ($customRange as $i) {
  echo $i, PHP_EOL;
}
```

best: 

```php
function makeRange($length) {
  for ($i=0;$i < $length; $i++) {
    yield $i;
  }
}

foreach (makeRange(1000000) as $i) {
  echo $i, PHP_EOL;
}
```

>闭包:

```php
var $closure = function($name) {
  return spritf('Hello %s',$name);
}

echo $closure('K');
```

闭包其实就是对闭包对象`\Closure`实现了`__invoke()`魔术方法,只要变量后有`()`,PHP就会查找并调用__invoke()方法

关于闭包的另外一个函数bindTo

例如在处理路由时

```php
class App {
  public function addRoute($routePath,$routeCallback) {
    $this->routes[$routePath] = $routeCallback->bindTo($this,__CLASS__);
  }
}

//调用
$app = new App();
$app->addRoute('users/josh',function(){...});
```

这样闭包里就可以使用运行时的app

>Zned OPcache

在php5.5.0开始 php才内置字节码缓存

如果没开字节码缓存,每次HTTP请求PHP解析器把php变成字节码

如果自己编译PHP

执行`./configure`时必须包含`zend_extension=/path/to/opcache.so`

编译后,可以查看PHP拓展所在目录

php-config --extension-dir

配置ZendOPcache在php.in中

```shell
opcache.validate_timestamps = 1 //生产环境设为0
...其他
```

validate_timestamps设为0后,Zend OPcache会感觉不到PHP脚本的编号,必须手动清空Zend OPcache缓存的字节码

>内置的HTTP服务器

PHP5.4.0开始,PHP内置WEB服务器

1. cd到项目根目录
2. php -S localhost:4000







