# Linux鸟哥私房菜基础学习篇

## 12 正则表达式与文本格式化处理

需要注意版本的字符格式是啥,不同的字符格式可能会不一样

### 12.1 grep

grep分析的是一行,当一行中有我们想要的东西,就把该行抽取出来

`grep [-A] [-B] [-acinv] [--color=auto]`

```shell
-a: 将binary文件以text文件的方式查找数据
-c: 计算找到`查找字符串`的次数
-i: 忽略大小写
-n: 输出行号
-v: 反向选择,即显示没有`查找字符串`的那一行
--color=auto: 可以将`查找字符串`加上颜色
-A: 后面直接跟数字,表示显示匹配行的后多少行
-B: 后面直接跟数字,表示显示匹配行的前多少行
```

`--color=auto`是一个很爽的东西,但是每次都要输入吗?.在Mac上改一下`~/.bash_profile`吧

1. ` vim ~/.bash_profile`: 添加`alias grep='grep --color=auto'`
2. 编译: `source ~/.bash_profile`,OK

正则注意点

1. `^`在`[]`外表示匹配行首,在`[]`内部表示反义
2. 用`{}`匹配时,因为`{与}`shell有语义所以必须`\{\}`转义

### 12.2 sed工具

`sed [-nefr] [动作]`





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
4. `/n`: 每隔n执行





