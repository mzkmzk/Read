# 编程实战


##1. 避免双重求值

标准动态执行代码方法

```javascript
    var num_1 =5,
        num_2 = 6;
```

1. eval()
    ```javascript
    result = eval ("num_1 + num_2");
    ```
2. Function() 构造函数
    ```javascript
    sum = new Function("arg1","arg2","return arg1 +arg2");
    ```
3. setTimeout()
    ```javascript
    setTimeout("sum = num_1 + num_2",100);
    ```
4. setInterval()
    ```javascript
    setInterval("sum = num_1 + num_2",100);
    ```
因为在执行一段`javascript`代码时去执行另外一个`javascript`代码,会导致双重求值的性能消耗.

尽可能不用eval和Function,setTimeout和setInterval建议使用匿名函数.

##2. 使用Object/Array直接量

```javascript
var my_object =new Object();
my_object.name ='404_K';

var my_array =new Array();
my_array[0] = 1;
my_array[1] = 2;
```

使用直接量.

```javascript
var my_object = {
    'name' : '404_K'
};

var my_array = ['1',2];
```

##3 避免重复工作

1. 别做无关紧要的工作
2. 别重复做已经完成的工作.

最常见的重复工作为浏览器坦诚,

```javascript
function add_handler(target,event_type,handler){
    if(target.addEventListener){ //DOM2 Events
        target.addEventListener(event_type,handler,false);
    } else { //IE
        target.attachEvent("on" +eventType,handler);
    }
}

function remove_handler(target,event_type,handler){
    if(target.removeEventListener){
        target.removeEventListener(event_type,hanlder,false);
    }else {
        target.detachEvent("on"+eventType,handelr);
    }
}
```

每次运行方法都熬做方法的判断

解决办法

1. 延迟加载
    
    在函数被调用前,没有必要判断用哪种方式执行.

    ```javascript
    function add_handler(target,evnet_type,handler){
        if(target.addEventListener){
             add_handler = function(target,event_type,handler){
                target.addEventListener(evenet_type,handler,false);
             }
        }else {
            add_handler = function(target,event_type,handler){
                target.attachEvent("on"+event_type,handler);
            }
        }
        
        addhandler(target,event_type,handler);
    }
    ```
    这里就第一次执行add_handler去判断该如何操作.
2. 条件预加载
    
    和延迟加载类似,不过判断条件是提前执行的,以保证每次执行该函数的速度一致.

    ```javascript
    var add_handler = document.body.addEventLister ?
                      function(target,event_type,handler){
                         target.addEventLister(event_type,handler,false);
                      } :
                      function(target,evnet_type,handler){
                        target.attachEvent("on"+evenet_type,handler);
                      }
    ```
    
##2. 位操作

`javascript`中的数字都依照IEEE-754标准以64位格式存储,在位操作中,数字被转换为有符号的32位格式

```javascript
var num_1 =25
alert(num_2.toString(2));//会忽略最高位的0
```

逻辑操作符

1. 按位与 &
2. 按位或 |
3. 按位异或 ^ (两个操作数的对应位只有一个为1,则该位为1)
4. 按位取反 ~
5. <<
6. >>


1. 判断奇偶

    ```javascript
    if(i%2)...
    if(i&1)... //优化
    ```
2. 位掩码

    处理存在多个布尔选项的情形,思路为使用单个数字的每一位来判断是否选项成立.掩码中的每个选项的值都等于2的幂.
    
    ```javascript
    var OPTION_A = 1;
    var OPTION_B = 2;
    var OPTION_C = 4;
    
    //
    ```