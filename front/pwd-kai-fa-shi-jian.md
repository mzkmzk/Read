# PWA开发实践

## 3. CacheStorage API

### 3.6 匹配每个请求的正确响应

> ignoreSearch

在我们进行html请求缓存的时候, 因为html可以携带了N多参数

假如缓存的key是`/promo.html`, 那么请求的url是`/promo.html?test=1` 则会找不到缓存

如果确定参数对html内容无影响的话, 可以进行忽略参数的查找`Cache`

`caches.match(event.request, { ignoreSearch, true })`

## 4. Service worker生命周期及缓存管理

installing -> installed/waiting -> activating -> activated -> redundant

当 installing 失败时也会

installing -> redundant

> installing

使用`navigator.serviceWorker.register`注册一个新的service worker时, 

js代码就会被下载、解析、进入安装状态, 安装成功后 就进入installed状态

假如安装失败, 则直接进入redundant

安装过程中可以使用`waitUntil(promise)`, 进行延伸

```javascript
self.addEventListener('install', function(event){
    event.waitUntil(promise)
})
```

则会等待promise返回resolver才进入`installed`

> installed/waiting

service worker安装完成后, 就会进入installed状态

一般情况下会立马进入activating状态, 除非另一个activating的service worker依然在控制页面, 

那么installed的service worker 就会进入waiting状态

> activating

在sw激活和接管应用之前, 会触发activate事件, 同样可以用event.waitUntil进行扩展

一旦sw激活了, 就可以监听功能性事件了, 例如`fetch事件`

sw只能在页面开始加载之前控制页面, 

`如果页面在sw激活前就已经开始加载`, 则sw是不能够控制页面的

一个sw的生命周期中, 安装和激活只会运行一次

### 4.2 service worker的声明周期与waitUntil的重要性

一旦sw安装并激活, 会发生什么呢?

因为sw不是直接绑定在任何标签页/窗口上, 并且可以随时响应事件, 它是否意味着它一直在运行呢?

答案是否定的, 这样浏览器会很卡

代替的方案是: sw的生命周期直接与它所处理的事件的执行联系在一起, 当某个sw作用域下的时机被处罚, sw就会被唤起

然后处理事件, 然后终止

但是假如事件被唤起执行时, 执行了个异步方法, 但没进行event.waitUntil, 异步事件就不会执行

所以异步事件, 需要用event.waitUntil包裹

```javascript
self.addEventListener('push', function(event){
    event.waitUntil(function(){
        fetch('updates')
            .then(() => {
                return self.registration.showNotification('new updates')
            })
    })
})
```

### 4.3 更新service worker

每当页面加载一个激活的sw, 就会检查sw脚本的更新

如果sw在注册之后发生了更改, 新的sw就会被注册和安装

安装完成后不会立即替换到现有的sw

屙屎需要sw作用域中的每个标签页/窗口关闭, 或者导航到一个不再其控制范围内的页面.

只有在当前激活的sw控制的页面全部被关闭后, 旧的sw才会进入废弃状态

然后新的sw才会激活

### 4.4 为什吗需要管理缓存

建议每一个sw存取/读取的缓存都加一个版本号, 避免新旧sw操作同一个缓存

但我们也需要把之前的缓存给清除掉, 不然很快存储会达到上限

一般操作是在sw里增加`active`事件监听, 在里面清除旧缓存

### 4.6 重用已缓存的响应

当我们知道有些url内容是用久不变的, 就可以copy旧缓存到新缓存里

### 4.7 配置服务器以提供正确的响应头部

在sw文件里每次加载都会检查, sw的缓存建议1~10分钟

## 5.拥抱离线优先

### 5.2 常用的缓存模式

> 仅缓存

在缓存中读取请求, 若缓存无法找到, 则请求失败

> 缓存优先, 网络作为回退方案

现在缓存中取, 网络没有sw再去网络中取回

> 仅网络

> 网络优先, 缓存作为回退方案

> 先缓存, 后网络

先使用缓存结果, 当网络结果回来了进行结果对比, 不一样则替换

### 5.3 混合和匹配: 创造新模式

- 按需缓存
- 缓存优先, 网络作为回退方案, 并且频繁更新缓存