**说明: 本博客仅用于自己方便查看,转自[Git常用的操作]([(14条消息) Git常用的操作_M-Ellen的博客-CSDN博客_git常用操作](https://blog.csdn.net/pzm1993/article/details/79980258))**

# Git常用的操作

## 前言
目前来说，版本控制主要分为：集中式版本控制（Centralized Version Control Systems，简称 CVCS）和分布式版本控制，（Distributed Version Control System，简称 DVCS）。

CVCS的代表主要有CVS、SVN 以及 Perforce 等； 

DVCS主要有 Git、Mercurial、Bazaar 以及 Darcs 等。我们平时比较常用的就是SVN和Git。

集中式与分布式争议很多，各有特色，这里就多说了。目前公司内部2种多有使用。

如想了解SVN的简单使用，可以查看：SVN的介绍与使用流程  。接下就开始介绍Git的简单操作使用。以下主要对官方文档以及其他文档的总结，如果需要查看详细官方文档，可以跳转到这里：https://git-scm.com/book/zh/v2

## 要知道的概念
### 工作区域的三种状态
Git 有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）。由此引入 Git 项目的三个工作区域的概念：Git 仓库、工作目录以及暂存区域，如下图所示。

 

Git 仓库：是 Git 用来保存项目的元数据和对象数据库的地方。 这其它计算机克隆仓库时，拷贝的就是这里的数据。

工作目录：是对项目的某个版本独立提取出来的内容。这些从 Git 仓库提取出来的文件，放在磁盘上供你使用或修改。

暂存区域：是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 有时候也被称作“索引“，不过一般说法还是叫暂存区域。

三种工作区域和三种文件状态关系

如果 Git 目录中保存着的特定版本文件，就属于已提交状态； 如果作了修改并已放入暂存区域，就属于已暂存状态； 如果自上次取出到工作目录，作了修改但还没有放到暂存区域，就是已修改状态。

Git 的基本工作流程如下：

其实上面的流程图，也是在实际工作中的大体流程，简介的概括如下：

在工作目录中修改文件。
暂存文件，将文件的快照放入暂存区域；
提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。
Git 保证完整性
Git 中所有数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。  若你在传送过程中丢失信息或损坏文件，Git 就能发现。

用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。这是一个由 40 个十六进制字符（0-9 和 a-f）组成字符串，基于 Git 中文件的内容或目录结构计算出来；如：

 

24b9da6552252987aa493b52f8696cd6d3b00373
Git 数据库中保存的信息都是以文件内容的哈希值来索引，即commit id ，而不是文件名。如：

git show 24b9da6552
这里指定了commit id，则相当于指定了这次提交的记录。而且不一定写入整个id，一般需要6-8位则可以确定。

文件的生命周期

工作目录下的每一个文件都不外乎这两种状态：已跟踪或未跟踪。

已跟踪的文件：是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，它们的状态可能处于未修改，已修改或已放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。

未跟踪文件：工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有放入暂存区。 

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 我们逐步将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复。所以使用 Git 时文件的生命周期如下：



 

可以通过git status 查看文件的当前状态，简单介绍下每个状态：

1.Untracked：未纳入版本库中的文件，即未跟踪；

2.Unmodified：已纳入版本库的文件，未做修改；

3.Modified：已纳入版本库的文件，已修改；

4.Staged：添加到缓存区的文件；

整体的流程大致是这样子，如果没看懂也没事。现有个印象，学习完实践之后，就会觉得so easy！接下里就介绍流程中会使用到的命令。

## Git指令的使用
### 帮助文档
若你使用 Git 时需要获取帮助，有四种方法可以找到 Git 命令的使用手册：

$git help
$ git help <verb>
$ git <verb> --help
$ man git-<verb>    //man是Linux的查询指令，试了下该指令在git的控制端没有效果，需在Linux服务器 

### 仓库的初始化  
git init

该命令将创建一个名为 .git 的隐藏文件，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。仓库初始化之后，才能执行其他命令，不然会报错。

git clone

如果直接从某个远程仓库拷贝，那么就可以使用该命令。比如在GitHub想拷贝一个demo，如：

$ git clone https://github.com/libgit2/libgit2
命令格式是 ：git clone [url]

如果想拷贝在指定的目录：git clone [url]  [pathName]

添加/暂存 文件

git add 

 作用是跟踪的新文件、已修改的文件添加到暂存区。该命令是对内容快照的缓存，不是针对文件（与SVN不同），所以每次修改多需要执行该指令才能将此次修改添加到暂存区。

git add -u : 提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)。
git add .  ：提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件。
git add -A : 提交所有变化，包括以上2种。
如果需要选择性暂存文件，那么可以将这个文件连缀在后面，用空格隔开：

git add <file1> <file2> <file3>

### 查看文件状态
git status

显示工作目录的状态，不带参数执行，输出内容很详细。并且根据文件是否暂存，会预示下一步的指令操作。

如果想简洁一点，那么加个--short （-s）参数：git status -s

 

### 提交文件
git commit                  

将文件添加到暂存区之后，就可以开始提交了。每次提交之前，一般先再次检查文件状态git status，看是否还有文件未添加到暂存区。一般执行提交是：

git commit -m <commit log>  使用-m 参数 ，附带简明提交说明信息。
如果直接执行git commit，启动文本编辑器以便输入本次提交的说明。

问题：是不是所有的修改多必须git add后才能提交？

并不是，Git也提供跳过暂存区提交，如果觉得烦琐，可以直接不执行git add提交，直接执行以下指令：  

git commit -a -m <commit log>  
但是，只针对已经跟踪过的文件，如果是新建一个文件，是不会提交的。所以我本人还是比较少用。

 

### 移除文件
git rm 

从git中将已跟踪的文件从工作目录、暂存区移除，注意是已跟踪的。如果该文件又是已修改的，可以使用参数 -f 强制删除。

如果移除未跟踪的文件，或者只在工作目录移除，在暂存区继续保留，那么可以执行：

rm  <file>  
如果只移除暂存区的文件，但不移除工作目录文件，如.gitignore 文件，可以使用使用 --cached 选项（（Git 1.6.1 及更高版本还允许使用 git diff --staged，后面还会用到）

### git rm --cached  <file> 
移动文件
git mv

从git中将已跟踪的文件重新命名，或者将文件从一个目录移动到另一个目录。如：

git mv README.md  README     // 将README.md 重新命名 README
git mv README    test/       //将 README文件 移动到test目录下
其实，git mv，相当于执行以下三条命令：

$ mv README.md README
$ git rm README.md
$ git add README
他们的效果是一样的，git一样会记录这是一次改名。

### 查看文件差异
#### git diff 
git status 命令输出的只是大体的差异，要知道具体修改了什么地方，可以用 git diff 命令。

如果不加参数执行，那么显示差异是当前目录下所有未暂存的文件，比较的是工作目录中文件和暂存区域快照之间的差异。

 

如果查看暂存区与仓库之间的差异，还是用到 --cached 选项:

git diff  --cached
所以这2条命令是有区别的，有时你执行git diff 什么也没有，那是因为你已经添加在暂存区了，嘎嘎！

 

如果要查看指定的文件差异，那么后面直接添加文件路径即可：

git diff README.md  
如果要查看2个指定版本的差异，那吗后面可以添加这个2版的commit id，还有添加路径，查看指定的文件：

git diff <commitId_1> <commitId_2>  [path]
### 查看历史记录 
git log  

查询记录最常用到，而且参数也非常多：

git log [<options>] [<since>..<until>] [[--] <path>...]

不加参数执行，会按提交时间列出所有的更新，最近一次更新排在最上面，列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

git reflog： 查看历史提交记录，包括你没有更新的提交

有时历史记录那么多，怎么才能又快又精准找到呢？这里有太多参数可以供选择了：

 -p  用来详细显示每次提交的内容差异，显示与git diff 类似。
--stat   用来简略显示每次提交的统计信息，
--pretty  使用其他格式显示历史提交信息。
比如：git log --pretty=oneline ：将每个提交放在一行显示，查看的提交数很大时非常有用。可用的选项包括 short，medium，full，fuller ，email 和 format（后跟指定格式），展示的信息或多或少有些不同，自己可以动手实践一下看看效果如何。

--graph   显示 ASCII 图形表示的分支合并历史。一般结合oneline ，format使用时尤其有用，如查看分支提交记录：

git log --graph --pretty=oneline --abbrev-commit   
-n  仅显示最近的 n 条提交 ，其中的 n 可以是任何整数
--author 显示指定作者的提交
--grep  仅显示提交说明中含指定关键字的提交
--since / --after   仅显示指定时间之后的提交，如：git log --since “1 day ago”
--until / --before    仅显示指定时间之前的提交 如：git log --until “2018-2-1”
<path>  查看当前某些文件或目录的提交
.....
其他选项可以通过 git log --help 查看

 

一般，以上这些选项多是可以组合使用，比如：如果要查看 Git 仓库中，2018 年 1个月期间，jack 提交的历史记录，可以用下面的查询命令：

 

git log --pretty=oneline    --author="jack" --since="2018-3-1" --until="2018-4-1"

### 撤销修改

使用 git reset撤销

git reset --hard   HEAD~：  将本地仓库、暂存区、工作目录恢复到上一个版本（所有的修改将会失去）

git reset --mixed HEAD~：将本地仓库、暂存区恢复到上一个版本，工作目录保存着修改

git reset --soft HEAD~：将本地仓库、上一个版本，暂存区、工作目录保存着修改

git reset HEAD~2 <path>： 带文件路径，默认是--mixed，只将暂存区，路径path下的文件恢复到之前2个版本



git checkout -- <file>... ：  撤销工作区中已修改的文件

git commit  --amend ：覆盖上一次的提交。

比如还有文件未添加，或者需要重新修改提交信息，如：

$ git commit -m "initial commit"
$ git add README.md
$ git commit --amend  -m“initial commit +1”
最终只会有一个提交 ，第二次提交将代替第一次提交的结果。

 

### 远程仓库使用


远程仓库是指托管在因特网或其他网络中的项目的版本库。其常使用的指令有如下：

git clone <url>   克隆远程仓库到本地
git remote  列出每个远程仓库的简短名字
git remote -v    列出每个远程仓库的简短名字与其对应的 URL
git remote show [remote-name]   查看某个远程仓库的详细信息
git remote rename [old name] [new name]  重命名远程仓库
git remote rm [remote-name]   移除某个远程仓库
git remote add <shortname> <url>  添加一个远程仓库
git fetch [remote-name]  从远程仓库数据拉取最新到本地，但不自动合并本地的修改
git  pull   [remote-name] [branch-name]  把远程仓库数据拉到本地，并自行合并
git pull 的魔法经常令人困惑所以通常单独显式地使用 fetch 与 merge 命令会更好一些。
git  push [remote-name] [branch-name]    把本地代码推送到远程仓库，一般先执行git pull、在执行git push  确保代码是最新的，不然会被拒绝。

***如果git pull提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，用命令：

git branch --set-upstream branch-name origin/branch-name

### 打标签
比较常用在发布版本的时候，比如当前软件第一次发布，那么就可以在发布的版本上打上“v1.0”标签。无论在什么时候，取出“v1.0”标签，对应的一定是第一次发布的版本，他是不会变，与分支不同。

git tag   <tagname>    给版本添加标签

git tag        按字母顺序直观列出所有标签
git tag -l <tagname>   简洁列出某个标签
git show <tagname>   详细列出某个标签，显示包括打标签者的信息、打标签的日期时间、附注信息，然后显示具体的提交信息。

 

git tag -a <tagname>  <commit id>  给之前的版本添加标签
git tag -d <tagname>    可以删除一个本地标签；
git push origin <tagname>  将某个本地标签推到远程，共享标签
git push origin --tags           将所有的本地分支推到远程；
git checkout -b [branchname] [tagname]     检出标签，在特定的标签上创建一个新分支
git push origin :refs/tags/<tagname>  删除某个远程的标签，要删除远程标签，必须要先删除本地

标签不是按时间顺序列出，而是按字母排序的

Git 使用两种主要类型的标签：

附注标签（annotated）：附注标签是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息。如：

git tag -a v0.1  -m "version 0.1 released"   其中，用-a指定标签名， -m指定说明文字，还可以使用 -s 给私钥签名
轻量标签（lightweight）：很像一个不会改变的分支，本质上是将提交校验和存储到一个文件中 ， 没有保存任何其他信息，只是一个特定提交的引用。如果只是想用一个临时的标签，则一般使用轻量标签。不需要使用 -a、-s 或 -m 选项，只需要提供标签名字，如：

git tag  v1.0    给版本添加标签为v1.0

### 分支（分支是重点，建议查看官方文档，非常详细易懂）
 Git 的分支模型称为它的“必杀技特性”，也正因为这一特性，使得 Git 从众多版本控制系统中脱颖而出。 Git 处理分支的方式可谓是难以置信的轻量，创建新分支这一操作几乎能在瞬间完成，并且在不同分支之间的切换操作也是一样便捷。 （分支是重点，建议查看官方文档，非常详细易懂）

git branch   查看分支（当前工作分支前面会标一个*号）
git branch -v  查看每一个分支的最后一次提交
git branch -vv  查看每一个分支的详细信息，如每一个分支正在跟踪哪个远程分支与本地分支是否是领先、落后
git show-branch   详细查看的分支记录

git  branch <branchname>       创建分支， HEAD 的特殊指针也会移到当前分支
git checkout  <branchname>   切换分支
git checkout  -b <branchname> 创建分支，并切换到该分支，即合并上面2步  

git mergr  <branchname> ：合并分支，如果需要合并到master分支，那么需要先切换到master分支，再进行整合 (该合并分支，是Fast forward模式，在服务器中是没有记录的)
git merge --no-ff -m "merge with no-ff" <branchname>     合并分支（禁用Fast forward模式，能看到分支记录）

git branch --merged   查看已经合并到当前分支的分支。 
git branch --no-merged  查看尚未合并到当前分支的分支。 
git branch -d  <branchname>        删除已经合并的分支
git branch -D  <branchname>      可强制删除尚未合并的分支 
git push origin --delete serverfix    删除某个远程分支

git checkout -m <branchname>  将本地的修改加入到新的分支上

git checkout -b branch-name  origin/branch-name 在本地创建和远程分支对应的分支，本地和远程分支的名称最好一致

在合并的时候，你应该注意到了"快进（fast-forward）"这个词。 由于当前 master 分支所指向的提交是你当前提交的直接上游，所以 Git 只是简单的将指针向前移动。 换句话说，当你试图合并两个分支时，如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”。

 

### 储藏与清除
问题：如果在其他分支开发，突然有个更紧急的bug需要修复，怎么办？
此时只要切回master分支，继续其他分支开发就可以。但是，要留意你的工作目录和暂存区里那些还没有被提交的修改，它可能会和你即将检出的分支产生冲突从而阻止 Git 切换到该分支。 最好的方法是，在你切换分支之前，保持好一个干净的状态。 这里有2个方向：将修改的文件储藏起来；将修改的文件清除。

 

 

#### 储藏文件

指的是在当前分支先将目前修改的文件保存起来（保存进度（stashing）），完成任务之后再回到该分支恢复进度（apply）。那么相关的指令有如下：
git stash  将当前工作目录已跟踪的文件储藏起来，如同时储藏将未跟踪的文件，可以添加参数--include-untracked （-u ）
git stash list  查看储藏列表
git stash apply   将最近一次储藏恢复到工作目录，但是恢复后，储藏内容并不删除
git stash apply stash@{<num>}   恢复某个指定的缓存
git stash drop   stash@{<num>}   将某个缓存从列表中移除
git stash pop   恢复最近一次缓存，并立即从列表上移除

 

 

git stash branch <branchname>  从储藏中创建一个分支

 

#### 清除文件

对于工作目录中一些工作或文件，你想做的也许不是储藏而是移除，那么就有以下指令：
git clean    移除未跟踪文件（不包括忽略的文件），恢复不了
git clean  -d -f  移除工作目录中所有未追踪的文件以及空的子目录
git clean  -d -x    移除未跟踪文件（包括忽略的文件）
git clean  -d  -n  预示将要移除什么文件，但还未移除

git clean   -i      交互式预示将要移除什么文件，但还未移除

git stash --all   将移除每一样东西并存放在栈中

### 搜索
git grep <tag>   搜索当前目录下的字符串
git grep -n <tag>  将搜索的字符串添加行号显示
git grep --count  <tag>  只显示当期目录匹配的个数
git grep -p <tag>  匹配字符串是属于哪一个方法或者函数，后面还可以添加文件类型，如git grep -p gmtime_r *.c
git log -S[tag]    显示新增和删除该字符串的提交   如：git log -SZLIB_BUF_MAX --oneline
git log -L   查看函数或者一行的提交记录，如：git log -L :git_deflate_bound:zlib.c

### Git的配置
Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

 

/etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的 git config 时，它会从此文件读写配置变量。
~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 可以传递 --global 选项让 Git 读写此文件。
当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：针对该仓库。
每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。

要想获得 config 命令的手册，执行 ：git help config  

配置用户信息

安装完之后，我们一般多是需要配置自己的邮箱与用户名，那么就可以：

$ git config --global user.name  John Doe
$ git config --global user.email   johndoe@example.com
这里使用了 --global参数，那么所有的仓库都将使用该信息。如果你想对特定的仓库使用不同的信息，那么在该仓库目录下执行没有带 --global 参数的命令。


配置编辑器

一般Git默认使用的是Vim，这也是我们经常使用的编辑器。但如果你想使用不同的文本编辑器，例如 Emacs，可以这样做：

$ git config --global core.editor emacs
配置简写

有些指令已经练到信手拈来，但无奈指令太长了，写太多感觉会走火入魔。还好Git可以将指令设置别名，格式：

git config --global  alias.<别名> <指令名>   
 如： 

$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
将一些复杂的指令，也配置一下，如：

$ git config --global alias.unstage “reset HEAD --”
设置该别名之后，那么以下2条命令是等同的：

$ git unstage fileA
$ git reset HEAD -- fileA
丧心病狂者还有这种操作：

git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
原来git log是这样子，现在变成这样子了，简直不要不要的。

如果哪一天，你太牛逼，牛逼到没有朋友。想回到从前平淡的生活，删除这些设置也是灰常容易的。找到配置文件，删除这些别名即可。

最后你可以检查你的配置，可以使用 ：

git config --list  ：命令来列出所有 Git 当时能找到的配置；
git config <key> ：检查 Git 的某一项配置
### 忽略文件
在编译过程中创建的临时文件，或者不想纳入版本控制的文件，就可以添加到名为 .gitignore 的文件。一般git clone的仓库是带有这个文件的，如果没有，可以自行创建：touch .gitignore。创建之后，建议将.gitignore提交，这样以后就比较方便了。

文件 .gitignore 的格式规范如下：

 

所有空行或者以 ＃ 开头的行都会被 Git 忽略。
可以使用标准的 glob 模式匹配。
匹配模式可以以（/）开头防止递归。
匹配模式可以以（/）结尾指定目录。
要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。
所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 比如：星号（*）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符。使用Linux指令的，应该就比较熟悉了，这里不介绍了。
所有不同开发的配置文件可以直接在线浏览：https://github.com/github/gitignore
一般拷贝过来直接用就可以了，当然也可以自己再添加
比如，一般在android下使用的忽略文件：
```

# Built application files
*.apk
*.ap_

# Files for the ART/Dalvik VM
*.dex

# Java class files
*.class

# Generated files
bin/
gen/
out/

# Gradle files
.gradle/
build/

# Local configuration file (sdk path, etc)
local.properties

# Proguard folder generated by Eclipse
proguard/

# Log Files
*.log

# Android Studio Navigation editor temp files
.navigation/

# Android Studio captures folder
captures/

# IntelliJ
*.iml
.idea/workspace.xml
.idea/tasks.xml
.idea/gradle.xml
.idea/assetWizardSettings.xml
.idea/dictionaries
.idea/libraries
.idea/caches

# Keystore files
# Uncomment the following line if you do not want to check your keystore files in.
#*.jks

# External native build folder generated in Android Studio 2.2 and later
.externalNativeBuild

# Google Services (e.g. APIs or Firebase)
google-services.json

# Freeline
freeline.py
freeline/
freeline_project_description.json

# fastlane
fastlane/report.xml
fastlane/Preview.html
fastlane/screenshots
fastlane/test_output
fastlane/readme.md

```

### 分支策略
在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

你和你的小伙伴们每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样：

 

如果你需要一个更加完美的分支策略，那么这里有个阿里巴巴分享的：

[阿里巴巴的分支结构](http://mp.weixin.qq.com/s?__biz=MzIzOTU0NTQ0MA==&mid=2247487231&idx=1&sn=8cce3158336969343762f8c98f9e4651&chksm=e92933f0de5ebae678988ea8928f4751a30bf1585e98e7c8b57868fa5d23f131e934bfe68f13&mpshare=1&scene=1&srcid=0418puqRmtNvFlpM5EjDwvnl#rd_)

### 分布式工作流程
Git 的分布式协作可以为你的项目和团队衍生出种种不同的工作流程，比较常见有以下3种： 

你可以选择使用其中的某一种，或者将它们的特性混合搭配使用。

#### 1.集中式工作流

一个中心集线器，或者说仓库，所有人将自己的工作与之同步。 若干个开发者则作为节点，并且与其进行同步。这和 Subversion 中的概念一样，这个模式也可以很好地运用到 Git 中。

 只需要搭建好一个中心仓库，并给开发团队中的每个人推送数据的权限，就可以开展工作了。Git 不会让用户覆盖彼此的修改。这种模式的工作流程的使用非常广泛，因为大多数人对其很熟悉也很习惯。

 



 

 

#### 2.集成管理者工作流

 

 

这是 GitHub 和 GitLab 等集线器式（hub-based）工具最常用的工作流程。

如果要为这个项目做贡献，你需要从该项目克隆出一个自己的公开仓库，然后将自己的修改推送上去。 接着你可以请求官方仓库的维护者拉取更新合并到主项目。 维护者可以将你的仓库作为远程仓库添加进来，在本地测试你的变更，将其合并入他们的分支并推送回官方仓库。 这一流程的工作方式如下所示：

项目维护者推送到主仓库。
贡献者克隆此仓库，做出修改。
贡献者将数据推送到自己的公开仓库。
贡献者给维护者发送邮件，请求拉取自己的更新。
维护者在自己本地的仓库中，将贡献者的仓库加为远程仓库并合并修改。
维护者将合并后的修改推送到主仓库。


这么做最主要的优点之一是你可以持续地工作，而主仓库的维护者可以随时拉取你的修改。 贡献者不必等待维护者处理完提交的更新，每一方都可以按照自己节奏工作。

#### 3.司令官与副官工作流

这种工作流程并不常用，只有当项目极为庞杂，或者需要多级别管理时，才会体现出优势。例如著名的 Linux 内核项目。本人研究下了也搞不懂，这里就不介绍，减少负 及。

