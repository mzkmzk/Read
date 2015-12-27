# UI

在执行`javascript`时,大多数浏览器会停止把新UI任务放进队列中.

但当点击一个按钮,会先触发UI变化(假如有),然后再执行`javascript`,如果在`javascript`有改变UI的代码,会等`javascript`执行完了,再把UI任务放进队列.

不同的浏览器会对`javascript`运行的时间/行数进行限制.

`有些浏览器`,当你在`javascript`运行时点击按钮,而如果按钮正在重绘,就不会处理你这次点击.

当脚本执行时,UI不随用户交互而更新(跳过),执行时间段内用户交互引发的`javascript`任务会到队列中,

IE会控制用户交互时触发的`javascript`任务,连续两次的重复动作,会只执行一次.

所以`javascript`应该支持`最(I)慢(E)浏览器`执行在100毫秒内完成.

##1. 使用定时器让出时间片段

1. setTimeout(函数,毫秒)
    
    创建一个执行一次的定时器

2. setInterval(函数,毫秒)

    创建一个周期性重复运行的定时器.
    
```javascript
button.onclick = function (){
    one_method();
    
    setTimeout(function(){
        UI变化.
    },50)
    
    two_method();
}
```    

这里有几个注意点

1. 50ms代表,在执行到`setTimeout`函数之后50ms开始把这个`UI变化`放进UI队列中.
2. 定时器的代码只有在创建它的函数(`onclick`处理事件)执行完成后,才有可能被执行.
3. 如果`two_method()`执行了超过50ms,`UI变化`可能在函数执行完成了之后立马就被执行.
4. 每创建一个定时器,都会让UI线程停止(但新的UI任务可以尽快进来),并且重置了所有浏览器的时间和代码条数的限制.
5. 如果UI队列中存在由同一个`setInterval()`创建的任务,后来的任务不会加进UI队列中.

##1.2 定时器精度

`javascript`定时器精度一般会差几ms,因为规定50ms后加入队列,但不一定会那么准时进入队列.

举IE来说,IE会在15ms刷一次是否有定时器完成了等待时间,所以IE延时是0~15ms,10ms左右的定时器间隔,各个浏览器表现都不一致.

##1.3 定时器处理数组.

能用定时器处理数组的决定性条件

1. 处理过程不需同步.
2. 数据不用按顺序处理.

    (这里读者不太明白,按理说即使使用定时器也应该是按顺序执行的啊?难道是因为怕定时器间隔定义太小了,然后由于浏览器的定时器精度问题(~10ms)然后会导致后面的定时器任务先加入UI队列?,那我设大点(100ms)不就可以避免这个问题了吗)
    
    但是写到这里也大概懂了,因为作者写的定时器是数组内的每条数据都执行一个定时器,然后每个定时器设置了25ms,所以这样的忧虑还是有的.每条数据100ms可能会太长了~.
    
```javascript
function process_array(items,process,callback){
    var tudo = items.concat();//克隆数组
    
    setTimeout(function(){
        process(todo.shift());
        
        if(todo.length >0){
            //arguments.callee代表当前执行的匿名函数
            setTimeout(arguments.callee,25);
        } else {
            callback(items);
        }
    },25);
}
```

这代码算不算递归?.

递归的定义是调用自身的函数.应该是算,当时也是尾递归,尾递归指递归语句执行后,后面没有需要执行的语句,这样递归后,内存不用记录已递归函数的状态,就不会把已递归的函数入栈,所以也就不存在爆栈.

每个定时器都只执行一条数据,太慢了啊...

所以一般50ms左右的任务阻塞是不会影响用户体验的,所以我们可以定制一个定时器,处理数组时,阻塞准备超过50ms后,设置定时器.

```javascript
function time_process_array(items,process,callback){
    
}
```

##1.4 分割任务

上一节说的是每条数据只需执行一条语句,那如何写一条数据执行多条语句呢.


```javascript
function save_document(id){
    open_document(id);
    write_text(id);
    close_document(id);
    
    update_UI(id);
}

//拆分语句

function multistep(steps,args,callback){
    var tasks = steps.concat(); //克隆数组
    
    setTimeout(function(){
        var task =task.shift();
        //参数null,代表调用者为window
        task.apply(null,args || []);
        
        if(tasks.length >0 ){
            setTimeout(arguments.callee,25);
        }else {
            callback();
        }
    },25);
}

//使用

function save_document(id){
    var tasks = [open_doucment,write_text,close_document,update_UI];
    
    multistep(tasks,[id],function(){
        alert("Save completed!");
    });
}
```

其实这个分割任务可以和上面的定时器使用数组结合起来使用.

但是这个通用的分割方法会有一个弊端,他们的每条语句的参数都是一样的.

所以你每个任务如果参数不一样,你可以新建一个数组,数组里放每条语句所需的参数(每条语句的参数也需封装成数组).


