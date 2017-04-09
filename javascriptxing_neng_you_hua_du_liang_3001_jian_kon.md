# JavaScript的性能优化度量、监控与可视化

# 0. 总结

这本书我看第一章,还以为有点东西

后面看着大量的代码,其实核心的东西没多少

评分: 3星(满分5)

总页数185

# 1. 什么是性能

## 1.2 解析和渲染

规定一个IP包最大值为65535字节(约8kb)

## 1.4 为什么性能如此重要

放弃率 = (1- (成功访问数/访问总数)) x 100

成功访问数是 购买一个商品 注册一个账号 点击一个按钮

## 1.6 本书的目的

熟练的工人能制造出一些东西,但只有大师才能制作出杰出的作品,并用数据证明它的杰出之处

## 1.7 使用技术以及技术拓展

http://httparchive.org/trends.php

上面有非常多有趣的统计 例如CDN的占用率等

推荐可视化入门书籍https://item.jd.com/11098788.html

还有可视化讲解的网址 http://flowingdata.com

# 3. WPTRunner

插件相关url: https://sites.google.com/a/webpagetest.org/docs/advanced-features/webpagetest-restful-apis



作者也把自己的小mode放在github上https://github.com/tomjbarker/WPTRunner

这里面主要统计的有加载时候和请求数

# 4. perfLogger --javascript基于测试和日志记录

这里主要通过自己主动埋点然后通过xhr记录

https://github.com/tomjbarker/perfLogger

# 5. 展望未来,性能的标准化

这里主要介绍performance进行数据记录

# 6. Web性能优化

主要通过分析性能来提优化点,也大都是老生常谈的优化技巧了

# 7. 运行时性能

也是对一些技巧进行性能分析....

# 8. 在性能、软件工程最佳实践和软件产品运行之间谋求平衡

没啥用


