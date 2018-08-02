# 初识前端跨域

# 方式 

解决跨域的方式

1. form表单+iframe
2. xhr
3. jsonp

| 满足情况 | form | xhr | jsonp | 
| --- | --- | --- | --- |
| 可否POST | 1 | 1 | 0 |
| 可否兼容IE8 | 1 | 1| 1 |
| 可否设置请求头 | 1 | 1 | 0 |
| 可否在IE9兼容设置请求头 | 1 | 0 | 0 |
| 可否上传文件 | 1 | 1 | 0 |
| 后端服务器可否不与页面根域一致 | 0 | 1 | 1 |

# form表单跨域原理

form跨域传输数据有两种

第一种就是通过客户端返回Set Cookie

![Set Cookie](/assets/QQ20180802-175921.png)

第二种方式是 

在响应问中返回类似的结构

`<script>document.domain="xxx.com";window.top.btupload({"result":0})</script>`

因为第一种Set Cookie 服务器只能设置服务器根域或子域的Cookie

而 JS最多只能拿到根域的cookie 所以页面根域和接口根域 必须一致 才能拿到数据

# JSONP

JSONP的原理就是

1. 现在window设置一个函数名 window.abc = function(){}
2. 往页面插入一个script: script src="...?callback=abc"
3. 服务器返回 abc({...}) 

所以JSONP只是类似请求一个JS资源..所以它当然只能GET拉

# OPTIONS 204 和 跨域

假如发跨域的xhr请求时  自定义了header

浏览器就会给服务器先发一个OPTIONS请求 主要问浏览器支持哪些提交方法 和 允许哪些自定义头

然后服务器一般是返回204给浏览器

![204返回](/assets/QQ20180802-212558.png)

然后再去真正的请求

# xhr跨域和cookie

form表单和jsonp都是默认带cookie

但是 xhr的跨域想带cookie就js和服务器都是做设置

服务器需要带`Access-Control-Allow-Credentials: true`

而浏览器需要设置`xhr.withCredentials = true`

这样才能带上cookie

# 跨站与IE

虽然IE8 9 都有XMLHttpRequest 

但是跨域的话 会显示`SCRIPT5: 拒绝访问。`

所以这个可以用XDomainRequest 来解决跨域问题

但是 XDomainRequest 是 没有setRquestHeader方法的...所以IE无法跨域时设置Header

# 服务器的Set-Cookie是否只允许 设置域名根域 或者其子域

aaa.xxx.com接口返回Set-Cookie: upgrade=; Path=/; Domain=xxx.com

这样才能设置cookie 否则无效(抓包有Set-Cookie头 但是chrome Response没有)

![无效cookie](/assets/QQ20180802-183651.png)


