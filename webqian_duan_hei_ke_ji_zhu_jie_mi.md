# Web前端黑客技术揭秘

# 1. Web安全的关键点

## 1.2 浏览器的同源策略

域名 http://www.foo.com

1. https://www.foo.com: 不同域
2. http://abc.foo.com: 不同域
3. http://foo.com: 不同域
4. http://www.foo.com:8080: 不同域
5. http://www.foo.com/a: 同域

# 6. 漏洞挖掘

csrf漏洞只需确认以下内容即可

1. 目标表单是否有token随机串
2. 目标表单是否有验证码
3. 目标是否判断了refer源
4. 网站根目录下crossdomain.xml的'allow-access-from domain'是否是通配符
5. 目标JSON数据是否可以自定义callback函数等



# 7. 漏洞利用

## 7.1 渗透前的准备

需要明确

1. 渗透的目标环境是什么
    
    如果是开源的系统,例如CMS内容管理系统,我们可以联合白盒黑盒进行渗透
2. 目标用户是谁

    例如CMS管理员,客服,用户的攻击思路存在差异,可以通过社工手段进行调查
3. 达到的攻击效果是什么

    如 获取Cookie,添加一篇文章,传播网马等





