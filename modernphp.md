# Modern PHP

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

若要在局域网被别人访问到

php -S 0.0.0.0:4000

这样phpWEB服务器监听所有接口

php使用初始化文件

php -S localhost:8000 -c app\config\php.ini

php内置服务器遗落了支持.htaccess文件的功能

需要使用路由补充

php -S localhost:8000 router.php

查找是否为内置服务器

```php
if(php_sapi_name() === 'cli-server') {
  ...
}
```


# 4. 组件

组件特征

1. 作用单一
2. 小型
3. 合作
4. 测试良好
5. 文档完善

业内比较认可的php组件,https://github.com/ziadoz/awesome-php

# 5. 良好实践

## 过滤输入

简单的可以使用`htmlentities()`函数把特殊的HTML转换为HTML实体

但是htmlentities默认不会转义单引号和识别字符集

所以一般这么用

`htmlentities(字符串,ENT_QUOTES,'UTF-8')`

如果需要更严格的过滤,可以使用HTMLpurifier库,但是速度慢和难以配置

## SQL查询

可以对前端得到的属性值进行过滤

`$email = filter_val(前端email,FILTER_SANITIZE_EMAIL);`

一般过滤单个属性,可以采用filter_val和filter_input函数

## 转义输出

可以试试smarty/smarty 默认会转义输出,除非明确表明不要转义

## 密码

业界认为比较安全的是bcrypt算法,特点是故意很慢......让你不好破

## 错误

一般的php.ini关于错误的设置

开发
```
dispaly_startup_errors = On
display_errors = On

error_reporting = -1

log_errors =On
```

生产

```php
dispaly_startup_errors = Off
display_errors = Off

error_reporting = E_ALL & ~E_NOTICE

log_errors =On
```

## 错误处理程序

```php
set_error_handler(function ($errno, $errstr, $errfile, $errline){
  if (!(error_reporting() & $errno)) {
    return;
  }
  throw new ErrorException($errstr, $errno,0,$errfile,$errline);
});

//...编写其他代码 触发错误

//还原之前的错误处理程序
restore_error_handler();

```

## 在生产环境中处理错误和异常

可以尝试Monolog

# 7. 配置

## SSH密钥对认证

在本地设备中

```shell
输入ssh-keygen

//然后把生成的.pub文件上传到服务器, 带最后的: 会传输到账号的家目录中
scp ~/.ssh/id_rsa.pub 账号@IP:

#AuthorizedKeysFile     %h/.ssh/authorized_keys
```