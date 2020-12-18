# Linux 基础命令
## 1. alias
> 使用`alia`s命令可以给命令或命令序列指定自己的名称。
- 有一个命令使用`ps`命令列出正在运行的进程，然后将它们通过`grep`命令传输。`grep`命令在`ps`的输出中查找与命令行参数`$1`匹配的条目。
```bash
alias pf="ps -e | grep $1"
```
- 想要发现某一个进程的进程`ID (PID)`——或者想要知道这个进程是否在运行——我们可以像这样使用别名。
- 输入`pf`，空格，以及你感兴趣的进程名称
```bash
pf 进程名
```
当终端关闭后，命令行上定义的别名也消失了。要使您的别名永久可用，请将其添加到`.bash_aliases`主目录中的文件中。

## 2. ls
> 这可能是大多数Linux用户遇到的第一个命令。
> 它列出了用户指定目录中的文件和文件夹。
> 默认情况下，ls查找当前目录。

- 使用`-l` (long)选项列出当前目录下的文件和文件夹的详细列表
- 使用`-a`列出当前目录下所有文件和文件夹包括隐藏文件

- 使用`-la`以上两个的集合
```bash
ls -l
ls -a
ls -la
```
[ls 命令进阶](https://www.linuxmi.com/linux-ls-rm.html)

## 3. cd
> `cd: change directory`, 更改当前目录。
1. 切换到当前目录的上级目录
- `..`为上级目录，可以不加`/`；若想要切换到再上一级目录，即`../../`，更上一级同理。
```bash
cd ../
```

2. 切换到当前目录的下级目录
- `./` 一个`.`确定当前目录，`./`即定位到当前目录，若要找下级目录中的文件输入文件名/文件夹名即可(输入开头几个字母或名字后可按`tab`补全名字)
```bash
cd ./workspace/
```

3. 切换到主目录，根目录
- Linux 的根目录皆为`root`
- 不确定当前目录在`root`中的哪个位置，而且回到`root`的路径太过于长了，不可能使用`../`一直回退。

```bash
cd /
```

- 回到主目录也很简单，后根`~`即可

```bash
cd ~
```

### 注意事项
- 以上`cd`后根的有些参数可互相叠加，可自行玩耍

例如
```
cd ~/Desktop
cd ../game/file
```

## 4. man
> man命令以less显示命令的“man页面”。
> 手册页是该命令的用户手册。
> 因为man使用less来显示man页面，所以我们可以使用less的搜索功能。
- 要查看chown的手册页，可以使用以下命令
```bash
man chown
```
- 使用向上和向下箭头或PgUp和PgDn键来滚动文档。
- 按q退出手册页或按h请求帮助。

## 5. cat
> `cat: concatenate` 的缩写，将文件内容输入到终端窗口。
> 这比在编辑器中打开文件要快，而且您不可能意外地修改文件。

```bash
cat file_name
```
- 如果文件的行数超过终端窗口中的行数，那么文本拉到的速度会快得让我们无法阅读。
- 我们可以将来自`cat`的输出通过`less`管道传输，以使流程更易于管理。

### less
> less命令可以不打开编辑器的情况下查看文件。
> 它使用起来更快，而且不会无意中修改文件。
> 使用更少的键，使用**向上**和**向下**箭头键、**PgUp**和**PgDn**键以及**主键**和**结束键**在文件中向前和向后滚动。
> 若决定退出，按一下q键退出less。

```bash
cat .bashrc|less

less Test.java
```

## 6. head
> head命令默认给出一个文件的前10行清单。
> 如果希望看到更少或更多的行，可以使用`-n` (number)选项。
```bash
head -5 Test.java
```

## 7. tail
> tail命令给出一个文件的最后10行清单。
> 如果希望看到更少或更多的行，可以使用`-n` (number)选项。
```bash
tail Demo.java
tail -3 Demo.java
```

## 8. chmod(修改文件权限)
[具体请按照这个内容学习](https://www.linuxmi.com/linux-chmod.html)
> `chmod`命令设置文件或文件夹上的文件权限标志。
> 这些标志定义了谁可以读取、写入或执行文件。

- 使用ls -l命令以长格式的形式查看当前目录下所有可见文件的详细属性。该-l标志列出文件的权限。权限分为三组：用户（user）组（group）和其他（others）。
- 文件和目录可以属于用户（u），组（g）或其他（o）的所有者
	- u: 所有人的权限
	- g: 所有组的权限
	- o: 其他人的权限
- 在Linux中，我们具有3种类型的文件权限：读（r），写（w）和执行（x）权限。这些权限确定哪些用户可以读取，写入或执行文件。
- 可以使用文本或八进制（数字）表示法来分配这些权限
----
1. 从最左端开始，第一个字符/符号表示文件类型。
2. 一个连字符（ – ） ，表示该文件是一个普通文件。
3. 符号d表示它是目录。符号l表示它是符号链接。
4. 其余的9个字符分成3个三元组，每个三元组带有三个符号r（read），w（write）和x（execute）。
5. 如前所述，第一部分指向所有者权限，第二部分指示组权限，最后一部分指定其他用户对该文件或目录具有的权限。
(以后补充，若要看详细内容请按照给出的网址学习)

## 9. mkdir
> mkdir:make directory 当前目录下新建一个文件夹
```bash
mkdir [directory]
```
## 10. touch
> 创建一个文件
```bash
touch [file]
```

## 11. vi/vim
> 文本编辑器
- 一般新建文件我都用这个
```vim
vim Test.java
```
- 进入编辑页面，按`i`进入插入模式
- 输入你想要输入的文本信息之后`[ESC] + [:] + [wq]`即可退出，左下角可看见这行命令。
- 若不想保存，输入`[ESC] + [:] + [q!]`即可。
- vim 非常强大，有时间可以做一个`vim`的超新手教学。

## 12. mv
> `mv:move` 移动文件位置，操作方式和`cd`一样
> 额外功能可以重命名当前文件或文件夹

```bash
mv Test.java ./Test1.java

mv Test.java ../
```

## 13. rm
> `rm: remove` 删除文件/文件夹
> 文件夹删除有点儿不一样
- -f,--force 忽略不存在的文件，不显示任何信息
不会提示确认信息。(小心使用这个参数)
- -r,-R,--recursive   指示rm将参数中列出的全部目录和子目录均递归地删除。
- -v, --verbose    详细显示进行的步骤
- --help     显示此帮助信息并退出
- --version  输出版本信息并退出
```bash
rm [file]

rm -rf [directory]
```

## 14. ping
> 测试网络是否畅通，两个机子之前是否能通信
```bash
ping www.baidu.com
ping 192.168.1.1
```
- 使用 `CTRL + C` 退出(这个退出可以退出大部分你无法操作的问题)

## 15. ps
> ps命令列出了正在运行的进程。
> 使用不带任何选项的ps将导致它列出当前shell中运行的进程。
```bash
ps
```

## 16. passwd
> passwd命令允许您更改用户的密码。
> 只需键入passwd来更改您自己的密码。
- 还可以更改另一个用户帐户的密码，但必须使用sudo。
- 被要求输入两次新密码。
```bash
sudo passwd [user]
```

## 17. pwd
> 当前目录所在路径
```bash
pwd
```

## 18. whereis
> 找到你安装的软件位置
```bash
whereis python
```

## 19. whoami
> 使用whoami查找您以谁的身份登录，或者谁登录到无人操作的Linux终端。
```bash
whoami
```
## 20. exit
> exit命令将关闭一个终端窗口，结束shell脚本的执行，或者使用户退出SSH远程访问会话。
```bash
exit
```

## 21. shutdown
> 关机
```bash
shutdown 0

shuwdown now
```
- 后接分钟数或时间
- `shutdown -c`取消当前关机操作（如果直接关机那没用了...)

## 22. reboot
> 重启
```bash
reboot
```

## 23. ssh
```bash
ssh 192.168.1.1
```

## 24. sudo
- 以管理员权限做一些事情，需要输入`root`密码
```bash
sudo apt-get install [软件]
```

## 25. top
> top命令显示与Linux机器相关的实时数据。屏幕的顶部是状态摘要。
> 第一行显示了时间和计算机已经运行了多长时间，有多少用户登录到它，以及过去1、5和15分钟的平均负载。
> 第二行显示了任务的数量及其状态:正在运行、已停止、休眠和僵死状态。

## 26. uname
> 使用`uname`命令获得有关您正在使用的Linux计算机的一些系统信息。
- 使用-a (all)选项查看所有内容。
- 使用-s(内核名称)选项可以查看内核的类型。
- 使用-r(内核发布)选项来查看内核发布。
- 使用-v(内核版本)选项可以查看内核版本。

## 27. chown
- chown命令允许您更改文件的所有者和组所有者。
```markdown
使用ls -l列出我们的example.txt文件，我们可以在文件描述中看到dave dave。第一个指示文件所有者的名称，在本例中是用户linuxmi。第二个条目显示组所有者的名字也是linuxmi。每个用户都有一个在创建用户时创建的默认组。该用户是该组的唯一成员。这表明该文件不与任何其他用户组共享。
可以使用chown更改文件的所有者或组，或同时更改文件的所有者或组。必须提供所有者和组的名称，中间用:字符分隔。您将需要使用sudo。要保留linuxmi作为文件的所有者，但设置linux为组所有者，使用以下命令:
```
```bash
sudo chown linuxmi:linux linuxmi.py
```
<img src="../image/Linux/chown1.png">

要将所有者和组所有者都更改为linux，可以使用以下命令:
```bash
sudo chown linux:linux linuxmi.py
```
<img src="../image/Linux/chown2.png">

要更改文件，使linuxmi再次成为文件所有者和组所有者，使用以下命令:
```bash
sudo chown linuxmi:linuxmi linuxmi.py
```
<img src="../image/Linux/chown3.png">

## 9. curl
> 在Linux中curl是一个利用URL规则在命令行下工作的文件传输工具，可以说是一款很强大的http命令行工具。
> 它支持文件的上传和下载，是综合传输工具，但按传统，习惯称url为下载工具。
```bash
curl [option] [url]
```
1. -A/–user-agent <string> 设置用户代理发送给服务器
2. -b/–cookie <name=string/file> cookie字符串或文件读取位置
3. -c/–cookie-jar <file> 操作结束后把cookie写入到这个文件中
4. -C/–continue-at <offset> 断点续转
5. -D/–dump-header <file> 把header信息写入到该文件中
6. -e/–referer 来源网址
7. -f/–fail 连接失败时不显示http错误
8. -o/–output 把输出写到该文件中
9. -O/–remote-name 把输出写到该文件中，保留远程文件的文件名
10. -r/–range <range> 检索来自HTTP/1.1或FTP服务器字节范围
11. -s/–silent 静音模式。不输出任何东西
12. -T/–upload-file <file> 上传文件
13. -u/–user <user[:password]> 设置服务器的用户和密码
14. -w/–write-out [format] 什么输出完成后
15. -x/–proxy <host[:port]> 在给定的端口上使用HTTP代理
16. -#/–progress-bar 进度条显示当前的传送状态

- [具体使用方法](https://www.linuxmi.com/linux-curl-mingling.html)

## 28. wget

## 29. df
> df命令显示计算机安装的文件系统上的大小、使用空间和可用空间。
> 两个最有用的选项是`-h`(human readable 通俗易懂的)和`-x`(排除)选项。通俗易懂的选项以`Mb`或`Gb`而不是字节显示大小。
> 排除选项允许我们告诉`df`贴现您不感兴趣的文件系统。

## 30. diff
> diff命令比较两个文本文件并显示它们之间的差异。
> 有许多选项可以根据需求定制显示。
> `-y` (side by side)选项并排显示行差。
> `-w`(宽度)选项允许用户指定用于避免绕行的最大行宽。
> `--suppress-common-lines` 防止diff列出匹配的行，从而使用户能够关注具有差异的行。

## 31. echo
> echo命令向终端窗口输出一串文本。
> echo命令可以显示环境变量的值，例如，`$USER`、`$HOME`和`$PATH`环境变量。它们包含用户名、用户的主目录以及用户在命令行上键入内容时搜索匹配命令的路径。
> echo命令在shell脚本中也是非常宝贵的。脚本可以使用此命令生成可见的输出，以指示脚本执行时的进度或结果。

## 32. find
> Linux中的Find(查找)命令是在Linux系统中最重要并且更有用的命令之一。
> Find命令主要用于指定匹配文件条件的参数查找或者定位文件和目录的列表。
> Find命令能够被使用基于各种各样的条件，例如permissions（权限），users（用户），groups（组），file type（文件类型），date（日期），size（大小）等等其它可能的条件。
[若要详细内容，请点击这里](https://www.linuxmi.com/linux-find-mingling-shiyong.html)

## 33. finger
> `finger`命令提供一个关于用户的简短信息转储，包括用户最后一次登录的时间、用户的主目录和用户帐户的全名。
```bash
find [user]
```

## 34. free
> free命令提供计算机内存使用情况的摘要。它对主随机访问内存(RAM)和交换内存都这样做。
> `-h`(人)选项用于提供人类友好的数字和单位。 如果没有这个选项，图形将以字节表示。
```bash
free -h
```

## 35. grep
> grep实用程序搜索包含搜索模式的行。
> 在查看alias命令时，我们使用grep搜索另一个程序ps的输出。
> grep命令还可以搜索文件的内容。
> 这里我们在当前目录的所有文本文件中搜索单词“le”。
```bash
grep le *.java
```
- 输出将列出文件的名称并显示与之匹配的行。高亮显示匹配的文本。
[若要详细学习，请点击这里](https://www.linuxmi.com/linux-grep-mingling-gaoji.html)

## 36. groups
> groups命令告诉您用户属于哪个组。
```bash
groups [user]
```

## 37. gzip
> gzip命令压缩文件。默认情况下，它删除原始文件并留下压缩版本。
> 要同时保留原始版本和压缩版本，可以使用-k (keep)选项。
```bash
gzip -k [file]
```

## 38. history
> history命令列出您以前在命令行上发出的命令。
> 您可以通过键入感叹号来重复历史记录中的任何命令`!`以及历史列表中的**命令编号**。
> 输入两个感叹号重复前面的命令。
```bash
history
!100

!!
```

## 39. kill
> kill命令允许我们使用命令行终止进程。
> 我们需要提供要终止的进程的进程ID (PID)来实现这一点。
> 请不要随意杀死进程，除非我们有一个很好的理由这样做。
1. 得到目标`PID`
```bash
ps -e | grep shutter
14205 ? 00:00:02 shutter

kill -9 14205
ps -e | grep shutter
```
- 默认参数下，kill 发送SIGTERM（15）信号给进程，告诉进程，你需要被关闭，请自行停止运行并退出。
- kill -9 发送SIGKILL信号给进程，告诉进程，你被终结了，请立刻退出。

