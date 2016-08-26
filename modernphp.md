# ModernPHP

# 2. 特性

以下use为PHP5.6开始

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

闭包:

闭包实现

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