# 图解HTTP

##1. 了解Web

###1.1 三次握手

1. 发送端标有SYN(synchronize)标志的数据给接收端
2. 接收端回传一个带有SYN和ACK(acknowledgement)标志的数据包以示传达成功.
3. 发送端再回传带有ACK标志的数据包,结束.

###1.2 请求报文 & 响应报文

![请求报文](QQ20160114-0.png)

其实请求报文也有主体,一般当方式为POST,参数就作为请求报文的主体,首部和主体靠空行(CR回车符+LF换行符)相隔开.

1. 请求报文的起始行叫请求行: 包含请求方法 请求URI HTTP版本.
2. 响应报文的起始行叫状态行: 包含HTTP版本 响应结果状态码 原因短语.
3. 首部字段: 表示请求/响应的各种条件和属性的各类首部.一般的首部有通用首部,请求首部,响应首部和实体首部.
4. 其他: HTTP的RFC里未定义的首部(Cookie等)

###1.3 报文主体和实体主体

1. 报文: 是HTTP通信中的基本单位,由8位组字节流组成,通过HTTP通信传输.用于传输请求/响应的实体主体.
2. 实体: 作为请求或响应的有效载荷数据(补充项)被传输,其内容由实体首部和实体主体组成.

通常,报文主体等于实体字体,只有当传输中进行编码操作时,实体主体发生变化,才导致它和报文主体产生差异.

###1.4 压缩

常用

1. gzip(GNU zip)
2. compress(UNIX系统的标准压缩)
3. deflate(zlib)
4. identity(不进行编码)
5. sdch(Shared Dictionary Compression over HTTP)

笔者网站在Chrome47打开就会显示

    Accept-Encoding:gzip, deflate, sdch

###1.5 分块发送

当编码实体资源未全部传输完成,客户端无法显示请求页面.可以把数据分割为多块,称为分块传输编码,么一块都会用16进制标记块大小,而实体主体的最后一块会用"0(CR+LF)"标记.

HTTP1.1存在一种称为传输编码(Transfer Coding)机制,可以在通信时按某种编码方式传输,但只能作用于分块传输编码中.

###1.6 发送多种数据的多部分对象集合

1. multipart/form-data web表单上传文件时使用

2. multipart-byteranges

    状态码为206

在使用多部分集合时,首部字段都会加上Content-type

###1.7 获取部分内容的范围请求

在执行部分请求时,首部字段Range指定资源的byte范围

1. 获取5001~1000字节: `Range: bytes=5001-10000`
2. 获取5001之后全部:`Range: bytes=5001-`
3. 从一开始到3000和5000-7000字节的多重范围 : `Range:bytes=-3000, 5000-7000`

针对范围请求,返回状态码为206Partial Content,对于多重范围的请求,响应首部字段Content-Type表名multipart/byteranges后返回响应报文.

###1.8 内容协商返回最合适的内容

1. 服务器驱动协商:服务器根据首部字段自动处理
2. 客户端驱动协商:用户从浏览器显示手动选择
3. 透明协商,服务器驱动和客户端驱动各自进行内容协商.


## 2. 状态码

1. 1XX : 接受请求正在处理
2. 2XX : 请求处理成功
3. 3XX : 重定向状态码
4. 4XX : 服务器无法处理请求
5. 5XX : 服务器处理请求错误

详解: 

1. 200 OK 当请求方式不同,返回结果不同,GET请求资源的实体会作为响应返回,HEAD请求资源的实体首部不随报文主体响应返回.
2. 204 No Content 不允许返回任何实体的主题.
3. 206 Partial Content 返回指定资源的一部分请求

---

1. 301 Moved Permanently 永久性重定向
2. 302 Found 临时性重定向
3. 303 See Other 和302类似,不过303表明客户端应当采取GET方法获取资源
4. 304Not Modified表名客户端发送附带条件的请求(If-Math,If-Modified-Since等),服务区允许访问,但请求未满足条件,返回304
5. 307 临时重定向,和302类似,不过大多浏览器不会强迫把POST改为GET

