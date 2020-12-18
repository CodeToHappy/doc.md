[Git 学习文档 网络来源](https://www.liaoxuefeng.com/wiki/896043488029600/900003767775424)

[TOC]

## 1. 简介

Git是一个分布式版本控制系统。
1. 免费开源
2. 可以追踪每次文件增删改，很容易恢复到你想要恢复的版本。联网后，能迅速将本地文件更新到远程仓库的最新文件版本。
4. 从远程仓库克隆下来后，本地有一个完整的版本库，工作时不需要联网。即使电脑坏了也能从网络上直接克隆一份，增加了稳定性。
5. 工作完成时，直接用命令提交；可以与同事的代码进行冲突对照，解决冲突后完成提交。
6. ...

## 2. 安装

1. Linux
- 打开Bash尝试输入`git`，查看系统是否已经安装Git
- 没有就安装它：
```bash
sudo apt-get install git

yum -y install git
```
- 以上两种方式选择一种安装即可。
- 若上述两种均不能成功，选择下面的傻瓜式安装法。

2. Mac OS
- 打开终端输入`git`检查是否安装

- 未安装git需要安装 homebrew，然后直接用homrbrew命令下载
```bash
brew install git
```
- App Store 中搜索 Xcode 下载并安装。
- Xcode集成了Git，不过默认没有安装，你需要运行Xcode，选择菜单`Xcode`-> `Preferences`，在弹出窗口中找到`Downloads`，选择`Command Line Tools`，点`Install`就可以完成安装了。

3. Windows
- `https://git-scm.com/downloads` 下载即可

4. 全系统傻瓜式教学
- `https://git-scm.com/downloads`里面有Linux，Mac 和 Windows的安装程序。

5. 验证是否安装成功
- Windows：`windows键`+`R`，输入`cmd`进入命令行界面。
- Mac OS：手势进入 launch（展示所有安装好的程序界面），在第一面找到工具并打开就能看到 Terminal 终端，点开就能看到命令行界面。
- 使用 Linux 的选手默认会打开 bash ，就不介绍了。
- 每个界面虽然系统不同，但是 Git 的命令是相同的。
```bash
git --version
```
	- 如果回车显示 Git 当前版本（与你下载时候看到的版本一致），即为安装成功。
	- 安装完成后，可以输入`git`来查看可以输入的命令参数有哪些。看得懂的小伙伴可以自行尝试。

## 1. 创建你的 Git 工作目录
- 图形界面创建空文件夹
	- 请动用鼠标创建。操作不做介绍。双击进入文件夹。
- 命令行创建空文件夹(这里目录名字不一定是Git，换成任何你想要的文件夹的名字)
```bash
mkdir Git
```
	- 改变当前路径到`Git`文件夹下
	- 查看当前目录的绝对路径
	- pwd：print working directory 打印正在工作的目录
```bash
cd Git
pwd
```

## 2. 初始化 Git 文件夹为Git仓库
- 通过命令行实现
- init: inialization 初始化
```bash
git init
```

## 3. 验证身份
- 如果没有验证身份，你会遇到如下情况
```bash
*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

  to set your account's default identity.
  Omit --global to set the identity only in this repository.
```
- 解决方案
	- 供 Git 分析你是否有权限上传文件到目标仓库
	- `Your Name`因为我使用的Github，所以这里我填写Github账户的`username`
	- `email@example.com`我填写的是Github账户的`email`地址。
	- config: configuration 配置信息
	- global: 全局。这里用--global说明在你电脑上任何用户或者文件夹所在目录都可以使用你的`username`和`email`地址进行认证。如果需要限定文件夹/不同的账户认证，这时候的你应该已经会自己动手，丰衣足食了。
```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

## 4. 检查 Git 文件夹下的内容
- Mac/Linux
```bash
ls -a
```
	- 显示`.git`文件。默认隐藏，先不要管他。
- Windows
	-最底下任务栏中间左侧有一个文件夹，打开后在页面左上方点击“查看”，接着在右侧找到“隐藏的项目”点击勾上就能看到文件夹下所有隐藏的文件了。

## 5. 在 Git 目录下新建一个Markdown文档试试
- Mac/Linux（`test`可改成你想要的名字）
```bash
vim test.md
```
- 在`Vim`界面不要慌，按`i`键进入 insert 插入模式即可正常打字
- 我输入了“Something is fun...”, 你们可以改成其他内容。
- 写完之后，按键盘左上方的`ESC`键一次，再按`shift`+`;`（这两个合起来其实就是我们想要的`:`)，输入`wq`退出。
- `wq`: write and quit 强制性写入文件并退出。
- 如果是`ESC`+`:q!`就是直接退出，不保存本次修改。

- Windows（一定要确保文件新建在`Git`文件夹下）
	- 鼠标：右击-新建-文本文档
	- 文档一定要看得到`.txt`后缀，将`txt`修改成`md`
	- 前面的`新建文本文档`可改成我写的`test`也可以改成你想要的名字
	- 文档内容写入“Something is fun...”也可以写你自己想写的。

## 6. 检查 Git 仓库下，所有文件的状态
- 在命令行界面输入
- status: 状态
```bash
git status
```
- 返回结果很人性化的告诉我可以使用`git add <file>...`命令将我修改的文件加入本地仓库
```bash
Untracked files:
  (use "git add <file>..." to include in what will be committed)
		test.md

nothing added to commit but untracked files present (use "git add" to track) 
```

## 7. 将修改文件加入缓存区
- 将文件`test.md`加到缓存区
```bash
git add test.md
```
- 若需要加入多个文件，只需要在后面继续加入你想要加的文件名（中间用空格分开即可）
- 若想要加入所有修改好的文件，不需要填写任何文件名，用`.`点替代即可。

## 8. 填写信息告诉 main / master 分支新增/修改了什么
```bash
git commit -m "Create a Markdown file: test.md"
```
- 返回结果：1个文件改动，新增一行
```bash
[master (root-commit) 92cfcec] Create a new markdown file: test.md
  1 file changed, 1 insertion(+)
  create mode 100644 test.md
```

## 9. 继续在文件下新增一些东西，检查两个版本的不同

```bash
vim test.md
```
- 我修改为“Something is not fun...”

检查不同
- diff: difference 不同
```bash
git diff
```
返回结果(很明了不是吗 :D)
```bash
diff --git a/test.md b/test.md
index 8c9ac28..7fbd83b 100644
--- a/test.md
+++ b/test.md
@@ -1 +1 @@
-> something is fun...
+something is not fun...
```
提交修改和新增文件是一样的
```bash
git add test.md
git commit -m "Modify file: test.md"
```
最后检查一下
```bash
git status
```
返回结果(clean)
```bash
On branch master
nothing to commit, working tree clean
```

## 10. 检查历史版本
- log: (某时期事件的)正式记录，日志
```bash
git log
```
返回结果(按照时间后先的顺序进行打印)
```bash
commit 136418cf00c8ea49d127b447ea6548aa0543c985 (HEAD -> master)
Author: your_name
Date:   Tue Nov 24 14:01:57 2020 +0800

	 Modify file:test.md #这是我们第二次提交我们修改之后的文件

commit 92cfcec8aece64dab7e7c6977642757578d1166b
Author: your_name
Date:   Tue Nov 24 13:40:11 2020 +0800
	 
	 Create a new markdown file: test.md #这是我们第一次提交我们的test.md文件
```

## 11. 回退版本
- Git 中参数`HEAD`表示当前版本，`HEAD^`之前一个版本，`HEAD^^`再之前一个版本。也可以写成`HEAD~n`(n为你想要回退几个版本)
- `HEAD`默认是指向`main`/`master`（老版本）分支
- reset: 重置
- HEAD：可以把他当作一个指向`main`分支的指针，后面跟你想要修改的版本
```bash
git reset --hard HEAD~1
```
返回结果：
- 92cfcec是我第一次提交commit时，它给出来的版本信息。
```bash
HEAD is now at 92cfcec Create a new markdown file: test.md
```
注意，当你回退到某一个版本，最新的版本会被删除。
```bash
git log
```
返回结果：
```bash
commit 92cfcec8aece64dab7e7c6977642757578d1166b (HEAD -> master)
Author: your_name
Date:   Tue Nov 24 13:40:11 2020 +0800
	 Create a new markdown file: test.md 
```

## 12. 错误回退版本
### 还未退出当前命令行界面
- 如果你还想回到最新版本，需要找到上面最新版本的版本号。
- （我的是：136418cf00c8ea49d127b447ea6548aa0543c985）版本号可以不写全，但是尽量多写几位，因为有可能会有小概率有几位重复，没输全就比较麻烦。
```bash
git reset --hard HEAD 136418cf00c8ea49d127b447ea6548aa0543c985
```
返回结果：
```bash
HEAD is now at 136418c Modify file:test.md
```

### 已经关闭当前命令行界面
- reflog: 记录你每一次的命令
```bash
git reflog
```
返回结果：
```bash
136418c (HEAD -> master) HEAD@{0}: reset: moving to 136418cf00c8ea49d127b447ea6548aa0543c985
92cfcec HEAD@{1}: reset: moving to HEAD~1
136418c (HEAD -> master) HEAD@{2}: commit: Modify file:test.md
92cfcec HEAD@{3}: commit (initial): Create a new markdown file: test.md
```
- 找到前面的版本号，就可以修改到你想要的版本了。

## 13. 工作区（Working Directory）
- 我们所在的`Git`就是一个工作区。
- 刚刚`Git`中我们看到的隐藏文件夹`.git`就是Git的版本库。
- Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。
- 我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支

## 14. 工作流程
- `git add`将文件从工作区加入到暂存区中。
- `git commit`提交更改，把暂存区中所有内容加入到当前分支（master）。

随便修改test1中的内容，新建一个`test1.md`文档，输入“Something is so fun...”（随你想改什么）
返回结果：
```bash
On branch master #在名叫 master 的分支上
Changes not staged for commit: #还未提交的更改
  (use "git add <file>..." to update what will be committed) #可以使用git add <file>...提交
  (use "git restore <file>..." to discard changes in working directory) #使用git restore <file>...取消工作区的文件更改
        modified:   test.md #更改的文件: test.md

Untracked files: #未被追踪的文件（新建的）
  (use "git add <file>..." to include in what will be committed) # 使用git add <file>... 将文件添加到缓存区
		test1.md #我们新增的test1.md文件 

no changes added to commit (use "git add" and/or "git commit -a") #现在没有任何加到commit上的改动，如果使用git add或者 git commit -a就会被加上去。
```
使用`git add`文件从工作区加入到缓存区中
```bash
git add test1.md
git status
```
返回结果(很明了，就不解释了)
```bash
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   test.md
        new file:   test1.md
```
使用`git commit`将缓存区中的文件加入到`master`分支上
```bash
git commit -m "Add a new Markdown file: test1.md & Modify file: test.md"
```
返回结果：
```bash
[master 8c8f799] Add a new Markdown file: test1.md & Modify file: test.md
  2 files changed, 4 insertions(+), 1 deletion(-)
  create mode 100644 test1.md
```
最后`git status`检查工作区
```bash
On branch master
nothing to commit, working tree clean
```

## 15. 工作区-缓存区-分支
工作区中修改`test.md`文件(“Let's play.”)
加入到缓存区
工作区再次修改`test.md`文件(“OK.)
将缓存区的文件加入到分支(master)上。
```bash
vim test.md
git add test.md
git commit -m "Modify test.md file again!"
git status
```
- 发现问题：第二次修改的内容未被提交。
- 原因：还在工作区中。

查看分支(master)和工作区(Working Directory)的区别
```bash
diff --git a/test.md b/test.md
index bd77cca..dfcc4f5 100644
--- a/test.md
+++ b/test.md
@@ -3,3 +3,5 @@ You: something is not fun...
 Me: I agree.
 You: Let's play.
+
+Me: Sure.
```
```bash
git add test.md
git commit -m "I forget to submit my new modified file: test.md"`
git status
```
## 16. 撤销你对文件的修改
1. 对`test1.md`文件新增“What a shame...”，还未进行`git add test1.md`提交时候的恢复
```bash
vim test1.md
git status
git restore test1.md
git status
```
发现完全没有任何修改了，说明文件状态已经恢复到被修改之前了。
当然用`git checkout -- <file>...`也是一样的。
如果没有加--，就会删除`git commit`到`master`分支的内容。

已经使用命令`git add <file>...`，缓存区会恢复到还未添加之前的状态


2. 文件在`git add`之后，`git commit`之前的恢复
test1.md添加“Something is wrong...”
```bash
vim test1.md
git add test1.md
git status
```
- 使用`git restore --staged test1.md`退回到`git add`之前
```bash
git status
vim test1.md
```
发现版本回溯到`git add <file>...`之前，而且`test1.md`内容仍然是修改之后的。

- 使用`git reset HEAD <file>`：可以把暂存区的修改撤销掉（unstage），重新放回工作区
```bash
git reset HEAD test1.md
git status
vim test1.md
```
`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示是最新的版本。

两种方法最终结果一样。
最后，丢弃工作区的修改。
```bash
git checkout -- test1.md
git status
```
----
- 工作区恢复: `git checkout -- <file>`
- 缓存区恢复: `git reset HEAD <file>` + `git checkout -- <file>`
- 版本库恢复: 
```markdown
1. git reset --hard HEAD~n
2. git reset --hard HEAD^^^^ #数量取决于你想回退的版本与本版本的序列差值
3. git reset --hard HEAD 版本号（尽量多写点）
```

## 17. 删除文件
1. 添加文件`test2.md`到版本库
`test2.md`: 添加一行"A stupid programmer!"
```bash
vim test2.md
git add test2.md
git commit -m "I add a new Markdown file:test2.md"
```
2. 本地工作区删除`test2.md`并检查状态
```bash
rm test2.md
git status
```
返回结果：
```bash
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test2.md

no changes added to commit (use "git add" and/or "git commit -a")
```
- `git add <file>`: 将工作区的`<file>`文件内容/状态推到缓存区，最后`git commit`推到`master`分支更新。
- `git rm <file>`: 和`add`几乎一样。在缓存区上删除`<file>`文件，并需要`git commit`到`master`分支上进行最终删除确定。
- `git restore <file>`:在工作区中恢复被删除的文件。

-`git checkout -- test2.md`: 从版本库中选择test2.md进行恢复，无论工作区是否修改或者删除此文件都可以直接恢复，一键还原。但是若没有加到版本库无法用此方法恢复文件。

## 18. 远程仓库
### 注册Github账户
`www.github.com`
创建一个新的repo,在主界面的左侧有一个new。新建远程仓库名字必须与你本地相同否则无法对接。
### 本地-远程仓库连接
origin: 远程库标识名，origin可以修改成你想要的名字
your_username:你的Github用户名
your_repo.git: 你的远程仓库名

- 新建本地仓库
```bash
echo "# Git" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/your_username/your_repo.git
git push -u origin main
```
由于远程库是空的，我们第一次推送`main`分支时，加上了-u参数，Git不但会把本地的`main`分支内容推送的远程新的`origin/main`分支，还会把本地和远程的`main`分相对应起来。
以后只需要 `git push origin main`就可以从本地上传到远程仓库，并在页面中查看。

- 已有本地仓库推送到远程仓库
```bash
git remote add origin https://github.com/your_username/your_repo.git
git branch -M main
git push -u origin main
```
-刷新Github界面，发现文件已经上传。而且能看到你上传有关文件的描述。

### 远程克隆到本地
#### Github
Git-Https
```bash
git clone https://github.com/your_username/your_repo.git
```
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入username和密码，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

Git-SSH
为什么GitHub需要SSH Key呢？
如果GitHub上知道你的SSH Key，然后与你本地的SSH Key匹配，这样他就知道你的身份，你可以随意地进行传输文件等操作。如果别人想要冒充你，那么他必须拥有一个你创建的SSH Key才能偷取你的成果。

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

当你想要使用SSH作为传输文件的链接时，Github上会告知你没有SSH key。如果没有SSH的话，你只能使用另外的HTTP的方式（用户名/密码）进行克隆。
```
You don't have any public SSH keys in your GitHub account. You can add a new public key, or try cloning this repository via HTTPS.
```
在电脑的用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。
```bash
ssh-keygen -t rsa -C "youremail@example.com"
```
然后一路回车，使用默认值即可，无需设置密码。完成上述操作之后，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。
登陆Github，Account settings - SSH Keys - Add SSH Key
填入一个有意义的Title(标题)，并在下方key的方框内填入你的SSH public key。
之后检查SSH Key是否已经被加上。
----
Git-CLI
Github出品，2020-9-17刚活过测试版本，仍需要完善。
`https://cli.github.com/`
下载之后，输入
`git auth login`，上下键选择。
将one-time一次性密码输入打开的Github认证网页即可。
-（具体功能待测试。）
----
如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了，具体传输方式由当前情况而定。


### Gitee
git remote add origin 
使用GitHub时，国内的用户经常遇到的问题是访问速度太慢，有时候还会出现无法连接的情况（原因你懂的）。
国内的Git托管服务 www.gitee.com 可以帮你解决这个问题。
和GitHub相比，Gitee也提供免费的Git仓库。此外，还集成了代码质量检测、项目演示等功能。对于团队协作开发，Gitee还提供了项目管理、代码托管、文档管理的服务，5人以下小团队免费。
1. 增加SSH Key方式和Github相同
2. 创建一个你想要的仓库(名称最好与本地相同)
3. 与本地仓库相连
```bash
git remote add origin git@gitee.com:your_gitee_username/your_project_name.git
```
遇到的错误情况：
```bash
git remote add origin git@gitee.com:your_gitee_username/your_project_name.git
fatal: remote origin already exists.
```
使用命令查看远程库信息
```bash
git remote -v
```
返回结果
```bash
origin	git@github.com:your_username/project_name.git (fetch)
origin	git@github.com:your_username/project_name.git (push)
```
- 说明刚刚使用Github的时候已经关联了一个Github的远程仓库。

解决方案：
1. 删除Github远程库，再与Gitee远程库相连。
```bash
git remote rm origin
git remote add origin git@gitee.com:your_username/project_name.git
```
2. 增加Gitee远程库，使得本地可以同步两个远程库
## 19. 创建与合并本地分支
- 每次本地(提交)操作到最后都会到`main`分支,我们也可以把他叫做主分支。最后由我们输入`git push origin main`
`HEAD`指向`main`，`main`指向提交的，所以，`HEAD`指向的就是当前分支。

- 一开始的时候，`mian`分支是一条线，Git用`main`指向最新的提交，再用`HEAD`指向`main`，就能确定当前分支，以及当前分支的提交点或者叫版本点。
- 在版本回退中，我们知道了不断使用`git commit -m`命令进行有效的提交可以使得`main`分支拥有不同的版本，这些版本就像一个个时间线。你提交的次数越多，`HEAD`指向的`main`主分支离最初的版本就越远，时间线越长。
- 当你从单机开发转到公司多人开发模式的时候，仅仅一个分支环境是不够的。一旦遇到复杂的情况，比如我们需要各种各样的测试环境，不可能你在写新功能的时候，测试部更新了之后发现原来的稳定版本已经被你覆盖了。那么这时候一个`main`分支很明显不足以支撑我们的需求。
这时候我们就需要新建其他的分支来帮助唯一的`main`分支分担压力。

### 创建一个 `dev` 开发环境的分支
（删除checkout，切换为新的switch命令）
- 这里的`dev`分支可以换成其他你需要的分支名。 实际情况以公司需求为准。
```bash
git checkout -b dev
```
- 上面这条等于下面两条的结合。
```bash
git branch dev
git checkout dev
```
返回结果：
```bash
Switched to a new branch 'dev' #这里已经切换到了一个新分支
```
也可以用命令查看当前分支：
```bash
git branch
```
返回结果：
- 有多少分支取决于项目需求。
- * 表示当前所在分支
```
* dev
  main

```
对`test1.md`修改并提交
```bash
vim test1.md
i
I add a new branch: dev...
ESC
:wq
git status # 这里只是查看结果，可以不写
git add test1.md
git commit -m "new branch:dev added"
```
返回结果：
```bash
[dev 0cae4f1] new branch:dev added
 1 file changed, 1 insertion(+)
```
在`dev`分支下我们完成了工作，但是`main`才是我们的主分支。
所有文件都应该通过`main`分支来最终完成上传。
我们之前连接远程仓库`origin`的时候写过这么一行。
```bash
git push -u origin main
```
表示远程仓库`origin`与本地分支`main`对接的
那么我们需要将已经完成工作的`dev`分支的内容添加到`main`分支上。
首先，切换回`main`分支。
```bash
git checkout main
```
返回结果：
```bash
Switched to branch 'main' 
Your branch is up to date with 'origin/main'.
```
- 这时候，我们看一下`test1.md`文件的内容。
```bash
vim test1.md
```
- 发现`test1.md`并没有刚刚我们添加的那么一句。
- 这就说明了`dev`分支的版本已经顺着时间线往后了，但是`main`分支被切换后，文件状态仍然留存于上一个我们在`main`分支里使用命令`git commit`完成的版本。

问题来了，你从`main`分支切换到新增的`dev`分支，里面内容同步到了`dev`分支下。但是你切换回`main`分支并没有同步增加，那么从`dev`分支下新增一个`dev1`是和`main`分支内容一样吗？
1. 切换到`main`分支，新增`dev1`分支，查看`test1.md`内容与`main`分支的一样还是和`dev`分支的内容相同，完成后切换回`main`分支并删除`dev1`分支。
- 如果没有切换到别的分支，并不能删除当前分支。
```bash
git checkout main
git checkout -b dev1
vim test1.md
ESC :wq

git checkout main
git branch -d dev1
```
	- 发现文件内容与`main`分支下的是相同的，但不是我们最后一次修改的修新版本。
2. 切换到`dev`分支，新增`dev1`分支，查看`test1.md`文件，与其他两个分支的对比，完成后删除`dev1`分支。
```bash
git checkout dev
git checkout -b dev1
vim test1.md

git checkout dev
git branch -d dev1
```
	- 发现文件内容与`dev`分支下的是相同的。
3. 那么这样操作之后，我们就知道了当你想要复制一个当前已经完成提交的版本内容，只需要在最新更新的分支下新建一个新的分支。 我们可以把这新建的分支叫做上一个分支的子分支。

现在我们想提交最新改完的代码了，需要将`dev`分支的内容合并到`main`分支上。
- 如果没有切换到`main`分支上，会发现`dev`分支下显示`Already up to date.`
```bash
git checkout main
git merge dev
```
返回结果：
```bash
Updating 435c91f..0cae4f1
Fast-forward
 test1.md | 1 +
 1 file changed, 1 insertion(+)
```
删除`dev`分支
```bash
git branch -d dev
```
返回结果：
```bash
Deleted branch dev (was 0cae4f1). 
```
查看分支：
```bash
* main
```
- 建议你在非`main`分支上完成你的工作，确认之后再合并到`main`分支上，这样过程更加安全。

## 20. 冲突与合并
- 这时候如果你新建一个分支，再切回`main`分支，你会发现这么几行字：
```bash
Switched to branch 'main'
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)
```
- 说明你的本地`main`分支已经经过修改，但是远程origin仓库并没有被修改。 先不要着急着上传，我们先了解一下冲突。

- 新建一个`test`分支，修改`test1.md`（I add a new branch: test and I don't need dev branch any more...)
```bash
git switch -c test
vim test1.md
git add test1.md
git commit -m "Can you fix this issue?"
```
- 接着修改`main`分支下的`test1.md`
```bash
git switch main
vim test1.md
git add test1.md
git commit -m "main branch update: test1.md file"
```
- 合并两个分别提交了新版本的分支
	-这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突。
```bash
Auto-merging test1.md
CONFLICT (content): Merge conflict in test1.md
Automatic merge failed; fix conflicts and then commit the result. 
```
	- 当然也可以使用`git status`也可以检查冲突
返回结果：
```bash
On branch main
Your branch is ahead of 'origin/main' by 2 commits. #两个分支有两个不同版本
  (use "git push" to publish your local commits) 
#使用git push直接推送当前分支版本
You have unmerged paths.
  (fix conflicts and run "git commit") # 解决冲突
  (use "git merge --abort" to abort the merge) # 中止合并

Unmerged paths: 
  (use "git add <file>..." to mark resolution) 使用命令标记最终决定
        both modified:   test1.md #两个分支都修改了这个文件

no changes added to commit (use "git add" and/or "git commit -a") #不添加任何提交
```
- 这时候打开`test1.md`看看
```bash
Something is so fun...
<<<<<<< HEAD
I add a new branch:
=======
I add a new branch: test
>>>>>>> test 
```
- HEAD -> main
- test -> test
- 中间用=号作为分隔符
这很清晰明了不是吗？
修改一下两个文件的冲突点，然后继续我们的操作完成最终的提交。
```
git add test1.md
git commit -m "fix conflicts" 
```

### 21. 分支管理策略
- 通常，合并分支时，如果可能，Git会用Fast-forward模式，但这种模式下，删除分支后，会丢掉分支信息。
举例：
```bash
Updating 435c91f..0cae4f1
Fast-forward
 test1.md | 1 +
 1 file changed, 1 insertion(+)
```
如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
1. 创建并切换`dev`分支,修改`test1.md`文件
```bash
git switch -c dev
vim test1.md
git add test1.md
git commit -m "dev-test1.md file change"
```
2. 切回`main`分支，合并`dev`分支
```bash
git switch main
git merge --no-ff -m "dev-test1.md file merge to branch main"
```
3. 使用`git log`查看分支情况

分支管理的基本原则：
尽量不随意改动`main`分支下的文件，其应当作为稳定版本来进行维护/升级
所有合并内容和冲突修改都可以在`dev`下进行，最终稳定版本合并到`main`分支并选择合适时间上传至远程仓库`origin`

在`dev`下，每个人都可以有自己的分支，时不时地往`dev`分支合并就行了。

合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

### Bug 分支
每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
1. 当你遇到需要及时解决的Bug，但是`dev`分支还有工作需要完成，无法提交。
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作.
切换到`dev`分支，新增一个`new.txt`文件，里面随便写内容。使用`git stash`命令
```bash
git switch dev
vim new.txt
git stash
ls
```
你会发现此时工作区已经被清空，回到了这一次工作之前的状态（没有new.txt这个文件）
```bash
Saved working directory and index state WIP on dev: ef70b48 add new branch:dev
```
等你修复完bug，合并在`dev`分支之后，你想要重新加载你刚刚工作时创建的这些文件和修改的代码。
```bash
git stash list
```
返回结果：
```bash
stash@{0}: WIP on dev: ef70b48 add new branch:dev
```
使用命令恢复：
1. 用这个命令恢复后，stash内容并不删除，通过`git stash list`仍能查到
```bash
git stash apply
```
返回结果：
```bash
On branch dev
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   new.txt
```
2. 使用这个命令后，stash list内容删除
```bash
git stash pop
```
返回结果：
```bash
On branch dev
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   new.txt

Dropped refs/stash@{0} (0c1a6a5f75a7f43cb0f73464975bb8ae18574004)
```
再使用`git stash list`就找不到这一纪录了。

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash
```bash
git stash apply stash@{0}
```

或者在你使用`git stash`保存了现有工作状态，想要在`dev`分支新建一个`issue101`的分支用来解决bug时。并不想解决完bug之后，用整个`dev`分支与`main`分支合并。
```bash
git switch dev
git checkout -c
vim new.txt
git add new.txt
git commit -m "fix bug"
```
找到`issue101`相应的版本号：[issue101 **9d20815**] fix bug
```bash
git checkout main
git cherry-pick 9d20815
```

强行删除分支需要使用大写的D：`git branch -D your_branch_name`

## 23. 多人协作
当你从远程仓库克隆时，实际上Git自动把本地的`main`分支和远程的`main`分支对应起来了，并且，远程仓库的默认名称是`origin`。

查看远程库的信息:
```bash
git remote
```
返回：
```bash
origin
```
或者使用：
```bash
git remote -v
```
信息返回更加详细

本地修改好的分支推送到远程：
```bash
git push origin <branch_name>
```
必须同步的分支：
- `mian`是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支本地修改解决无需提交
- 更新功能合并到`dev`再上传

当你克隆一个新项目时，本地并没有`dev`分支，克隆下来的只有`main`主分支。这时候只需要创建一个`dev`分支并于远程`origin`连接即可。
```bash
git branch --set-upstream-to=origin/dev dev
```
返回结果：
```bash
Branch 'dev' set up to track remote branch 'dev' from 'origin'.
```
如果你推送你的`dev`到远程仓库发现冲突，那么肯定是有人在你之前提交了代码，而且他的代码与你的冲突。这之后需要pull他的代码进行代码冲突解决，即可继续推送。
```bash
git pull
git commit -m "your message"
git push origin dev
```

总结多人合作模式流程：
1. 用`git push origin <branch-name>`推送自己的修改
2. 推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并
3. 合并有冲突，则解决冲突，并在本地提交
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功
5. 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`

## 24. Rebase
https://www.liaoxuefeng.com/wiki/896043488029600/1216289527823648
Git的提交历史经过不同分支的修改/增删和pull其他人的代码版本，会变成复杂的关系网。使用Rebase可以使得Git的提交历史变成一条干净的直线。

## 25. 标签管理
Git不同的版本有不同的版本号（commit完成之后会看到）为什么要引入标签？
版本号过于随机，很难记住一串不熟悉的组合。
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
```bash
git tag -a your_version -m "message" commit_id
```
使用如下可以看到说明信息
```bash
git show <tagname>
```
删除标签
- 因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
```bash
git tag -d <version>
```
如果要推送某个标签到远程，使用命令
```bash
git push origin <tagname>
```
一次性推送
```bash
git push origin --tags
```
如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：
```bash
git tag -d <version>
git push origin :refs/tags/<version>
```