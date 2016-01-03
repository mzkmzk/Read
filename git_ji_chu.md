# 基础

##1 Git的优势

1. `SVN`和`Git`,因为`SVN`要是主机挂了.所有记录都GG了.而`Git`是分布式的.而且`Git`平台比较多,`GitLab`,`coding.net`,`GitHab`等等

2. `Git`可以现在本地执行,可以在本地进行`commit`,而`SVN`每次`commit`都必须是提交到主机.(虽然笔者推荐多push,但是可以本地`commit`有时也是不错的,主要是保证本地能有备份)
3. `Git`能保证数据完整性,Git中所有数据在存储前都计算校验和,然后以校验来引用,所以你在传送过程中丢失文件,Git都知道(笔者试过一个push断了4次,但是最后还是每次增量的提交了上去.)

##2. Git和SVN存储方式的不同

SVN :

![SVN存储方式](QQ20160103-0.png)

Git :

![Git存储方式](QQ20160103-1.png)

注意`Git`的存储图中,外面有间隔线的表明数据没有变化,存的只是索引.

SVN是差异比较,每个版本的文件依赖于上一个版本.

Git的每个版本的文件是独立存在的,是直接记录快照.



##3. Git的三个工作区域和三种状态

![Git三个工作区域](QQ20160103-2.png)

三个工作区域 :

1. 工作目录 : 我们从Git仓库提取出来的文件,正在本地修改的目录
2. 暂存区域 : 是一个文件,保存下次将要提交的文件信息列表
3. Git仓库  : 保存项目元数据和对象数据库的地方


三种状态:

1. 已提交 : 如果Git仓库保存着特定版本文件,就属于已提交状态(commit)
2. 已修改 : 自上一次取出来修改了,但还没放入暂存区(add)
3. 已暂存 : 如果作了修改并放入暂存区域(add),就属于已暂存. 

![未/已跟踪](QQ20160103-3.png)

其实还有二种形式可分
1. 未跟踪 : 在本地未`git add`的就是未跟踪的.
2. 已跟踪 : 上述三种状态都是已跟踪状态.

以上状态都可以通过`git status`查看 or 紧凑一点的`git status -s`


##4. 安装Git

    //ubuntu
    apt-get install git
    
其他版本系统 :<http://git-scm.com/downloads>   

##5. Git配置

1. 全局系统配置 : `/etc/gitconfig` git config --system ...
2. 用户`~/.gitconfig` or `~/.config/git/config` git config --global ...
3. 当前项目 : ./git/config

一开始设置用户名和邮箱

    $ git config --global user.name "404_K"
    $ git config --global user.email 404_K@example.com
    
更改git编译器

    git config --global core.editor 命令设定你喜欢的编辑软件
    
详情输入`git config`查看 or `git help config`

##6 获取Git仓库

1. 初始化仓库

    1. git init : 创建.git目录初始化git
    2. git add . 添加当前目录到git中.
    3. git commit -m "first commit" 放入暂存区中.
2. 克隆现有仓库

    `git clone https://github.com/mzkmzk/Read.git [重命名本地目录]
`

##7 Git 基础

##7.1 .gitignore

1. 所有空行或者以 ＃ 开头的行都会被 Git 忽略。

2. 可以使用标准的 glob 模式(简化的正则)匹配。
 
3. 匹配模式可以以（/）开头防止递归。

4. 匹配模式可以以（/）结尾指定目录。

5. 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反.

详细可参考<https://github.com/github/gitignore>

##7.2 git diff 

1. git diff : 查看尚未暂存的文件更新了哪些部分,比较工作目录和暂存区的差异
2. git diff --staged 查看已暂存的下次将要提交的.

##7.2 git commit 

1. git commit : 进入编译器,里面包含更改信息
2. git commit -m "内容" :直接提交内容,不进入编译器
3. git commit -a : 提交所有已跟踪的文件,包括未add到缓存区的.(但不会提交未跟踪的)

##7.3 git rm

要从Git移除文件,必须git rm 掉已跟踪文件,并且commit到缓存区.并且在工作目录中删除指定文件.

会有几种情况

1. 删除之前修改过并且放到暂存区的话,则必须 `git rm -f 文件`强制删除.为了防止删除未add到缓存区的文件.
2. 当你想缓存区和仓库都删除了某目录,但工作区保留 : `git rm --cached 目录/`

git rm 后面可跟`glob`

删除log下后缀为log的文件

    git rm log/\*.log
    
为何要加\,因为不需要shell帮忙,git自行解析

git rm *~ ,所以~结尾的文件.

##7.4 git mv

git mv README.md README 

无论通过命名还是图形改名字,git都能检测出来

以上语句git会自动执行

    mv README.md README
    git rm RADME.md
    git add README

##7.5 git log

`git log`参数说明

| 选项            | 说明                             |
|-----------------|----------------------------------|
| -p              | 显示更新差异                     |
| --stat          | 显示修改统计信息                 |
| --shortstat     | 只显示--stat中最后的行数修改统计 |
| --name-only     | 显示已修改文件清单               |
| --name-status   | 显示新增,修改和删除的文件清单    |
| --abbrev-commit | 只显示SHA-1的前几个字符          |
| --relative-date | 使用较短的时间显示               |
| --graph         | 图形显示分支合并历史             |
| --pretty        | 使用自定义格式显示历史提交信息   |

git log 限制输出

| 选项        | 说明                              |
|-------------|-----------------------------------|
| -n          | 仅显示n条提交                     |
| --after     | 显示指定时间之后的提交            |
| --before    | 显示指定时间之前的提交            |
| --author    | 只显示该作者                      |
| --committer | 只显示该提交者                    |
| --grep      | 显示关键字                        |
| -S          | 只显示添加/删除了某个关键字的提交 |

##7.6 撤销操作

1. 当你先commit了一次,后面发现少add了一些文件/commit的信息写错了

        git commit -m  "first"
        git add other.js
        git commit --amend
        
2. 当已经`commit`但想撤回提交到缓冲区.
    
    `git reset HEAD 文件名`
        404-K:My_Website maizhikun$ git add .
        404-K:My_Website maizhikun$ git status
        On branch master
        Your branch is ahead of 'origin/master' by 7 commits.
          (use "git push" to publish your local commits)
        Changes to be committed:
          (use "git reset HEAD <file>..." to unstage)
        
        	modified:   REAMDME.md

        404-K:My_Website maizhikun$ git reset HEAD REAMDME.md
        Unstaged changes after reset:
        M	REAMDME.md

3. 想撤销工作区间的操作,

    `git checkout -- README.md`,注意这会把git仓库中的文件覆盖掉你本地的文件.

##7.7 git remote

1. git remote -v 可以看到当前远程分支的名字和url
2. git remote add <分支名称> url : 添加分支
3. 