301,302,303作为装潢台吗返回,几乎所有浏览器都会默认把POST改为GET,并删除请求报文内的主题,之后请求会自动重新发送.

---
1. 400 Bad Request:   请求报文存在语法错误,浏览器会对待200一样对待此状态
2. 401 Unauthorized:请求需要通过HTTP认证(BASIC认证/DIGEST认证)等,但若之前进行过一次失败的认证,返回该状态码
3. 403 Foribdden : 无权限访问该资源
4. 404 服务器上没有请求的资源

---

1. 500 Internal Server Error表示服务器执行请求时发生了错误
2. 503 Service Unavailable 表示服务器超负载,无法处理请求.

##3. 与HTTP协作的Web服务器

###3.1 通信数据转发程序:代理 网关 隧道

1. 代理: 是一种有转发功能的应用程序.
    每次经过一个代理服务器时,都会增加Via首部字段以标记经过的合租记信息.
    
    使用代理原因有:是有缓存,对特定网站进行访问控制

    代理有两种基准分类,是否使用缓存/是否修改报文
    
    1. 缓存代理: 会预先将资源的副本缓存到代理服务器上
    2. 透明代理: 不对报文进行任何加工的代理成为透明代理,反之称为非透明代理
    
2. 网关: 网关和代理类似,但是网关能使通信线路上的服务器提供非HTTP服务.
3. 隧道: 可按要求建立一条与其他服务器的通信线路,加密,不会解析HTTP请求.

##4. HTTP首部

HTTP首部字段类型

1. 通用首部字段: 请求报文和响应报文都会使用
2. 请求首部字段: 客户端向服务器请求时使用
3. 响应首部字段: 服务器向客户端请求时使用
4. 实体首部字段: 针对请求报文和响应报文的实体部分使用的首部.

可以参考<https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9.4>

###4.1 通用首部字段

通用首部指:请求报文和响应报文双方都会使用的.

