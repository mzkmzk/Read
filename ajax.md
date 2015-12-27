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


