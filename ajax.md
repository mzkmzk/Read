# Ajax

请求数据分类:

1. XMLHttpRequest(XHR)
2. Dynamic script tag insertion 动态脚本注入
3. iframes
4. Comet
5. Multipart XHR

这里主要降序`XHR` `动态脚本注入` `Multipart XHR`

##1. XMLHttpRequest

```javascript
var url = '/data.php';
var params = [
    'id=123',
    `limit=20`
]

var req = new XMLHttpRequest();

req.onreadystatechange  function(){
    if (req.readyState == 3){ //正在`流`数据 接受到部分信息,但不是所有
        var data_now = req.responseText;
        ...
    }
    
    if (req.readyState === 4){
        var reponse_headers = req.getAllReponseHeaders();//获取响应头信息
        
        var data =req.reponseText; //获取数据
        ...
    }
    
    req.open('GET',url+'?' +params.join('&'),true);
    req.setRequestHeader('X-Requested-With','XMLHttpRequest')//设置请求头信息.
    req.send(null);//发送请求
    
}
```

注意点

1. `XHR`不能从外域请求数据.
2. 低版本IE不支持`流`,和readyState为`3`
3. `GET`请求不改变服务器数据,只作请求数据(幂等行为),这样这个GET会被缓存起来(也是一些BUG引发的原因),当URL长度超过2048时,建议使用POST,具体建议参考`RESTful`

##2. 动态脚本注入

特点:

1. 能跨域请求
2. 不能设置请求头
3. 只能采用`GET`请求
4. 不能设置请求超时
5. 必须等所有数据都返回了,才可访问它们.
6. 响应消息作为标签源码,外部的`js`必须是可执行`javascript`代码,不能使用纯XML和JSON,必须封装在一个回调函数汇总. 
7. 你使用外域的JSON,无法控制请求.容易有安全问题,例如JSONP漏洞(<http://blog.knownsec.com/2015/03/jsonp_security_technic/>)

外部文件lib.js
```javascript
json_callback({"status" : 1});
```
本地`javascript`
```javascript
    var script_element = document.createElement('script');
    script_element.src = "http://any-domain.com/javascript/lib.js";
    document.getElementByTagName('head')[0].appendChild(script_element);
    
    //回调函数,名字与外域的可执行`javascript`方法名一样,当外域js加载完成后,会执行json_callback(json_string)方法;
    function json_callback(json_string){
        var data = eval('('+json_string+')');
        
    }
```

本地通过创建和外域.js的方法同名而实现回调.

但是调用在声明之前,为何可以?.

可以详细看`第二章 :数据读取`.

##3. Multipart XHR

`Multipart XHR`基于XHR,

只不过是把多个`XHR`封装成一个`XHR`而已.

但是性能可以快4~10倍.

具体实现代码可参考.<http://techfoolery.com/mxhr/>

使用建议.

1. 页面包含了大量其他地方用不到的资源.
2. 无需从缓存读数据,除非重载页面