1. Cache-Control:操作缓存机制,可有多个值`,`分割
    
    缓存请求指令

    1. no-cache: 强制向服务器再次验证,可指定特定用户`no-cache=Location`,其实该指令代表不缓存过期的资源,缓存会向源服务器进行有效期确认后处理资源.
    2. no-store: 不缓存请求或响应的任何内容,该指令才是真正的不进行缓存.
    3. max-age=秒: 响应最大的Age值,如果判断缓存资源的缓存时间数值比指定的数值更小,那么客户端接受缓存的资源,另外max-age值为0,那么缓存服务器通常需要将请求转发给源服务器.
    4. max-stale=秒: 接受已过期的响应,如果该指令未指定参数值,无论经过多久,客户端都会接受响应,如果指定了值,即使过期,主要出于max-stale指定时间内,仍旧会被服务器接受.
    5. min-fresh=秒: 期望在指定时间内响应仍有效,要求缓存服务器返回至少还未超过指定时间的缓存资源.
    6. no-transform: 代理不可更改媒体类型,,可防止缓存/代理压缩图片等操作
    7. only-if-cached: 从缓存获取资源,要求服务器不重新加载响应,也不会再次确认资源的有效性,若发生请求缓存服务器本地缓存无响应,则返回504Gateway Timeout.
    8. cache-extension: 新指令标记(token).
    ---
    缓存响应指令
    
    1. public: 可向任一方提供响应缓存
    2. private: 仅向特定用户返回响应
    3. no-cache: 缓存前必须先确认其有效性
    4. no-store: 不缓存请求或响应的任何内容
    5. no-transform: 代理不可更改媒体类型,可防止缓存/代理压缩图片等操作
    6. must-revalidate: 可缓存但必须再向源服务器进行确认,代理缓存会向服务器再次验证即将返回的响应缓存目前是否仍然有效,若代理无法连通服务器再次获取有效资源,缓存必须给客户端一条504状态码.设置了该指令会忽略max-stale.
    7. proxy-revalidate: 要求中间缓存服务器对缓存的响应有效性再进行确认,和must-revaladate的区别是该方法不适用于非共享代理服务器,可以让用户只进行一次验证,其他用户再请求则无需验证.(该解释笔者参考下文<https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9.4>,
    
        The proxy-revalidate directive has the same meaning as the must- revalidate directive, except that it does not apply to non-shared user agent caches. It can be used on a response to an authenticated request to permit the user's cache to store and later return the response without needing to revalidate it (since it has already been authenticated once by that user), while still requiring proxies that service many users to revalidate each time (in order to make sure that each user has been authenticated). Note that such authenticated responses also need the public cache control directive in order to allow them to be cached at all.)

    8. max-age=秒: 响应最大的Age值,当源服务器设置了该值,缓存服务器将不对资源的有效性再作确认,而max-age数值代表资源保存为缓存的最长时间.HTTP1.1会忽略Expires,而HTTP1.0会忽略max-age,
    9. s-maxage=秒: 公共缓存服务器响应最大的Age值,只适用于多用户使用的公共缓存服务器,设置了该指令后,忽略Expires和max-age.
    10. cache-extension: 新指令标记(token),自己设定指令,给缓存服务器识别.
    
2. Connection

    作用:
    
    1. 控制不再转发给代理的首部字段:`Connection:不再转发的首部字段名`.
    2. 管理持久连接: HTTP1.1默认连接都是持久连接,当服务器明确想断开关系时:`Connection:close`在HTTP1.1版本之前默认连接未非持久连接的,需要持久连接可以`Connection:Keep-Alive`.

3. Date

    表名创建HTTP报文的日期和时间`Date:Sat, 16 Jan 2016 07:22:04 GMT`,不同版本的规则Date格式有所不一样.详情看本章一开头的链接.
4. Pragma: 旧版本主要设在通用首部字段,历史遗留字段.
5. Trailer: 说明报文主体记录了哪些首部字段,可应用在分块传输编码.
6. Transfer-Encoding: 规定报文主体采用的编码方式,HTTP1.1只对分块传输编码有效.
7. Upgrade: 检测HTTP或其他协议是否有更高版本使用,要和`Connection:Upgrade`联合使用.
8. Via: 追踪客户端和服务器之间的传播路径.每经过一个代理/网关,会在via增加代理/网关的信息,还可以避免回环.
9. Warning: Warning : [警告码][警告主机:端口号] "[警告内容]" ([日期时间]).

###4.2 请求首部字段

1. Accept: 告知服务器,客户端能处理媒体类型的优先级别,格式为type/subtype,例如客户端想要HTML资源时候`Accept: text/html application/xhtml+xml;q=0.9,*/*;q=0.8`q值为0~1,指定优先级.
2. Accept-Charset: 告知服务器客户端支持字符串的优先顺序.
3. Accept-Encoding: 告知服务器客户端支持的编码和优先顺序,gzip,deflate等
4. Accept-Language: 告知服务器客户端所需的语言及其优先级别.
5. Authorization: 告知服务器,客户端的认证信息.
6. Expect: 告知服务器,客户端需要某种特殊的行为,`Expect: 100-continue`,服务器无法识别特殊行为时,返回417Expectation Failed
7. From: 告知服务器客户端用户的电子邮件.
8. Host: 同一服务器运行两个Web,靠Host加以区分.
9. if-Match: 告知服务器资源的实体标记(ETag值),只有在服务器又匹配的ETag文件,资源才可返回.还可以使用`*`指定,代表忽略ETag值.
10. if-Modified-Since: 告知服务器若if-Modified-Since字段早于资源更新时间,则返回现有资源,若再if-Modified-Since晚于资源更新时间,则返回304NOT Modified
11. if-None-Math: 只有当if-none-Match字段和ETag值不一致时,可以处理这个请求.使用GET/HEAD方法使用if-none-Math,可获取最新的资源.
12. if-Range: 设定ETag/时间,和请求的资源的ETag/时间一致,作为范围请求处理,反之,返回全体资源.
13. if-unmodified-Since: 和if-Modified-Since作用相反.
14. Max-forwards: 设定最大的转发次数,当Max-Forwards为0时,由服务器直接请求.
15. Proxy-Authorization: 接受到服务器发送过来认证质询时,客户端发送此字段.和HTTP访问认证类似,不过该字段是代理和客户端认证
16. Range: 指定资源的部分请求
17. Referer: 会告知服务器请求原始资源的URL
18. TE: 告知服务器,客户端传输编码方式及优先级,和`Accept-Encoding`类似,但这个方法用于传输编码.书上写的模糊这两个方式的区别,一般浏览器看到的是`Accept-Encoding`比较多.
19. User-Agent: 浏览器信息和代理信息.

###4.3 响应首部字段

1. Accept-Ranges: 服务器告诉客户端,服务器能接受的处理范围请求,可处理范围请求时值为bytes,繁殖为none.
2. Age:源服务器在多久前创建了响应,若创建该响应的是缓存服务器,该值 指缓存后的响应再次发起认证完成的时间值.(代理创建响应时必须带上Age)
3. ETag: ETag告知客户端实体识别,强ETag,只要发生变化,ETag都会发生变化`ETag:"usagi-1234"`,弱ETag值只用于提示资源十分相同,当资源发生更笨变化时,才改变Etag`ETAG: W/"usagi-1234"`
4. Locaition: 重定向
5. Proxy-Authenticate: 该字段会由代理服务器所要求的认证信息发送给客户端.
6. Retry-After: 告知客户端过多久后重新访问,一般配合503和3xx
7. Server: 告诉客户端,服务器的运行环境
8. Vary: 代理服务器接受到Vary请求时,如果值和`Accept-Language`值相同,那么返回缓存结果,反之,重新从源服务器请求.
9. WWW-Authenticate: HTTP访问认证.

###4.4 实体首部字段

实体首部是包含在请求报文和响应报文中的实体部分所使用的首部.用于补充内容更新时间等与实体相关的信息.

1. Allow: GET,HEAD等,告知客户端服务器支持的Request-URI指定的所有HTTP方法,当服务器接受不支持的HTTP方法时,返回405 Method Not Allowed,并且会把所有能支持HTTP方法写入首部字段Allow后返回.
2. Content-Encoding: 告知客户端实体的主体部分的编码方式,gzip,compress等.
3. Content-Language: 告知客户端实体所用的语言
4. Content-Length: 服务器告诉客户端实体主体部分的大小(字节),当设置了内容编码传输时,不能再使用Content-Length.
5. Content-Location: 给出与报文主体部分相对应的URI,和首部字段Location不同,Content-Location表示报文主体返回资源的URI.例如当设置了Accept-Language时,返回页面内容和实际请求对象不同,Content-Location会写明URI.
6. Content-MD5: 客户端会对接受报文主体执行相同的MD5算法,然后和首部字段Content-MD5字段值比较.目的在于验证报文主体在传输过程是否保持了完整.对报文主体进行MD5算法得出128位二进制,再通过Base64编码后将结果写入Content-MD5,然后只能保证完整性,没有安全性可言,因为内容被篡改,Content-MD5也能被篡改.
7. Content-Range: 范围请求
8. Content-Type: 指定实体主体内对象的媒体类型,
9. Expires: 会将资源失效日期告知客户端,缓存服务器接收到含有Expires响应后,会以缓存来应答请求,在Expires字段值指定之前,响应的副本会被一直保存,当超过指定时间时,缓存服务器在请求发送过来时,会转向源服务器请求资源
10. 



疑问:代理服务器和缓存服务器的区别?.