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




