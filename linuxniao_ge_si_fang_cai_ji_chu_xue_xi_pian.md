# Linux鸟哥私房菜基础学习篇

# 7 Linux文件与目录管理

## 7.5 命令与文件查询

### 7.5.1 脚本文件名的查询

查找可执行文件的位置, 在PATH变量制定的路径中,查找

`which [-a] command`

参数:

-a: 将所有PATH目录中可以找到的命令均列出, 而不只是第一个被找到的命令名称

```bash
which node
/Users/maizhikun/.nvm/versions/node/v6.10.0/bin/node
```
### 7.5.2 文件名的查找

#### locate

locate使用简单, 直接在后面输入"文件的部分名称"

locate会在自己创建数据库中查找内容, 而不是硬盘

所以新增了一个文件, 可能没有办法立即使用locate找出, 因为还没更新到数据库(一般每天一次)

使用'updatedb', 会读取updatedb.conf配置, 然后取查找硬盘, 最后更新数据库文件 

`locate [-ir] keyword`

参数:
```
-i: 忽略大小写差异
-r: 后面可以正则表达式方式
```

```bash
work@iZ94fnej0x9Z:~$ locate -r "input_definition_[0-9]\?.md"
/var/www/html/404mzk/vendor/symfony/console/Tests/Fixtures/input_definition_1.md
/var/www/html/404mzk/vendor/symfony/console/Tests/Fixtures/input_definition_2.md
/var/www/html/404mzk/vendor/symfony/console/Tests/Fixtures/input_definition_3.md
/var/www/html/404mzk/vendor/symfony/console/Tests/Fixtures/input_definition_4.md
````

#### find

`find [PATH] [option] [action]`

参数

> 与时间有关的参数 

-atime(读取文件或者执行文件时更改的)

-mtime(是在写入文件时随文件内容的更改而更改的)

-ctime(在写入文件、更改所有者、权限或链接设置时随 Inode 的内容更改而更改的)

-mtime n : 在n天之前24小时内被修改的文件

-time +n: 列出n天之前被修改的文件 (不含n)

-time -n: n天内被修改的文件(含n)

-newer file: 比file文件还要新的文件名

```bash
find ./ -mtime 0 //查找24小时内修改的文件

find /etc -newer /etc/passwd 查找在/etc下比/etc/passwd新的文件
```

> 与用户相关的参数

-uid n: 查找属于uid文件

-gid n: 查找属于gid的文件

-user name: 查找属于user的文件

-group name: 查找属于group的文件

-nouser: 查找文件所有者不在/etc/passwd的人

-nogroup: 找所有用户组不存在/etc/group的文件

> 与文件权限和名称有关的参数

-name filename: 查找文件名为filename的文件

-size [+-]SIZE: 查找比SIZE还要大/小的文件,单文有 c(bytr), k, M, G

-type TYPE: 筛选类型, f(正规文件) b/c(设备文件)  d(目录) l(连接文件) s(socket) p(FIFO)

-perm -mode: 查找至少文件权限, 例如(-perm -0744) 也会把(0755)权限的文件筛选出来

-perm +mode: 查找文件权限"包含任意mode权限"的文件, 例如查找(-perm +0755), 那么(0744)也会被筛选出来

> 其他参数

-exec command: command为其他命令 

-print: 将结果打印到屏幕上(默认)    

```bash
 find ./Learning/apache_sites/K-Logging  -type d -exec ls -l {} \;
```

# 10 Vim程序编辑器

比较常用的命令

1. 连续上/下移30行: 30j/30k
2. 上一页/下一页: `<C-b>`/`<C-f>`
3. 移上半页/移下半页: `<C-u>`/`<C-d>`
4. `+`: 移到下行行首
5. `10<space>`: 光标往右移10个字符
6. `H`: 屏幕最上方第一行的第一个字符
7. `M`: 光标移到中央那行的第一个字符
8. `L`: 屏幕最下方最后一行的第一个字符
9. `G`: 移动到这个文件的最后一行
10. `10G`: 移动到文件的第10行
11. `gg|1G`: 移动到屏幕首行


## 10.3 vim的功能

### 10.3.2 多文件编辑

多窗口下基本命令

1. vim 文件1 文件2: 空格分隔同时打开多个文件
2. `:n/:N`: 编辑下/上一个文件
3. `:files`: 列出目前这个vim的打开的所有文件
4. `sp [filename]`: 若不加filename则再打开多一个本文件,加了则新打开文件
5. `ctrl+w+j/下箭头`: 先按`ctrl+w`,然后放开按键,然后按j/下箭头,到下一个文件
6. `ctrl+w+k/上箭头`: 同上,不过到上一个文件
7. `ctrl+w+q`: 也是先按ctrl+w,然后放开其他键按q,关闭下一个文件

# 11 认识与学习bash

# 11.1 认识bash这个shell

### 11.1.4 bash shell的功能

> 命令记忆能力 (history)

好处: 能知道执行过的命令 方便排查问题

坏处: 黑客进来了 mysql的登入命令写在命令行里..那么黑客也知道了

关于历史记录的几个查询方法

> ~/.bash_history文件

这个文件记录的是前一次登录以前所执行过的命令, 至于这一次登录所执行的命令都被暂存在内存中

当成功注销后 才写入到.bash_history当中

还有另外一个查询历史的用法是 使用history命令

具体可参考 https://linuxtoy.org/archives/history-command-usage-examples.html

> 命令与文件补全功能(Tab按键的好处)

减少打错拼音的可能和提高敲命令的效率

> 命令别名设置功能(alias)

例如可以设置~/.bash_profile

里设置

```bash
alias lm='ls -al'
```

> 作业控制、前台、后台控制(job control, foreground, background)

定时脚本的执行

不怕ctrl+c 中断了进程

> 程序脚本(shell script)

主要运行linux的一些操作集成到一个脚本中

> 通配符(wildcard)

例如使用 `ls -l /usr/bin/X*` 来查询url/bin目录下有多少X开头的文件

### 11.1.5 bash shell的内置命令 type

```bash
> type [-tpa] name

