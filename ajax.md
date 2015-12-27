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
        
        
    }
}
```