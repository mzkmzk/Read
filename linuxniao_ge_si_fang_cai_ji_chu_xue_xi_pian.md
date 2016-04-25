# Linux鸟哥私房菜基础学习篇

## 16.例行性工作

### 16.1 任务分类

1. 仅执行一次的命令: at设置只执行一次的命令(需要atd服务的支持)
2. 常规性命令: crontab循环性指令(需要crond服务)

### 16.2 仅执行一次的工作调度

linux检查有无atd服务: `/etc/init.d/atd status`

linux开机自启动atd: `chkconfig atd on`

这里发现at在ubuntu和书籍中很多不符,暂不研究

## 16.3 循环执行的例行性工作调度

### 16.3.1 用户的设置

限制用户

1. 设置允许的用户: /etc/cron.allow
2. 设置不允许的用户: /etc/cron.deny

优先性: cron.allow>cron.deny

命令说明

```shell
crontab [-u username] [-l|-e|-r]

-u: 只有root才能执行/帮其他用户新建/删除crontab
-e: 编辑crontab
-l: 列出所有crontab
-r: 删除所有crontab
```
命令设置

`分钟 小时 日期 月份 周 命令`

特别的符号代表

1. `*`: 此字段不做限制
2. `,`: 在符合的数字字段都可执行`||`
3. `-`: 时间范围设置
4. 