-t 表明命令类型 
    file: 表明为外部命令
    alas: 表明该命令为命令别名设置的名称
    builtin: 表明该命令为bash内置的命令功能

-p 当命令为外部命令时, 才显示完整的文件名

-a 将PATH变量的路径中, 将所有含name的命令都列出来, 包含alias

```

### 11.1.6 命令的执行

命令太长的时候我们会利用`\`来进行换行继续输入命令

```bash
> cp /var/spool/mzk /etc/crontab \
> /etc/fstab /root
```

这个是什么原理呢

因为我们是通过`Enter` 来让当前命令执行

所以我们要把Enter转义掉`\[Enter]`

所以`\`和`[Enter]`中间不能有任何字符 否则按下`[Enter]`时都会执行

## 11.2 什么是变量

### 11.2.2 变量的显示与设置: echo, unset

```bash
> echo $PATH
> echo ${PATH}
```

以上两种方式皆可显示PATH

定义变量

```bash
myname=maizhikun
```

变量的规定

- 变量名称和变量值要用`=`连接, 并且不能左右不能有空格
- 变量名称只有英文字母和数字, 并且数字不能开头
- 变量值有空格的话 需要用引号把内容包起来
- 双引号包住的变量值 可以解析`$PATH`这种变量, 而单引号包住的`$PATH` 只会认为是纯字符
- 不使用引号, 而用`\`将空格等特殊字符转义掉也可
- 变量内容的连接符为`:`
- 若该变量需要在其他子进程执行, 则需要以`export`来使变量变成环境变量
    - `PATH="$PATH:/home/bin"`
    - `export PATH`
- 通常大写变量为系统默认变量, 小写为用户变量
- 取消变量使用 unset, 例如`unset PATH`
- 反单引号的内容会先被执行, 作用和`$()`基本一致

反单引号的引用
```bash
#先执行locate crontab, 然后再ls
> ls -lla `locate crontab `
```

### 11.2.3 环境变量的功能

```bash
# 查看环境变量
> env
PATH=/home/work/.nvm/versions/node/v8.3.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
PWD=/var/mail
LANG=en_US.UTF-8
SHLVL=1
HOME=/home/work
LANGUAGE=en_US:
LC_TERMINAL_VERSION=3.3.7
...
```

```bash
# 输出最近一次命令执行的结果
echo $?
```

export可以将自定义变量转为环境变量, 供子进程读取

# 12 正则表达式与文本格式化处理

需要注意版本的字符格式是啥,不同的字符格式可能会不一样

例如LANG=C: 01234....ABCD....Zabc...z

和LANG=zh_CN: 01234....aAbB.....zZ

的编码顺序不一样, 会导致正则的返回不一样

## 12.2 基础正则表达式

### 12.2.2 grep的一些高级参数

grep分析的是一行,当一行中有我们想要的东西,就把该行抽取出来

`grep [-A] [-B] [-acinv] [--color=auto]`

```bash
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

## 12.2 sed工具

`sed [-nefri] [动作]`

```bash
-n: 使用安静(silent)模式,在一般sed的用法中,所有STDIN的数据一般都会显示在屏幕上,加上`-n`后只有sed特殊处理的那一行(或者操作)才会被列出来
-e: 直接在命令行模式上进行sed的动作编辑,sed后超过两个动作,需要每个动作前都加`-e`
-f: 直接将sed的动作写在一个文件内,-f filename 则可以执行filename内的sed操作
-r: sed的动作支持的是扩张型正则表达式的语法(默认是基础正则表达式语法)
-i: 直接修改读取的文件内容,而不是屏幕输出

动作说明: [n1[,n2]]function
n1,n2: 一般代表选择进行动作的行数,举例来说,如果我的动作是需要在10~20行之间进行的,则"10,20[动作行为]"

function有下面这些参数

a: 新增,a后面可以接字符串,而这些字符串会在新的一行出现(目前的下一行)
c: 替换,c后面可以接字符串,这些字符串可以替换n1,n2之间的行
d: 删除
i: 插入,i后面可以接字符串,而这些字符串会在新的一行出现(当前行的上一行)
p: 打印,也就是将某个选择的数据打印出来,通常p会跟参数sedn -n一起运行
s: 替换,可以直接进行替换工作,通常这个s可以搭配正则表达式
```

