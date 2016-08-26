# ModernPHP

# 2. 特性

以下use为PHP5.6开始

use导入函数: use func Namespace\functionName;

use导入变量 use constant Namespace\CONST_NAME;

生成器:

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