sed的查找替换功能

```bash
sed 's/要被替换的字符串/新字符串/g'
```

注意点

1. sed增加两行

    ```bash
    nl /etc/passwd | sed '2a Drink tea...\
    > drik beer'
    ```
2. 替换: `sed 's/要被替换的字符串/新的字符串/g'`

# 13 学习shell script

## 13.1 什么是shell script

一般程序结束时都建议写个`exit 0`返回给操作系统, 然后执行完这个shell, 可以通过`$?`来获取最后一个shell的返回结果



# 14 Linux账号管理与ACL权限设置

## 14.1 Linux的账号与用户组

每个登录的用户至少会有 用户ID(UID) 和 用户组ID(GID)

查看当前系统所有的用户文件在`/etc/passwd`

查看当前系统所有用户组在`/etc/group`

用户的密码表在`/etc/shadow`

### 14.1.2 用户账号

> /etc/passwd 文件说明

```bash
> head -n 4 /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
```

每个用户占一行,以:为分隔符, 可存储7类信息

账号名称:密码:uid:gid:账号说明文案:主文件夹:Shell

其中密码现在都是`x`, 以前Linux用户的密码都放着, 现在就是`x`

uid的号码是有讲究的

- 0是root
- 0~99是distributions自行创建的系统账号
- 100~499是用户有系统账号需求时, 可以使用的账号UID
- 500~65535是可登陆账号

> /etc/shadow 文件说明

```bash
> head -n 4 /etc/shadow
root:*:18255:0:99999:7:::
daemon:*:18113:0:99999:7:::
bin:*:18113:0:99999:7:::
work:$6$j7xxxk....xxxx:17062:0:99999:7:::
```

账号名称:加密的密码:最近更改密码的日期:密码不可被改动的日期:密码需要重新更改的天数:密码需要更改期限前的警告天数:密码过期后的账号宽限时间:账号失效日期:保留字段



- 最近更改密码的日期: 距离1970101到更改密码的天数

根据天数计算准确日期

```bash
#!/usr/bin/env bash
day=17062
seconds=`expr $day \* 86400`
echo `date -d @$seconds`
```

## 16.3 printf
`printf '打印格式' 实际内容`

格式方面的特殊样式

1. `\a` 警告声音输出
2. `\b` 退格键
3. `\f` 清除屏幕
4. `\n` 输出新一行
5. `\r` 即Enter按键
6. `\t` 水平tab按键
7. `\v` 垂直的tab按键
8. `\XNN` NN代表两位数的数字,可以转换数字成为字符

常见的变量格式

1. %ns n是数字,s是string,即多少个字符
2. %ni 那个n是数字,i代表integer,多少整数字数
3. %N.nf 代表N个整数,n个小数的浮点数

## 16.4 awk

`awk '条件类型1{动作1} 条件类型2{动作2} ...' filename`

默认变量
1. $0: 代表整行
2. $1~$n: 代表第n个字段
3. NF: 表示每一行拥有的字段数
4. NR: 代表正在处理的第几行
5. FS: 目前的分割字符,默认是空格键

注意事项

1. 所有awk动作,即在{}内的动作,如果有需要多个命令辅助时,可以用`;`间隔,或者直接以`[Enter]`按键来隔开每个命令,
2. awk的变量无需`$`,直接使用即可
3. awk支付`if`
4. 可用BEGIN和END去掉首尾行

## 16.5文件比较工具

`diff [-bBi] from-file to-file`,以行为单位

`from-file to-file`都可用`-`代替,代表Standar input

1. -b: 忽略一行当中仅有多少个空白的区别
2. -B: 忽略空白行的区别
3. -i: 忽略大小写

`cmp [-s] file1 file2`,以字节为单位

1. -s: 把所有的不同点的字节处都列出来,如果不加`-s`仅输出一个不同点

`patch [-R] [-pn]` 根据补丁,更新/还原

1. -R: 还原
2. -p: 后面的N表示取消几层目录的意思
制作补丁`diff -Naur 旧文件 新文件 > 补丁.patch`

## 16.6 pr文件打印准备

# 16.例行性工作

## 16.1 任务分类

1. 仅执行一次的命令: at设置只执行一次的命令(需要atd服务的支持)
2. 常规性命令: crontab循环性指令(需要crond服务)

## 16.2 仅执行一次的工作调度

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

```bash
crontab [-u username] [-l|-e|-r]

-u: 只有root才能执行/帮其他用户新建/删除crontab
-e: 编辑crontab,指定编辑器`EDITOR=vim crontab -e`
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





