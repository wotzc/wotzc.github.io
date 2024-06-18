---
title: Linux教程(一)
date: 2024-05-13 11:16:37
tags:
- Linux
categories: Linux
---

# 基础信息

其实整个指令下达的方式很简单，你只要记得几个重要的概念就可以了。举例来说，你可以这样下达指令的:

```
[dmtsai@study ~]$ command [-options] parameter1 parameter2 
... 指令 选项 参数(1) 参数(2)
```

上述指令详细说明如下:

1. 一行指令中第一个输入的部分绝对是“指令(`command`)”或“可执行文件案(例如批次 脚本,`script`)”

2. `command`为指令的名称，例如变换工作目录的指令为`cd`等等;

3. 中刮号[]并不存在于实际的指令中，而加入选项设置时，通常选项前会带`-`号，例如-h;

   有时候会使用选项的完整全名，则选项前带有 `--` 符号，例如 --help;

4. `parameter1` `parameter2`..为依附在选项后面的参数，或者是`command`的参数;

5. 指令,选项,参数等这几个咚咚中间以空格来区分，不论空几格shell都视为一格。所以空格是很重要的特殊字符!;

6. 按下[`Enter`]按键后，该指令就立即执行。[Enter]按键代表着一行指令的开始启动。

7. 指令太长的时候，可以使用反斜线(`\`)来跳脱[Enter]符号，使指令连续到下一行。注意！反斜线后就立刻接特殊字符，才能跳脱!

8. 其他:
    i. 在 Linux 系统中，英文大小写字母是不一样的。举例来说， `cd` 与 `CD` 并不同。


注意到上面的说明当中，“第一个被输入的数据绝对是指令或者是可执行的文件”! 这个是很 重要的概念喔!还有，按下[Enter]键表示要开始执行此一命令的意思。

## 基础指令的操作

下面我们立刻来操作几个简单的指令看看!

- 显示日期与时间的指令: date 
- 显示日历的指令: cal 
- 简单好用的计算机: bc

## 重要的几个热键[Tab], [ctrl]-c, [ctrl]-d

### [Tab]按键

在各种Unix-Like的 Shell当中， 这个[Tab]按键算是Linux的Bash shell最棒的功能之一了!他具有“命令补全”与“文件补齐”的功能喔!

### [Ctrl]-c 按键

如果你在Linux下面输入了错误的指令或参数，有的时候这个指令或程序会在系统下面“跑不 停”这个时候怎么办?别担心， 如果你想让当前的程序“停掉”的话，可以输入:[Ctrl]与c按键 (先按着[Ctrl]不放，且再按下c按键，是组合按键)， 那就是中断目前程序的按键啦!

### [Ctrl]-d 按键

那么[Ctrl]-d是什么呢?就是[Ctrl]与d按键的组合啊!这个组合按键通常代表着: “键盘输入结 束(End Of File, EOF 或 End Of Input)”的意思! 另外，他也可以用来取代exit的输入呢!例 如你想要直接离开命令行，可以直接按下[Ctrl]-d就能够直接离开了(相当于输入exit啊!)。

### [shift]+{[PageUP]|[Page Down]}按键

如果你在纯文本的画面中执行某些指令，这个指令的输出讯息相当长啊!所以导致前面的部 份已经不在目前的屏幕画面中， 所以你想要回头去瞧一瞧输出的讯息，那怎办?其实，你可 以使用 [Shift]+[Page Up] 来往前翻页，也能够使用 [Shift]+[Page Down] 来往后翻页! 这两个 组合键也是可以稍微记忆一下，在你要稍微往前翻画面时，相当有帮助!

# Linux 的文件权限与目录配置

Linux一般将文件可存取的身份分为三个类 别，分别是 `owner/group/others`，且三种身份各有 `read/write/execute` 等权限。

## **Linux**文件属性

嗯!既然要让你了解Linux的文件属性，那么有个重要的也是常用的指令就必须要先跟你说 啰!那一个?就是“ ls ”这一个察看文件的指令啰!在你以dmtsai登陆系统，然后使用 su - 切 换身份成为root后， 下达“ ls -al ”看看

```bash
 [dmtsai@study ~]$ su - # 先来切换一下身份看看
 
Password:
Last login: Tue Jun  2 19:32:31 CST 2015 on tty2
[root@study ~]# ls -al
total 48
dr-xr-x---.  5    root     root    4096  May 29 16:08 .
dr-xr-xr-x. 17    root     root    4096  May  4 17:56 ..
-rw-------.  1    root     root    1816  May  4 17:57 anaconda-ks.cfg
-rw-------.  1    root     root     927  Jun  2 11:27 .bash_history
-rw-r--r--.  1    root     root      18  Dec 29  2013 .bash_logout
-rw-r--r--.  1    root     root     176  Dec 29  2013 .bash_profile
-rw-r--r--.  1    root     root     176  Dec 29  2013 .bashrc
drwxr-xr-x.  3 		root 		 root 		 17  May  6 00:14 .config
drwx------.  3    root     root      24  May  4 17:59 .dbus
-rw-r--r--.  1 		root 		 root 	 1864  May  4 18:01 initial-setup-ks.cfg
[1]         [2]		[3]			 [4]		  [5]	 [6]				  [7]
[权限]    [链接] [拥有者]   [群组] [文件大小][修改日期      [文件名]
 
```

文件属性的示意图

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-file-type.png)

第一个字符代表这个文件是“目录、文件或链接文件等等”:

各个文件类型及其字符表示为：

| 表示字符 |           文件类型           |
| :------: | :--------------------------: |
|   `d`    |             目录             |
|   `-`    |             文件             |
|   `l`    |          符号链接等          |
|   `b`    |      可供储存的接口设备      |
|   `c`    | 串行端口设备，如键盘、鼠标等 |

接下来的字符中，以三个为一组，且均为 **rwx** 的三个参数的组合。其中， **r** 代表可读(read)、 **w** 代表可写(write)、 **x** 代表可执行(execute)。 要注意的是，这三个权限的位置不会改变，如果没有权限，就会出现减号 **-** 而已。

- 第一组为“文件拥有者可具备的权限”，以“initial-setup-ks.cfg”那个文件为例， 该文件 的拥有者可以读写，但不可执行;
- 第二组为“加入此群组之帐号的权限”; 
- 第三组为“非本人且没有加入本群组之其他帐号的权限”。

每个文件的属性由左边第一部分的 10 个字符来确定（如下图）。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-file-permissions.png)

从左至右用 **0-9** 这些数字来表示。

第 **0** 位确定文件类型，第 **1-3** 位确定属主（该文件的所有者）拥有该文件的权限。

第4-6位确定属组（所有者的同组用户）拥有该文件的权限，第7-9位确定其他用户拥有该文件的权限。

其中，第 **1、4、7** 位表示读权限，如果用 **r** 字符表示，则有读权限，如果用 **-** 字符表示，则没有读权限；

第 **2、5、8** 位表示写权限，如果用 **w** 字符表示，则有写权限，如果用 **-** 字符表示没有写权限；第 **3、6、9** 位表示可执行权限，如果用 **x** 字符表示，则有执行权限，如果用 **-** 字符表示，则没有执行权限。

第五栏为这个文件的容量大小，默认单位为Bytes;

第六栏为这个文件的创建日期或者是最近的修改日期:

例题:如果我的目录为下面的样式，请问testgroup这个群组的成员与其他人 (others)是否可以进入本目录?

```bash
 drwxr-xr--   1 test1    testgroup    5238 Jun 19 10:25 groups/
```

答:

- 文件拥有者test1[rwx]可以在本目录中进行任何工作; 
- 而testgroup这个群组[r-x]的帐号，例如test2, test3亦可以进入本目录进行工作，但是不能 在本目录下进行写入的动作;
-  至于other的权限中[r--]虽然有r ，但是由于没有x的权限，因此others的使用者，并不能进 入此目录!

## 如何改变文件属性与权限

我们先介绍几个常用于群组、拥有者、各种身份的 权限之修改的指令，如下所示:

- `chgrp` :改变文件所属群组
- `chown` :改变文件拥有者
- `chmod` :改变文件的权限, SUID, SGID, SBIT等等的特性

### 改变所属群组, chgrp

改变一个文件的群组真是很简单的，直接以`chgrp`来改变即可，咦!这个指令就是`change group`的缩写嘛!这样就很好记了吧! ^_^。不过，请记得，要被改变的群组名称必须要 在/etc/group文件内存在才行，否则就会显示错误!

假设你已经是root的身份了，那么在你的主文件夹内有一个名为 initial-setup-ks.cfg 的文件， 如何将该文件的群组改变一下呢?假设你已经知道在/etc/group里面已经存在一个名为users的 群组， 但是testing这个群组名字就不存在/etc/group当中了，此时改变群组成为users与 testing分别会有什么现象发生呢?

```
[root@study ~]# chgrp [-R] dirname/filename ...
  选项与参数:
 -R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有文件、目录
 都更新成为这个群组之意。常常用在变更某一目录内所有的文件之情况。
 范例:
 [root@study ~]# chgrp users initial-setup-ks.cfg
 [root@study ~]# ls -l
 -rw-r--r--. 1 root users 1864 May  4 18:01 initial-setup-ks.cfg
 [root@study ~]# chgrp testing initial-setup-ks.cfg
chgrp: invalid group: `testing' &lt;== 发生错误讯息啰~找不到这个群组名~
```

发现了吗?文件的群组被改成users了，但是要改成testing的时候， 就会发生错误~注意喔!

### 改变文件拥有者, chown

如何改变一个文件的拥有者呢?很简单呀!既然改变群组是change group，那么改变拥有者 就是change owner啰!BINGO!那就是`chown`这个指令的用途，要注意的是， 使用者必须是已经存在系统中的帐号，也就是在/etc/passwd 这个文件中有纪录的使用者名称才能改变。

`chown`的用途还蛮多的，他还可以顺便直接修改群组的名称呢!此外，如果要连目录下的所有次目录或文件同时更改文件拥有者的话，直接加上 `-R` 的选项即可!我们来看看语法与范例:

```
[root@study ~]# chown [-R] 帐号名称 文件或目录
[root@study ~]# chown [-R] 帐号名称:群组名称 文件或目录
 选项与参数:
 -R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有文件都变更
 
 范例:将 initial-setup-ks.cfg 的拥有者改为bin这个帐号:
 [root@study ~]# chown bin initial-setup-ks.cfg
 [root@study ~]# ls -l
 -rw-r--r--. 1 bin  users 1864 May  4 18:01 initial-setup-ks.cfg
 
 范例:将 initial-setup-ks.cfg 的拥有者与群组改回为root:
 [root@study ~]# chown root:root initial-setup-ks.cfg
 [root@study ~]# ls -l
  -rw-r--r--. 1 root root 1864 May  4 18:01 initial-setup-ks.cfg
```

### 改变文件权限, chmod

**数字类型改变文件权限**

我们可以用数字来代表各个权限，各个权限对应的数字为：

```
r:4
w:2
x:1
```

每种身份各自的三个权限分数是需要累加的，例如当权限为 `rwxrwx---` 时，对应的分数为：

```
user   = rwx = 4+2+1 = 7
group  = rwx = 4+2+1 = 7
others = --- = 0+0+0 = 0
```

得到的文件权限数字也就为 `770`, 修改权限时就可以使用这个数字完成：

```
$ chmod [-R] 770 文件或目录
参数
-R : 进行递回(recursive)的持续变更，亦即连同次目录下的所有文件都会变更
```

在实际的系统运行中 最常发生的一个问题就是，常常我们以vim编辑一个shell的文字批处理文件后，他的权限 通常是 -rw-rw-r-- 也就是664， 如果要将该文件变成可可执行文件，并且不要让其他人修 改此一文件的话， 那么就需要-rwxr-xr-x这样的权限，此时就得要下达:“ chmod 755 test.sh ”的指令啰!

另外，如果有些文件你不希望被其他人看到，那么应该将文件的权限设置为例如:“-rwxr- ----”，那就下达“ chmod 740 filename ”吧!

**符号类型改变文件权限**

还有一个改变权限的方法呦!从之前的介绍中我们可以发现，基本上就九个权限分别是 (1)`user` (2)`group` (3)`others`三种身份啦!那么我们就可以借由`u`, `g`, `o`来代表三种 身份的权限!此外， a 则代表 all 亦即全部的身份!那么读写的权限就可以写成`r`, `w`, `x`

啰!也就是可以使用下面的方式来看:

```
| chmod | u g o a | +(加入) -(除去) =(设置) | r w x | 文件或目录 |
```

符号类型改变文件权限需要遵循一定的语法规则，分别需要了解的有 **身份表示符**, **操作表示符** 和 **权限表示符**.

**身份表示符：**

| 表示符 |       代表的身份       |
| :----: | :--------------------: |
|  `u`   |      文件的拥有者      |
|  `g`   | 文件的拥有者所在用户组 |
|  `o`   |         其他人         |
|  `a`   |        所有用户        |

**操作表示符：**

| 表示符 | 代表的操作 |
| :----: | :--------: |
|  `+`   |  添加权限  |
|  `-`   |  去除权限  |
|  `=`   |  设定权限  |

**权限表示符** 就是 `r`, `w` 和 `x`.

来实作一下吧!假如我们要“设置”一个文件的权限成为“-rwxr-xr-x”时，基本上就是:

-  user (u):具有可读、可写、可执行的权限;
- group 与 others (g/o):具有可读与执行的权限。 所以就是

```
[root@study ~]# chmod u=rwx,go=rx .bashrc
# 注意喔!那个 u=rwx,go=rx 是连在一起的，中间并没有任何空白字符!
```

那么假如是“ -rwxr-xr-- ”这样的权限呢?可以使用“ `chmod u=rwx,g=rx,o=r filename` ”来设 置。此外，如果我不知道原先的文件属性，而我只想要增加.bashrc这个文件的每个人均可写入的权限， 那么我就可以使用:

```
 [root@study ~]# ls -al .bashrc
 -rwxr-xr-x. 1 root root 176 Dec 29  2013 .bashrc
 [root@study ~]# chmod  a+w  .bashrc
 [root@study ~]# ls -al .bashrc
 -rwxrwxrwx. 1 root root 176 Dec 29  2013 .bashrc
```

比如说下面的这条指令让拥有者具有所有权限，而为用户组和其他人添加执行权限：

```
chmod u=rwx,go+x .vimrc
```

需要注意的是： `u=rwx,go+x` 之间没有空格。

## 目录与文件之权限意义

### 权限对文件的重要性

文件是实际含有数据的地方，包括一般文本文件、数据库内容档、二进制可可执行文件 (binary program)等等。 因此，权限对于文件来说，他的意义是这样的:

- r (read):可读取此一文件的实际内容，如读取文本文件的文字内容等;
- w (write):可以编辑、新增或者是修改该文件的内容(但不含删除该文件); 
- x (eXecute):该文件具有可以被系统执行的权限。

那个可读(`r`)代表读取文件内容是还好了解，那么可执行(`x`)呢?这里你就必须要小心啦! 因为在Windows下面一个文件是否具有执行的能力是借由“ 扩展名 ”来判断的， 例如:.exe, .bat, .com 等等，但是在Linux下面，我们的文件是否能被执行，则是借由是否具有“`x`”这个权 限来决定的!跟文件名是没有绝对的关系的!

至于最后一个`w`这个权限呢?当你对一个文件具有`w`权限时，你可以具有写入/编辑/新增/修改 文件的内容的权限， 但并不具备有删除该文件本身的权限!对于文件的`rwx`来说， 主要都是 针对“文件的内容”而言，与文件文件名的存在与否没有关系喔!因为文件记录的是实际的数据嘛!

### 权限对目录的重要性

如果是针对目录时，那个 r, w, x 对目录是什么意 义呢?

- r (read contents in directory):

  表示具有读取目录结构清单的权限，所以当你具有读取(r)一个目录的权限时，表示你 可以查询该目录下的文件名数据。 所以你就可以利用 ls 这个指令将该目录的内容列表显 示出来!

- w (modify contents of directory):
   这个可写入的权限对目录来说，是很了不起的! 因为他表示你具有异动该目录结构清单 的权限，也就是下面这些权限:

- - 创建新的文件与目录;
  - 删除已经存在的文件与目录(不论该文件的权限为何!) 将已存在的文件或目录进行更名;
  - 搬移该目录内的文件、目录位置。 总之，目录的w权限就与该目录下面的文件名异 动有关就对了啦!

- x (access directory):

  咦!目录的执行权限有啥用途啊?目录只是记录文件名而已，总不能拿来执行吧?没 错!目录不可以被执行，目录的x代表的是使用者能否进入该目录成为工作目录的用途! 所谓的工作目录(work directory)就是你目前所在的目录啦!举例来说，当你登陆Linux

同一个权限对于 **文件** 和 **目录** 来说，含义是不一样的，这里来了解一下。

| 权限 |         文件         |          目录           |
| :--: | :------------------: | :---------------------: |
| `r`  |   可以读取文件内容   |  可以读取目录结构列表   |
| `w`  | 可以编辑修改文件内容 |  可以改动目录结构列表   |
| `x`  |    可以被系统执行    | 用户可以进入目录 (`cd`) |

这里需要注意的一个权限是： **可以改动目录结构列表**, 这意味着可以：

- 建立新的文件与目录
- 删除已经存在的文件与目录
- 将已存在的文件或目录进行更名
- 搬移该目录内的文件、目录位置

所以 `w` 这个权限还是慎重使用好了。

## **Linux**目录配置

由于 **linux distribution** 太多，所以有了 **FHS**（**Filesystem Hierarchy Standard**）标准。

该标准主要目的是：让使用者可以了解到已安装软件通常放置于哪个目录下， FHS 的重点在于每个特定的目录下应该要放上面样子的数据。

FHS 是根据过去的经验一直在持续的改版，依据文件系统使用的频繁与是否允许使用者随意更动， 而将目录定义成为四种交互作用的形态。

|          -          |    可分享的（shareable）     | 不可分享的（unshareable） |
| :-----------------: | :--------------------------: | :-----------------------: |
|   不变得（static)   |     `/usr`（软件放置处）     |    `/etc` （配置文件）    |
|          -          |     `/opt`（第三方软件）     |  `/boot` （开机与核心）   |
| 可变动的（variable) |  `/var/mail` （使用者邮箱)   |  `/var/run` （程序相关）  |
|          -          | `/var/spool/news` （新闻组） | `/var/lock` （程序相关）  |

上表中是一些代表性的目录，而下面放置的数据后面会讲到，这里主要了解什么是那四个类型？

- 可分享的:可以分享给其他系统挂载使用的目录，所以包括可执行文件与使用者的邮件 等数据， 是能够分享给网络上其他主机挂载用的目录;
- 不可分享的:自己机器上面运行的设备文件或者是与程序有关的socket文件等， 由于仅 与自身机器有关，所以当然就不适合分享给其他主机了。
- 不变的:有些数据是不会经常变动的，跟随着distribution而不变动。 例如函数库、文件说明文档、系统管理员所管理的主机服务配置文件等等;
- 可变动的:经常改变的数据，例如登录文件、一般用户可自行收受的新闻群组等。

事实上 FHS 针对目录树架构仅定义出三层目录下应该放置什么数据：

- `/` ： root 根目录，与开机系统有关
- `/usr` ：unix software resource 与软件安装/执行有关
- `/var`：variable 与系统运作过程有关

### 根目录 / 的意义与内容

根目录是整个系统最重要的一个目录，因为不但所有的目录都是由根目录衍生出来的，同时 根目录也与开机/还原/系统修复等动作有关。 由于系统开机时需要特定的开机软件、核心文 件、开机所需程序、函数库等等文件数据，若系统出现错误时，根目录也必须要包含有能够 修复文件系统的程序才行。 因为根目录是这么的重要，所以在FHS的要求方面，他希望根目 录不要放在非常大的分区内， 因为越大的分区你会放入越多的数据，如此一来根目录所在分 区就可能会有较多发生错误的机会。

因此 FHS 标准建议是：根目录所在分区槽应该越小越好，且应用程序所安装的软件最好不要与根目录放在同一个分区槽内， 报纸根目录越小越好。如此不但效能较佳，根目录所在的文件系统也较不容易发生问题

因此 FHS 定义出根目录下应该要有以下目录存在，即使没有实体目录，也希望至少有连接文件存在。

第一部分：FHS 要求必须要存在的目录

- `/bin`

  系统有很多放置执行文件的目录，单 /bin 比较特殊。 因为放置的是在单人维护模式下还能够被操作的指令。

  /bin 下的指令可以被 root 与一般账户所使用，主要有 cat、chmod、chown、date、mv、mkdir、cp、bash 等常用命令

- `/boot`

  主要放置开机会使用到的文件，包括 linux 核心文件以及开机选单与开机锁需配置文件等。

  **Linux kernel 常用额文件名为 vmlinuz** ，如果使用 grub2 开机管理程序，则还会存在 /boot/grub2 这个目录

- `/dev`

  任何装置与接口设备都是以文件形态存在这个目录当中。只要透过存取这个目录下的某个文件， 就等于存取某个装置，比较重要的文件有 /dev/null、/dev/zero、/dev/tty、/dev/loop*、/dev/sd* 等

- `/etc`

  系统主要的配置文件几乎都放在这个目录中，例如人员的账户密码文件、各种服务的启动文件等， 一般来说，这个目录下的各文件属性是可以让一般使用者查阅的，但是只有 root 有权利修改。 **FHS 建议不要放置可执行文件 (binary)** 在这个目录中。

  比较重要的有 /etc/modprobe.d、/etc/passwd、/etc/fstab、/etc/issue 等。

  另外 FHS 还规范几个重要的目录页最好咋 /etc 目录下：

  - `/etc/opt/` ：必要，放置第三方协力软件 /opt 的相关配置文件
  - `/etc/xqq/` ：建议，与 x window 有关的各种配置文件，尤其是 xorg.conf 这 x server 的配置文件
  - `/etc/sgml` ：建议，与 SGML 格式有管的各项配置文件
  - `/etc/xm` ：建议，与 XML 格式有关的各项配置文件

- `/lib`

  系统的函式库非常的多，而 lib 下放的是在 **开机时会用到的函数库**，以及在 /bin 和 /sbin 下的指令会呼叫的函数库。

  另外 FHS 还要求 /lib/modules 目录存在，主要放可抽换式的核心先关模块（驱动程序）

- `/media`

  放的是可移除的设备，例如 软盘、光盘、 DVD 等都暂时挂载于此。

  常见的有 /media/floppy、/media/cdrom 等

- `/mnt`

  如果暂时挂载某些额外的设备，一般建议可以放到这个目录中，在很早的时候该目录用途与 /mnt 相同， 只是有了 /media 后，这个目录就用来暂时挂载用了

- `opt`

  放第三方协力软件的目录。比如 KDE 这个桌面管理系统是一个独立的计划，不过他可以安装到 linux 系统中， 因此 KDE 就建议放置到该目录下了。

  如果你想要自行安装额外的软件（非原本 distribution 提供的），那么也建议放这里， 不过，以前的 linux 系统中，还是习惯放在 **/usr/local** 目录下

- `run`

  早期的 FHS 规定系统开机后所产生的各项信息应该放置到 /var/run 目录下， 新版的则规范到 /run 目录下了，由于 /run 可以用来内存仿真，因此效能上会好很多

- `/sbin`

  Linux 有非常多的指令是用来设置系统环境的，这些指令只有 root才能够利用来设置系统， 其他用户只能用来「查询」。放在 /sbin 下的为开机过程中所需要的，包括了开机、修复、还原系统所需要的指令。

  至于某些服务器软件程序，一般放置到 /usr/sbin 中。 至于本机自行安装的软件产生的系统执行文件（system binary）则放到 /usr/local/sbin 中了。

  常见的指令包括：fdisk、fsck、ifconfig、mkfs 等

- `/srv`

  src 可以视为 「service」的缩写，是一些网络服务启动之后，这些服务所需要取用的数据目录。 常见的服务如 www、ftp 等。例如：www 服务器需要的网页资源就可以放在 /srv/www 里面。

  不过，系统的服务数据如果尚未要提供给英特网上任何人浏览的话，预设还是建议放在 /var/lib 下

- `/tmp`

  一般用户或则是正在执行的程序暂时放文件的地方。该目录是任何人都可以存取的，所以需要定期清理一下。 因此 FHS 甚至建议在开机时，应该删除该目录下的文件

- `/usr`：属于第二层 FHS 规范，后续介绍

- `/var`：属于第二层 FHS 规范，主要放置变动性的数据，后续介绍

第二部分：FHS 建议可以存在的目录

- `/home`

  系统默认的用户目录。在你新增一个一般使用者账户时，默认的用户家目录都会规范到这里来。 比较重要的是，家的木有两种代号：

  - ~：代表目前这个用户的家目录
  - ~mrcode：则代表 mrcode 的家目录

- `/lib<qual>` 用来存放于 /lib 不同的格式的二进制函数库，例如支持 64 位的 /lib64 函数库

- `/root`

  系统管理员 root 的家目录。之所以放这里，是因为如果进入单人维护模式而仅挂载根目录时，该目录就能够拥有 root 的家目录， 所以会希望 root 的家目录与根目录放同一个分区槽中

事实上 FHS 针对目录所定义的标准就仅有上面的规范，不过还有其他的目录一需要了解下， 也是 linux 当中几个非常重要的目录：

- `/lost+found`

  这个目录使用标准的 ext2/3/4 文件系统格式才会产生的一个目录，目的是当文件系统发生错误时， 将一些遗失的片段放到这个目录下。

  不过如果使用的是 xfs 文件系统的话，就不会存在这个目录了

- `/proc`

  这个目录本身是一个「虚拟文件系统（virtual filesystem），放的数据都在内存当中， 例如系统核心、进程信息（process）、周边装置的状态以及网络状态等。

  因为这个目录下的数据都是内存当中，使用本身不占任何硬盘空间。比较重要的文件：

  ```text
  /proc
    cpuinfo
    dma
    interrupts
    ioports
    net/*
      
  ```

- `/sys`

  与 proc 非常类似，也是一个虚拟的文件系统，主要也是记录核心与系统硬件信息较相关的信息。 包括目前已加载的核心模块与核心侦测到的硬件装置信息等。同样不占用硬盘容量

### /usr 的意义与内容

根据 FHS 的基本定义， /usr 里面放置的数据属于可以分享的与不可变动的， 如果你知道如何透过网络进行分区槽的挂载（例如在服务器篇会谈到的 NFS 服务器）， 那么 /usr 确实可以分享给局域网内的其他主机来使用

/usr 不是 user 的缩写，而是 Unix Software Resource 的缩写（Unix 操作系统软件资源）， FHS 建议所有软件开发者，应该将他们的数据合理的分辨放置到这个目录下的次目录，而不要自行建立该软件自己独立的目录。

因为所有系统默认的软件（distribution 发布者提供的软件）都会放置到 /usr 下， 因此该目录类似 windows 「c:/windows 和 c:/Program files」这两个目录的综合体。

一般来说 /usr 的此目录建议有以下：

第一部分：FHS 要求必须要存在的目录

- `/usr/bin/`

  所有一般用户能够使用的指令都放在这里。 CentOS7 新版已经将全部的用户指令放在这里， 而使用连接文件的方式将 /bin 连接到这里。也就是说 /usr/bin 与 /bin 是一样的了。 而且 FHS 要求在此目录下不应该有子目录

- `/usr/lib/`

  基本上 与 /lib 功能相同，使用 /lib 就是连接到此目录的

- `/usr/local/`

  系统管理员在本机自行安装自己下载的软件（非 distribution 默认提供），建议安装到此目录。 比如，distribution 提供的软件较旧，想安装新的但是又不想移除旧版本的，就可以将新版安装到这里。

  该目录下也是具有 bin、etc、include、lib 的次目录

- `/usr/sbin`

  非系统正常运作所需要的系统指令。最长久的就是某些网络服务器软件的指令（daemon）。 不过功能基本与 /sbin 差不多，因此 /sbin 也是连接到此目录的

- `/usr/share/`

  主要放置只读架构的数据文件和共享文件。在该目录下的数据几乎是不分硬件架构均可读取的数据， 因为几乎上都是文本文件。常见的还有以下次目录 

  - `/usr/share/man`：联机帮助文件
  - `/usr/share/doc`：软件杂项的文件说明
  - `/usr/share/zoneinfo` 与时区有关的时区文件

第二部分：FHS 建议可以存在的目录

- `/usr/games/`：与游戏比较相关的数据

- `/usr/include`：

  c/c++ 等程序语言的档头（header）与包含档（include）放置处，当我们以 tarball 方式 （tar.gz 的方式安装软件）安装某些数据时，会使用到里头的许多包含档

- `/usr/libexe`

  某些不被一般使用者惯用的执行档或脚本，例如大部分的 x 窗口下的操作指令

- `/usr/lib<qual>`

  与 `/lib<qual>` 功能相同，连接过来的

- `/usr/src`

  一般源码建议放这里，src 有 source 的意思。 至于核心源码则建议放到 /usr/src/linux 目录下

### /var 的意义与内容

如果 /usr 是安装时会占用较大硬盘容量的目录，那么 /var 则是在运行后才会渐渐占用容量的。 主要放置的是针对常态性变动的文件，包括 cache、登录文件（log file）以及某些软件所产生的文件， 包括程序文件（lock file，run file），或则例如 mysql 数据库的文件等， 常见的目录有

第一部分：FHS 要求必须要存在的目录

- `/var/cache`：应用程序运行中使用的缓存文件

- `/var/lib`：

  程序本身执行过程中，需要用到的数据文件存放处。在此目录下各自的软件应该要有各自的目录， 比如：mysql 数据库放到 /var/lib/mysql 而 rpm 的数据库则放到 /var/lib/rpm

- `/var/lock`

  某些装置或是文件资源一次只能被一个程序使用，所以这里存放的是加锁的标识， 目前此目录已经挪到 /run/lock 中了

- `/var/mail`：个人电子邮件信箱目录，不过也被放置到了 /var/spool/mail 中了，通常两个目录互为连接文件

- `/var/run`

  某些程序或则是服务启动后，会将他们的 PID 放置在这个目录下，与 /run 相同，也连接到 /run 下了。 至于 PID 后续讲解

- `/var/spool`

  通常放置一些对了数据，这些数据被使用后通常都会被删除。 比如：系统受到新信会放到 /var/spool/mail 中，但使用者手下该信件后该封信原则上就会被删除。 信件如果展示寄不出去，则会放到 /var/spool/mqueue 中。等待被送出后会被删除。

  如果是工作排程数据（crontab）就会被放到 /var/spool/cron 目录中

建议在读完整个基础篇之后，可以挑战 FHS 官网英文文件，会让你对于 linux 操作系统的目录有更深入的了解

### 针对 FHS 各家 distribution 的异同，与 CentOS 7 的变化

由于 FHS 仅是定义出上层 / 与次层 /var 的目录内容应该放置的文件或目录， 其他的就由开发者自行配置了。

如： CentOS 网络设置数据放在 /etc/sysconfig/network-scripts 下。 但是 SuSE 的则放在 /etc/sysconfig/netwok 目录下，所以名称不一致，但是记住大致的 FHS 标准，差异性其实不大

centOS7 相对于老版做了改进，将许多原本应该要在 / 目录中的数据全部挪到 /usr 里面去，然后进行连接设置。 包括以下这些：

- /bin -> /usr/bin
- /sbin -> /usr/sbin
- /lib -> /usr/lib
- /lib64 -> /usr/lib64
- /var/lock -> /run/lock
- /var/run -> /run

## 目录树（directory tree）

在Linux下面，所有的文件与目录都是由根目录开始的!那是所有目录与文件的源头~ 然后再一个一个的分支下来，有点像是树枝状啊~因此，我们也称这种目录配置方式为:“目录树(directory tree)” 。主要特性如下：

- 目录树的起始点为根目录 `/`
- 每个目录可以使用本地端的分区（partition）文件系统，也可以使用网络上的文件系统。举例来说，就是可以利用 Network File System（NFS）服务器挂载某些特定的目录
- 每一个文件在此目录树种的文件名（包含完整路径）都是独一无二的

可以使用命令 `ls -l /` 来查看根目录下又哪些文件与数据。 下图将较为重要的文件数据列出来，那么目录树架构如下图这样。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linuxdirtree.png" style="zoom:67%;" />

学习了这么多，那么现在回去看看安装前 [主机规划与磁盘划分](https://zq99299.github.io/linux-tutorial/tutorial-basis/02/)，对于当初如何要这样划分， 现在你就明白了。

根据 FHS 的定义，最好能将 /var 独立出来，因为当 /var 死掉时，你的根目录还会活着，还可以进入救援模式。

## 绝对路径与相对路径

文件名与路径的写法分为：

- 绝对路径：由根目录开始写起的文件或目录，例如 /home/mrcode/.bashrc
- 相对路径：开头不是 `/` 则是相对路径，例如： ./home/mrcode

对于 `.` 的概念：

- `.`：代表当前目录，也可以使用 ./ 来表示
- `..`：代表上一层目录，也可以使用 ../ 来表示

## CentOS 的观察，linux 版本查询

除了第一章中谈到的 Linux distribution 的差异性，除了 FHS 之外，还有个 Linux Standard Base（LSB） 的标准是可以依循的。

可以简单的使用 ls 来查看 FHS 规范的目录是否正确的存在你的 Linux 系统中， 那么 支持 LSB 标准的 distribution 在 `https://www.linuxbase.org/lsb-cert/productdir.php?by_lsb` 中被列出

如果想要知道确切的核心与 LSB 所需求的几种重要的标准的话，就需要例如 `uname` 与 `lsb_release` 等指令来查询了。

`lsb_release` 软件不是默认安装软件了，因此需要先安装。

但是这里，新安装的机器居然不能连接外网，可以与宿主机通网了。那么这里无法安装，只能先记录命令

```bash
# 检查 linux 核心与操作系统的位版本
# 查看核心版本
[mrcode@study ~]$ uname -r
3.10.0-1062.el7.x86_64
# 查看操作系统位版本
[mrcode@study ~]$ uname -m
x86_64

# 如果可以联网的话，可以安装 lsb_release 指令（使用 root 身份）
yum install redhat-lsb
# 安装完成之后，使用指令
lsb_release -a

# 就会显示 LSB Version 等版本信息，如下类似的信息
Distributor ID：CentOS
Description : CentOS linux release 7.0(Core)
Release :  7.0
```

-----

# Linux 文件与目录管理

本章进一步操作与管理文件及目录，包括在不同的目录间变换、建立、删除目录， 建立与删除文件、查找文件、查阅问价内容等，都会在这个章节进行简单介绍。

## 相对路径与绝对路径

- 绝对路径：由根目录开头，如 /home/mrcode
- 相对路径：不是由根目录开头的，如 ./mrcode

## 目录的相关操作

以下的特殊目录需要着重了掌握

- `.`：代表此层目录
- `..`：上一层目录
- `-`：前一个工作目录
- `~`：目前用户身份坐在的家目录
- `~account`：表示 account 这个用户的家目录（account 是个账户名称）

需要特别注意的是，在所有目录下都会看到两个目录 `. 与 ..`，当前目录和上一层目录。

那么 linux 中，根目录有没有上层目录存在？

```bash
[mrcode@study ~]$ ls -al /
# 可以看到这两个目录的属性一模一样，所以这两个目录其实都是同一个目录
dr-xr-xr-x.  17 root root  224 Oct  4 18:31 .
dr-xr-xr-x.  17 root root  224 Oct  4 18:31 ..
```

那么下面讲解下几个常见的处理目录的指令：

- `cd`：变换目录
- `pwd`：显示当前目录
- `mkdir`：建立一个新的目录
- `rmdir`：删除一个空的目录

### cd（change directory）变换目录

```bash
[dmtsai@study ~]$ su - # 先切换身份成为 root 看看!
[root@study ~]# cd [相对路径或绝对路径]
# 最重要的就是目录的绝对路径与相对路径，还有一些特殊目录的符号啰!
[root@study ~]# cd ~dmtsai
# 代表去到 dmtsai 这个使用者的主文件夹，亦即 /home/dmtsai
[root@study dmtsai]# cd ~
# 表示回到自己的主文件夹，亦即是 /root 这个目录
[root@study ~]# cd
# 没有加上任何路径，也还是代表回到自己主文件夹的意思喔!
[root@study ~]# cd ..
# 表示去到目前的上层目录，亦即是 /root 的上层目录的意思;
[root@study /]# cd -
# 表示回到刚刚的那个目录，也就是 /root 啰~
[root@study ~]# cd /var/spool/mail
# 这个就是绝对路径的写法!直接指定要去的完整路径名称!
[root@study mail]# cd ../postfix
# 这个是相对路径的写法，我们由/var/spool/mail 去到/var/spool/postfix 就这样写!
```

`cd`是Change Directory的缩写，这是用来变换工作目录的指令。注意，目录名称与cd指令之间 存在一个空格。 一登陆Linux系统后，每个帐号都会在自己帐号的主文件夹中。那回到上一层 目录可以用“ cd .. ”。 利用相对路径的写法必须要确认你目前的路径才能正确的去到想要去的目录。例如上表当中最后一个例子， 你必须要确认你是在/var/spool/mail当中，并且知道 在/var/spool当中有个mqueue的目录才行啊~ 这样才能使用cd ../postfix 去到正确的目录说， 否则就要直接输入cd /var/spool/postfix 啰~

### pwd（print Working Directory） 显示当前所在目录

```bash
[mrcode@study mail]$ pwd
/var/mail
# 带参数 P 是显示真实的路径，而不是连接（link）路径，然而 /var/mail 就是一个连接路径
[mrcode@study mail]$ pwd -P
/var/spool/mail
# 通过命令也能看到，连接到了 spool/mail 目录中
[mrcode@study mail]$ ls -ld /var/mail
lrwxrwxrwx. 1 root root 10 Oct  4 18:21 /var/mail -> spool/mail
```

pwd是Print Working Directory的缩写，也就是显示目前所在目录的指令。

### mkdir 建立新目录

语法如下

```bash
mkdir [-mp] 目录名称

-m：配置文件案的权限，直接设定，不需要看预设权限（umask）的脸色
-p：将该路径上所有的目录都创建出来（当然不存在的话）
```

练习

```bash
# 进入临时目录
[mrcode@study ~]$ cd /tmp/
[mrcode@study tmp]$ mkdir test
# 不带参数 -p 不能创建多级目录
[mrcode@study tmp]$ mkdir test1/test2/test3/test4
mkdir: cannot create directory ‘test1/test2/test3/test4’: No such file or directory
[mrcode@study tmp]$ mkdir -p test1/test2/test3/test4

# 创建目录时直接配置该目录的权限
[mrcode@study tmp]$ mkdir -m 711 test2
[mrcode@study tmp]$ ls -ld test*
# 这些是创建目录默认的权限
drwxrwxr-x. 2 mrcode mrcode  6 Oct 11 04:32 test
drwxrwxr-x. 3 mrcode mrcode 19 Oct 11 04:33 test1
# 这个是创建目录时直接配置的权限
drwx--x--x. 2 mrcode mrcode  6 Oct 11 04:35 test2
```

###  rmdir 删除空的目录

语法如下

```bash
rmdir [-p] 目录名称

-p：「上层」的「空目录」也一起删除

```

练习

```bash
[mrcode@study tmp]$ ls -ld test*
drwxrwxr-x. 2 mrcode mrcode  6 Oct 11 04:32 test
drwxrwxr-x. 3 mrcode mrcode 19 Oct 11 04:33 test1
drwx--x--x. 2 mrcode mrcode  6 Oct 11 04:35 test2
# 该目录下无数据，可直接删除
[mrcode@study tmp]$ rmdir test
# 该目录下由多个目录，就无法阐述了，会报错
[mrcode@study tmp]$ rmdir test1
rmdir: failed to remove ‘test1’: Directory not empty
# 这里使用 -p 删除最后一个目录，但是当再次查看的时候，test4 的上层空的目录都不在了
[mrcode@study tmp]$ rmdir -p test1/test2/test3/test4/
[mrcode@study tmp]$ ls -ld test*
drwx--x--x. 2 mrcode mrcode 6 Oct 11 04:35 test2

# -p 删除上级空目录是什么意思，下面再来体验下

# 创建了多个目录
[mrcode@study tmp]$ mkdir -p test1/test2/test3/test4
# 然后在 test1 中创建了一个 txt 文件
[mrcode@study tmp]$ touch test1/txt
# 删除的时候，就报错了，无法删除 test1，因为该目录下有 txt 文件
# 但是注意，只是 test1 没有被删除， test2、test3、test4 还是被删除了的
[mrcode@study tmp]$ rmdir -p test1/test2/test3/test4/
rmdir: failed to remove directory ‘test1’: Directory not empty

```

但是如果想把该目录下所有的东西都删除呢？你可以使用指令 `rm -r test1` 就能全部删掉了， 相对来说，rmdir 没有这么危险

### 关于执行文件路径的变量：`$PATH`

前面讲解 FHS 后，我们知道 ls 指令完整文件名为 /bin/ls（这是绝对路径）， 那么为什么我们可以在任何地方执行 /bin/ls 这个指令呢？这是因为换了变量 PATH 的能力

当我们执行一个指令的时候，系统会按照 PATH 的设定去每个 PATH 定义的目录下搜索对应的可执行文件 （比如 ls），如果在 PATH 定义的目录中含有多个文件名为 ls 的可执行文件，那么先搜索到的被执行

```bash
# 打印变量的信息，使用 echo ,「$」表示接一个变量
[mrcode@study tmp]$ echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin

# 注意，每个账户的 path 值也是不一样的
[root@study ~]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin

```

PATH(一定是大写)这个变量的内容是由一堆目录所组成的，每个目录中间用冒号(`:`)来隔 开， 每个目录是有“顺序”之分的。

```bash
# 先把 ls 移动到 /bin 目录之外去，再运行 ls 看能不能运行？
[root@study ~]# mv /bin/ls ../
[root@study ~]# ls
bash: ls: 未找到命令...
相似命令是： 'lz'
# 现在已经报错找不到命令了，是因为 / 并不再 PATH 变量中

# 但是可以通过路径来运行
[root@study ~]# /l
lib/   lib64/ ls    # ls 的确被移动到 根目录下了
# 这里直接通过绝对路径运行指令
[root@study ~]# /ls
anaconda-ks.cfg  initial-setup-ks.cfg  initial-setup-ks-mrcode.cfg

# 要想不用绝对/相对路径也能使用 ls ，那么将 根目录加入到 PATH 中即可
# 也可以使用 PATH="${PATH}:/"  来配置
[root@study ~]# PATH="$PATH:/"
[root@study ~]# ls
anaconda-ks.cfg  initial-setup-ks.cfg  initial-setup-ks-mrcode.cfg

# 把 ls 移回原来的目录
[root@study ~]# mv /ls /bin/
# 可能会出现找不到指令了，没有关系，可能是因为指令参数被快取得关系
# 只要 exit 再登入 su -  就可以使用了
# 另外说一句，刚刚在命令行中把根目录添加到 PATH 中，不是永久的，退出后，再登录就失效了
[root@study ~]# ls
-bash: /ls: 没有那个文件或目录

# 假设 /usr/local/bin/ls 与 /bin/ls 两个指令，哪个先被执行？
# 可以使用 echo $PATH 或则 ${PATH} 直接显示某一个变量
[root@study ~]# ${PATH}
# 这里看哪一个目录在最前面，就是哪个目录下的 ls 先执行
-bash: /usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin: 没有那个文件或目录
[root@study ~]# PATH
bash: PATH: 未找到命令...

```

为什么不建议把 `.` 当前目录添加到 **PATH** 路径中？这其实是为了安全起见，不建议添加到 **PATH** 中， 比如在 **/tmp** 目录下，因为是大家都可以写的，有人搞破坏，写了一个 **ls** 的指令，但是里面写的是删除文件的， 这样就会先收到这个恶意的命令

由上面的示例，我们可以知道几件事情：

- 不同身份使用者预设的 PATH 不同，默认能够随意执行的指令也不同（如 root 与 mrcode)
- PATH 是可以修改的
- 使用绝对路径或相对路径直接指定某个指令文件名来执行，会比搜寻 PATH 来的正确
- 指令应该要放置到正确的目录下，执行才比较方便
- 当前目录「.」建议不要放到 PATH 中

-----

## 文件与目录管理

### 文件与目录的检视:  ls

```bash
ls [-aAdfFhilnrRSt] 文件名或目录名称
ls [--color={never,auto,always}] 文件名或目录名称
ls [--full-time] 文件名或目录名称
```

选项与参数：

- **a**：全部的文件，连同隐藏文件（开头为 .）一起列出来（常用）

- A：全部的文件，连同隐藏文件（不包括 . 和 .. 这两个目录)

- **d**：仅列出目录本身，而不是列出目录内的文件数据（常用）

- f：直接列出结果，而不进行排序（ls 默认以文档名排序）

- F：根据文件、目录等信息，给予附加数据结构

  如：

  - `*` 代表可执行文件，
  - `/` 代表目录
  - `=` 代表 socket 文件
  - `|` 代表 FIFO 文件

- h：将文件容量以人类较易读的方式（例如 GB、KB）列出来
- i：列出 inode 号码，inode 的意义后续讲解
- **l**：长数据串输出，包含文件的属性与权限等数据（常用）
- n：列出 UID 与 GID 而非使用者与群组的名称（UID 与 GID 会在账户管理中讲解）
- r：将排序结果反向输出，例如原本文件名由小到大，反向则由大到小
- R：连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来
- S：按文件容量大小排序
- t：按时间排序
- color 颜色配置
  - never：不要依据文件特性给予颜色显示
  - always：显示颜色
  - auto：让系统自行依据设置来判断是否给予颜色
- full-time：以完整时间模式）包含年月日时分输出
- `time={atime，ctime}`：输出 access 时间或改变权限属性时间（ctime），而非内容变更时间

在 linux 中 ls 指令可能是 最常用的，由于文件所记录的信息实在是太多了， 所以默认显示的只有：非隐藏文档、以文件名进行排序、文件名代表的颜色显示

实践练习

```bash
# 将家目录下的所有问价列出来，包含属性与隐藏文件
[mrcode@study ~]$ ls -al ~
total 40
drwx------. 18 mrcode mrcode 4096 Oct  8 23:15 .   # 深蓝色
drwxr-xr-x.  4 root   root     42 Oct  8 23:01 ..  # 深蓝色
-rw-------.  1 mrcode mrcode 2927 Oct 11 05:16 .bash_history
-rw-r--r--.  1 mrcode mrcode   18 Aug  8 20:06 .bash_logout
-rw-r--r--.  1 mrcode mrcode  193 Aug  8 20:06 .bash_profile
-rw-r--r--.  1 mrcode mrcode  231 Aug  8 20:06 .bashrc
drwx------. 16 mrcode mrcode 4096 Oct  4 21:33 .cache # 深蓝色

# 接上题，不显示颜色，但在文件名末尾显示该文件名代表的类型
# 实际的终端中看，颜色就没显示了
[mrcode@study ~]$ ls -alF --color=never ~
total 40
drwx------. 18 mrcode mrcode 4096 Oct  8 23:15 ./
drwxr-xr-x.  4 root   root     42 Oct  8 23:01 ../
-rw-------.  1 mrcode mrcode 2927 Oct 11 05:16 .bash_history
-rw-r--r--.  1 mrcode mrcode   18 Aug  8 20:06 .bash_logout
-rw-r--r--.  1 mrcode mrcode  193 Aug  8 20:06 .bash_profile
-rw-r--r--.  1 mrcode mrcode  231 Aug  8 20:06 .bashrc
drwx------. 16 mrcode mrcode 4096 Oct  4 21:33 .cache/

# 显示完整的修改实践（modification time）
[mrcode@study ~]$ ls -al --full-time ~
total 40
drwx------. 18 mrcode mrcode 4096 2019-10-08 23:15:44.109000000 +0800 .
drwxr-xr-x.  4 root   root     42 2019-10-08 23:01:04.516000000 +0800 ..
-rw-------.  1 mrcode mrcode 2927 2019-10-11 05:16:27.662000000 +0800 .bash_history
-rw-r--r--.  1 mrcode mrcode   18 2019-08-08 20:06:55.000000000 +0800 .bash_logout
-rw-r--r--.  1 mrcode mrcode  193 2019-08-08 20:06:55.000000000 +0800 .bash_profile
-rw-r--r--.  1 mrcode mrcode  231 2019-08-08 20:06:55.000000000 +0800 .bashrc
drwx------. 16 mrcode mrcode 4096 2019-10-04 21:33:12.075000000 +0800 .cache
```

可以看到 **ls** 支持的功能很多，这些都是因为 **linux** 文件系统记录了很多有用的信息的缘故， 那么这些与权限、属性有关的数据放在 **i-node** 里面的。后续会深入讲解 i-node 的

另外，由于 `ls -l` 使用频率很高，为此，很多 distribution 在预设情况中已经将 `ll` 设定为 `ls -l` 的意思了。其实，那个功能是 Bash shell 的 alias 功能

## 复制、删除与移动：cp、rm、mv

- `cp`：**copy** 复制文件，该指令还有其他功能，如建立连接档、比较亮文件的新旧而给予更新，复制整个目录等功能
- `mv`：**move** 移动目录与文件，也可以直接拿来当做更名（rename）
- `rm`：**remove** 移除文件

### cp 复制文件或目录

```bash
cp [-adfilprsu] 来源文件（source）目标文件（destination）
cp [options] source1 source2 source3 .... directory
```

选项与参数：

- **a**：相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明；（常用）
- **d**：若来源文件为链接文件的属性（link file），则复制链接文件属性而非文件本身
- **f**：强制（force）的意思，若目标文件已经存在且无法开启，则移除后再尝试一次
- **i**：若目标文件已经存在时，在覆盖时会先询问动作的进行。（常用）
- **l**：进行硬式链接（hard link）的链接档的建立，而非复制文件本身
- **p**：连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性；（备份文件常用）
- **r**：递归持续复制，用于目录的复制行为。（常用）
- **s**：复制称为符号链接文件（symbolic link）
- **u**：destination 与 source 旧才更新 destination，或 destination 不存在的情况下才复制

`--preserve=all`：除了 **-p** 的权限相关参数外，还加入 **SELinux** 的属性，**links**、**xattr** 等也复制

最后需要注意的是：如果来源档有两个以上，则最后一个目的文件一定要是目录才行

而且不同身份者执行这个指令会有不同的结果产生，尤其是 **-a**、**-p** 的选项，对于不同身份来说， 差异则非常的大。

实践练习

```bash
# 使用 root 身份，将家目录下的 .bashrc 复制到 /tmp 下，并更名为 bashrc
[root@study ~]# cp ~/.bashrc /tmp/bashrc
# 加上 -i 属性，由于上面已经复制过一次了，所以 bashrc 文件已经存在
[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
cp：是否覆盖"/tmp/bashrc"？ # n 不覆盖，y 覆盖

# 变换目录到 /tmp ,并将 /var/log/wtmp 复制到 /tmp 且观察属性
[root@study tmp]# cd /tmp/
[root@study tmp]# cp /var/log/wtmp .
# ls 可以列出多个文档名，这里列出了两个，刚好可以对比他们的属性
[root@study tmp]# ls -l /var/log/wtmp wtmp
-rw-rw-r--. 1 root utmp 44160 10月 13 14:38 /var/log/wtmp
-rw-r--r--. 1 root root 44160 10月 13 15:42 wtmp
# 可以看到上面不加任何选项，被复制之后的某些属性或权限已经被改变了
# 这是个很重要的特性，要注意，文件建立的时间也不一样
# 下面将所有的属性权限都一起复制过来
[root@study tmp]# cp -a /var/log/wtmp wtmp2
[root@study tmp]# ls -l /var/log/wtmp wtmp2
-rw-rw-r--. 1 root utmp 44160 10月 13 14:38 /var/log/wtmp
-rw-rw-r--. 1 root utmp 44160 10月 13 14:38 wtmp2

```

上面示例中，不加任何选项会使用预设的配置，比如常常会复制别人的数据（当然需要有 read 权限）， 总是希望复制到的数据最后是我们自己的，所以上面示例才有由 utmp 变更为 root

由于具有这个特性，因此在进行备份的时候，需要特别注意的特殊权限文件，例如密码文件（/etc/shadow) 以及一些配置文件，就不能直接以 cp 来复制，需要将全部的属性都原样复制过来

```bash
# 复制 etc 目录下的所有内容
[root@study tmp]# cp /etc/ /tmp/
cp: 略过目录"/etc/"  # 提示该目录不能直接复制，要加上 -r
[root@study tmp]# cp -r /etc/ /tmp/
# 再次强调，-r 只是能递归复制，但是文件权限等属性还是会更改
# 因此可以使用 cp -a /etc/ /tmp/ 来复制，尤其是在备份的情况下

```

#### 创建符号链接与实体链接

```bash
# 将之前复制过来的 bashrc 建立一个链接档
[root@study tmp]# pwd
/tmp
# 先查看该文件的属性
[root@study tmp]# ls -l bashrc
-rw-r--r--. 1 root root 176 10月 13 15:38 bashrc
# 分别建立 符号链接 和硬式链接
[root@study tmp]# cp -s bashrc bashrc_link
[root@study tmp]# cp -l bashrc bashrc_hlink
[root@study tmp]# ls -l bashrc*
# 注意看这里的数值，源文件是 1 这里变成了 2
-rw-r--r--. 2 root root 176 10月 13 15:38 bashrc
-rw-r--r--. 2 root root 176 10月 13 15:38 bashrc_hlink
# 下面这条数据，在终端中，bashrc_link 会显示浅蓝色
lrwxrwxrwx. 1 root root   6 10月 13 15:55 bashrc_link -> bashrc

```

使用 **-l** 及 **-s** 都会建立连接档（link file），那么有什么不同呢?

- **bashrc_link**：使用 s 创建出来的，是符号链接（symbolic link），简单说是一个快捷方式，会链接到 bashrc 中去。有一个 -> 的符号
- **bashrc_hlink**：使用 l 创建出来的，是实体链接（hard link）

实体链接与源文件的属性与权限一模一样，与尚未链接前的差异是 第二栏 的 link 由 1 变成了 2. 由于实体链接与 i-node 有关，这里先不深入了。

#### 备份常见下的复制

当源文件比目标新的时候才复制

```bash
# 先查看两个文件的时间，可以看到源文件是 2013 年，比目标文件旧
[root@study tmp]# ls -l ~/.bashrc /tmp/bashrc
-rw-r--r--. 1 root root 176 12月 29 2013 /root/.bashrc
-rw-r--r--. 2 root root 176 10月 13 15:38 /tmp/bashrc
# 这里使用 -u 复制后，没有任何提示
[root@study tmp]# cp -u ~/.bashrc /tmp/bashrc
# 再次查看，发现没有复制成功，当前时间是 16:14 了，如果成功，目标文件的时间也会变更
[root@study tmp]# ls -l ~/.bashrc /tmp/bashrc
-rw-r--r--. 1 root root 176 12月 29 2013 /root/.bashrc
-rw-r--r--. 2 root root 176 10月 13 15:38 /tmp/bashrc

```

#### 连接文档的复制

```bash
# 该文件是一个符号链接文件
[root@study tmp]# ls -l bashrc_link
lrwxrwxrwx. 1 root root 6 10月 13 15:55 bashrc_link -> bashrc
# 这里使用不加参数复制和加参数复制
[root@study tmp]# cp bashrc_link bashrc_link_1
[root@study tmp]# cp -d bashrc_link bashrc_link_2
[root@study tmp]# ls -l bashrc bashrc_link*
-rw-r--r--. 2 root root 176 10月 13 15:38 bashrc
lrwxrwxrwx. 1 root root   6 10月 13 15:55 bashrc_link -> bashrc
# 可以看到，不加参数复制把源文件复制过来了
-rw-r--r--. 1 root root 176 10月 13 16:16 bashrc_link_1
# 添加 -d 参数，只复制了链接文件本身
lrwxrwxrwx. 1 root root   6 10月 13 16:16 bashrc_link_2 -> bashrc
```

#### 多个文件同时复制到通一个目录下

```bash
cp ~/.bashrc ~/.bash_history /tmp/
 
```

#### 身份不同执行 cp 指令表现不同

```bash
# 使用 mrcode 身份, -a 把文件原原本本的复制过来
[mrcode@study ~]$ cp -a /var/log/wtmp /tmp/mrcode_wtmp
[mrcode@study ~]$ ls -l /var/log/wtmp /tmp/mrcode_wtmp
-rw-rw-r--. 1 mrcode mrcode 44160 Oct 13 14:38 /tmp/mrcode_wtmp
-rw-rw-r--. 1 root   utmp   44160 Oct 13 14:38 /var/log/wtmp
    
```

### rm (移除文件或目录)

- f：force 强制的意思，忽略不存在的文件，不会出现警告信息
- i：互动模式，在闪出去会询问使用者是否操作
- r：递归删除

实践练习

```bash
# 互动模式删除
[root@study ~]# cd /tmp/
[root@study tmp]# rm -i bashrc
rm：是否删除普通文件 "bashrc"？y
# 删除的文件名还可以使用通配符， * 表示 0 个或多个
[root@study tmp]# rm -i bashrc*
rm：是否删除普通文件 "bashrc_hlink"？y
rm：是否删除符号链接 "bashrc_link"？y
rm：是否删除普通文件 "bashrc_link_1"？y
rm：是否删除符号链接 "bashrc_link_2"？y

# 删除一个目录， rmdir 无法删除非空目录
[root@study tmp]# rmdir /tmp/etc/
rmdir: 删除 "/tmp/etc/" 失败: 目录非空
# 这里使用 r 参数递归删除
[root@study tmp]# rm -r /tmp/etc/
# 但是出现了交互模式，是因为 root 身份预设加入了参数 -i
rm：是否进入目录"/tmp/etc/"? y
rm：是否删除普通文件 "/tmp/etc/fstab"？y
rm：是否删除普通空文件 "/tmp/etc/crypttab"？y
rm：是否删除符号链接 "/tmp/etc/mtab"？y
rm：是否删除普通文件 "/tmp/etc/resolv.conf"？y
rm：是否进入目录"/tmp/etc/fonts"? ^C

# 在指令前添加反斜杠，可以忽略掉 alias 的指定选项，至于 alias 后续再 bash 章节详讲
\rm -r /tmp/etc/

# 删除一个带有 - 开头的文件
# 先使用 touch 建立一个空文件
touch ./-aaa
# 注意：在 /tmp 下文件太多，常见好的文件就在最前面
ls -l
# 看到文件大小是 0 ，这是一个空文件
-rw-r--r--.  1 root   root       0 10月 13 19:05 -aaa-

# 删除刚才创建的，肯定不会成功的，之前讲解过 - 是个特殊字符，表示选项
[root@study tmp]# rm -aaa-
rm：无效选项 -- a
Try 'rm ./-aaa-' to remove the file "-aaa-".  # 但是这里给出了建议，添加双引号删除
Try 'rm --help' for more information.

[root@study tmp]# rm "./-aaa-"
rm：是否删除普通空文件 "./-aaa-"？y


```

由于 root 的是天神，所以大部分 distribution 都默认添加了 -i 的选项，删除前请三思啊。

### mv 移动文件与目录或更名

```bash
mv [-fiu]  source destination
mv [options] source1 source2 ... directory
```

- f：强制，如果目标文件已经存在，不会询问，直接覆盖
- i：若目标文件已经存在时，就会询问是否覆盖
- u：若目标已经存在，且 source 比较新，才会功更新该文件

```bash
[root@study tmp]# cd /tmp/   
[root@study tmp]# cp ~/.bashrc bashrc
# 创建目录
[root@study tmp]# mkdir mvtest
# 将刚刚拷贝的 bashrc 复制到目录中
[root@study tmp]# mv bashrc mvtest/
# 目录更名
# 其实还有一个指令 rename，该指令专职进行多个文档名同时更名，并非针对单一文件更名
# 与 mv 不同，详细请 man rename
[root@study tmp]# mv mvtest/ mvtest2

```

### 取得路径的文件名与目录名称，basename、dirname

每个文件的完整文档名包含了前面的目录与最终的文件名，而每个文档名的长度都可达 255 个字符， 那么怎么区分哪个是文件名？哪个是目录名？可以使用斜线「/」来分辨

一般要获取文件名或目录名称，都是些程序的手来判断用，所以这部分指令可以用在后续的 shell scripts 里面。

```bash
# /etc/sysconfig/network 比如这个路径
# 可以使用指令分别获取到他的目录与文件名
[root@study tmp]# basename /etc/sysconfig/network
network
[root@study tmp]# dirname /etc/sysconfig/network
/etc/sysconfig
```

## 文件内容查阅

查阅一个文件内容是，这里有相当多有趣的指令来了解下， 最常使用的可以说是 **cat** 、**more**、**less**，那么当查阅一个很大型的文件的时候， 想要在几百兆的文件内容中找到我们想要的数据怎么办？下面的指令能发挥出一些作用

- **cat**：由第一行开始显示文件内容
- **tac**：从最后一行开始显示，可以看出 **tac** 是 **cat** 的倒着写
- **nl**：显示的时候顺道输出行号
- **more**：一页一页的显示文件内容
- **less**：与 **more** 类似，但是比 **more** 更好的是，他可以往前翻页
- **head** 只看头几行

### 直接检视文件内容

直接查阅一个文件的内容可以使用 **cat**、**tac**、**nl** 这几个指令

### cat（concatenate）

```bash
cat [-AbEnTv]

```

- A：相当于 -vET 的整合选项，可列出一些特殊字符而不是空白
- b：列出行号，仅针对非空白行做行号显示，空白行不标行号
- E：将结尾的断行字符 $ 显示出来
- n：打印出行号（包含空白行）
- T：将 tab 按键以 ^I 显示出来
- v：列出一些看不出来的特殊字符

实践练习

```bash
[root@study tmp]# cat /etc/issue
\S
Kernel \r on an \m

# 带行号显示，最后还有一行空白行呢。对于大文件要找某个特定的行时，有点用处
[root@study tmp]# cat -n /etc/issue
     1  \S
     2  Kernel \r on an \m
     3  

```

下面练习显示特殊的内容

```bash
[root@study tmp]# cat -A /etc/man_db.conf
#^I^I*MANPATH*     ->^I*CATPATH*$
#$
MANDB_MAP^I/usr/man^I^I/var/cache/man/fsstnd$
MANDB_MAP^I/usr/share/man^I^I/var/cache/man$

# 上面只是部分内容，说下差异
# 断行以 $ 显示，可以发现每行后面都有 $ ，这个其实就 window 中的换行把？
# tab 以 ^I 显示
# windows 的断行字符是 ^M$
# 这部分在 vim 软件介绍时会再次说明
```

### tac 反向列示

```bash
# 从最后一行开始显示
[root@study tmp]# tac /etc/issue

Kernel \r on an \m
\S

```

### nl 添加行号打印

```bash
nl [-bnw] 文件
    
```

- b：指定行号指定的方式，主要有两种
  - `-b a`：表示不论是否为空行，也同样列出行号（类似 cat -n）
  - `-b t`：如果有空行，空行不要列出行号（默认值）
- n：列出行号表示的方法，主要有三种
  - `-b ln`：行号在屏幕的最左方显示
  - `-b rn`：行号在自己字段的最右方显示，且不加 0
  - `-b rz`：行号在自己字段的最有方显示，且加 0
- w：行号字段的占用字符数

实践练习

```bash
# 用 nl 列出 /etc/issue 的内容
# 默认不显示空行的行号
[root@study tmp]# nl /etc/issue
     1  \S
     2  Kernel \r on an \m

# 显示空行行号
[root@study tmp]# nl -b a /etc/issue
     1  \S
     2  Kernel \r on an \m
     3  
# 行号自动补 0，前面说的左右，看下面的对比，这个右是指，行号区域的左右
[root@study tmp]# nl -b a -n rz /etc/issue
000001  \S
000002  Kernel \r on an \m
000003  
[root@study tmp]# nl -b a -n rn /etc/issue
     1  \S
     2  Kernel \r on an \m
     3  
[root@study tmp]# nl -b a -n ln /etc/issue
1       \S
2       Kernel \r on an \m
3       

```

### 可翻页检视

#### **more 一页一页翻动**

```bash
[root@study tmp]# more /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath(5) man page.
#
--More--(14%)   # 重点在这一行，你的光标也会在这里等待你的指令
```

在 `more` 程序中，有几个按键可以按：

- 空格键（**space**）：向下翻一页
- **Enter**：向下翻一行
- `/字符串`：在显示的内容中，向下搜索「字符串」这个关键词
- **q**：立即离开 **more**
- **b** 或 **ctrl+b**：向前翻页，只针对文件有用，对管线（管道 `|`）无用

#### less 一页一页翻动

```bash
# 使用指令后，就会进入到 less 环境
less /etc/man_db.conf

# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining

注意，刚进入的时候，没有光标，可以直接输入 : 或则方向下键，就会在最下面出现 「:光标」这里就可以输入指令了
```

可以使用的按键和指令有

- 空格键：向下翻一页
- pagedown：向下翻一页
- pageup：向上翻一页
- `/字符串`：向下搜索字符串；注意这个斜杠也是需要输入的，不是在 「:」输入，：也和这个是一个功能
- `?字符串`：向上搜索字符串
- n：重复前一个搜索（与 / 或 ？有关）
- N：反向的重复前一个搜索
- g：前进到这个资料的第一行
- G：前进到这个资料的最后一行去（注意是大写）
- q：离开 less 这个程序

此外，man page 就是调用 less 来显示说明文件内容的，所以看上去很相似

笔者工作中查看日志中有用得数据的时候，就是这个 less 了，但是只知道 shift+g 可以前进到最后一行去，原来 shift+g 其实就是输入了大写的 G 指令

### 资料摘取

可以将输出的资料做一个最简单的摘取，如去除文件前面几行（`head`）或则后面几行（`tail`）， 需要注意的是， `head` 和 `tail` 都是以行为单位来进行摘取的

#### head 取出前面几行

```bash
head [-n number] 文件

-n：后面接数字，表示摘取几行

```

```bash
# 默认显示前 10 行，可以指定显示 20 行
head -n 20 /etc/man_db.conf

# 注意后面的数值为负数
# 该文件共有 131 行，这里是的意思就是，从尾部 -128 行，剩下的内容显示
# 也就是说，忽略显示后 128 行的数据
[root@study tmp]# head -n -128 /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.


```

#### tail 取出后面几行

```bash
tail [-nf number] 文件

-n ：后面接数字，表示显示几行
-f ：表示持续侦测后面所接的档名，要等到按下 ctrl+c 才会结束 tail 的侦测

```

```bash
# 默认显示最后 10 行
tail /etc/man_db.conf
# 显示最后 20 行
tail -n 20 /etc/man_db.conf
# 忽略显示前 100 行的数据，也就是说显示 100 行后的数据
tail -n +100 /etc/man_db.conf


# 这个就是笔者最常用查看某个项目当前滚动日志的方式了
tail -f /var/log/messages

```

组合使用示例

```
# 获取 第 11 到 20 行的数据
# 思路是：先取前 20 行数据出来，再从这 20 行里面取后 10 行数据
[root@study tmp]# head -n 20 /etc/man_db.conf | tail -n 10

# 这个 | 就是管线的意思

```

`|`：管线/管道符，前面的指令所输出的信息，请透过管线交由后续的指令继续使用。后续会详细讲解

上面的例子，其实我也不知道到底取出来的行数对不对，那么就可以使用管线来组合其他的指令使用

```bash
# 先使用 cat -n 显示行号，再交给后续的指令
# 我这里是显示 第 18 行到 20 行的内容
[root@study tmp]# cat -n /etc/man_db.conf | head -n 20 | tail -n 3
    18  #MANDATORY_MANPATH                      /usr/src/pvm3/man
    19  #
    20  MANDATORY_MANPATH                       /usr/man

```

#### 非纯文本 od

上面讲解了读取出文本的内容，那么想阅读非文本文件呢？比如查看 **/usr/bin/passwd** 文档， 使用上面提出来的指令读取就会乱码。

可以使用 **od** 指令来读取

```bash
od [-t TYPE] 文件
    
```

type 选项为：

- a：利用默认的字符来输出
- c：使用 ASCII 字符来输出
- `d[size]`：十进制（decimal）输出数据，每个整数占用 size bytes
- `f[size]`：浮点数（floating）输出数据
- `o[size]`：八进制（octal)
- `x[size]`：十六进制（hexadecimal）

实践练习

使用 ASCII 展示

```bash
[root@study ~]# od -t c /usr/bin/passwd
0000000 177   E   L   F 002 001 001  \0  \0  \0  \0  \0  \0  \0  \0  \0
0000020 003  \0   >  \0 001  \0  \0  \0   H   2  \0  \0  \0  \0  \0  \0
0000040   @  \0  \0  \0  \0  \0  \0  \0 220   e  \0  \0  \0  \0  \0  \0
0000060  \0  \0  \0  \0   @  \0   8  \0  \t  \0   @  \0 035  \0 034  \0
0000100 006  \0  \0  \0 005  \0  \0  \0   @  \0  \0  \0  \0  \0  \0  \0

# 最左边第一栏以 8 进制来表示 bytes 数。
# 比如 00000020 表示是第16 个 bytes (2x8)

```

使用 8 进制位列出存储值与 ASCII 的对照表

```bash
[root@study ~]# od -t oCc /etc/issue
0000000 134 123 012 113 145 162 156 145 154 040 134 162 040 157 156 040
          \   S  \n   K   e   r   n   e   l       \   r       o   n    
0000020 141 156 040 134 155 012 012
          a   n       \   m  \n  \
0000027

# 上面是八进制表示，下面是对应的 ascii 字符

```

对照指令对于工程师来说可能更有用处，上面是文件是一个纯文本文件，显示了字符的 ACCIS 对照表， 百度了下， ACCIS 可以与上面的各种进制来对照

比如 password 字符串，需要他的 10 进制对照表

```bash
# 可以使用管道符来给 od 处理
[root@study ~]# echo password | od -t dCc
0000000  112   97  115  115  119  111  114  100   10
           p    a    s    s    w    o    r    d   \n
0000011

```

### 修改文件时间或新建文件 touch

使用 `ls` 指令的时候，提到过每个文件 linux 底下都会记录许多的时间参数，其实是有三个主要的变动时间：

- **modification time**（**mtime**）

  当文档 **内容数据** 变更时。该时间会被更新。

- **status time**（**ctime**）

  当文件 **状态** 改变时。比如权限与数学被更改了

- **access time**（**atime**）

  当文件 **内容被取用** 时。比如我们使用 cat 去读取 /etc/man_db.conf ，该时间就会改变

```bash
[root@study ~]# date;ls -l /etc/man_db.conf ;ls -l --time=atime /etc/man_db.conf ;ls -l --time=ctime /etc/man_db.conf
2019年 10月 13日 星期日 21:33:52 CST
-rw-r--r--. 1 root root 5171 10月 31 2018 /etc/man_db.conf # 2018/10/31 建立的 mtime
-rw-r--r--. 1 root root 5171 10月 13 15:36 /etc/man_db.conf # 10月13号 读取过 atime
-rw-r--r--. 1 root root 5171 10月  4 18:22 /etc/man_db.conf # 10月4号 更新过状态 ctime

# 笔者就现在使用了 cat /etc/man_db.conf，也没有发现时间变更，不知道是啥原因

```

当你看到一个未来时间的文件，这个是有可能的，因为支持多时区，安装系统行为不当，就有可能导致这种情况发生

可以使用 `touch` 来修订时间

```bash
touch [-acdmt] 文件
```

- a：仅修订 access time
- c：仅修改文件的时间，若该文件不存在则不建立新文件
- d：后面可以接欲修订的日期而不用目前的日期，也可以使用 --date="日期或时间"
- m：仅修改 mtime
- t：后面可以接欲修订的时间而不用目前的时间，格式为 YYYYMMDDhhmm

实践练习

```bash
[mrcode@study ~]$ cd /tmp/
[mrcode@study tmp]$ touch testtouch
[mrcode@study tmp]$ ls -l testtouch
-rw-rw-r--. 1 mrcode mrcode 0 Oct 13 21:45 testtouch

# 注意到这个文件的大小是 0，在预设的状态下，如果 touch 没有接文件
# 则该文件的三个时间（atime、ctime、mtime 都会更新为目前的时间。
# 若该文件不存在，则会主动建立一个新的空文件


# 复制一个文件，假设复制全部的属性，并检查日期
[mrcode@study tmp]$ cp -a ~/.bashrc bashrc
[mrcode@study tmp]$ date; ll bashrc ; ll --time=atime bashrc ; ll --time=ctime bashrc
Sun Oct 13 21:48:24 CST 2019
-rw-r--r--. 1 mrcode mrcode 231 Aug  8 20:06 bashrc # mtime
-rw-r--r--. 1 mrcode mrcode 231 Oct 13 14:38 bashrc # atime
-rw-r--r--. 1 mrcode mrcode 231 Oct 13 21:47 bashrc # ctime
# 属性完全被复制，mtime 与源文件相同，该文件是刚刚建立的， ctime 就是当前时间

# 可以将日期调整为两天前
[mrcode@study tmp]$ touch -d "2 days ago" bashrc
[mrcode@study tmp]$ date; ll bashrc ; ll --time=atime bashrc ; ll --time=ctime bashrc
Sun Oct 13 21:51:31 CST 2019
-rw-r--r--. 1 mrcode mrcode 231 Oct 11 21:51 bashrc # mtime
-rw-r--r--. 1 mrcode mrcode 231 Oct 11 21:51 bashrc # atime
-rw-r--r--. 1 mrcode mrcode 231 Oct 13 21:51 bashrc # ctime
# 可以看到前两个实际变化了，ctime 又变成当前时间了

# 将日期调整为诶指定的时间 2014/06/15 00:00
[mrcode@study tmp]$ touch -t 201406150000 bashrc
[mrcode@study tmp]$ date; ll bashrc ; ll --time=atime bashrc ; ll --time=ctime bashrc
Sun Oct 13 21:54:31 CST 2019
# 由于时间太久远，默认的格式显示不全的，没有显示时分格式
-rw-r--r--. 1 mrcode mrcode 231 Jun 15  2014 bashrc
-rw-r--r--. 1 mrcode mrcode 231 Jun 15  2014 bashrc
-rw-r--r--. 1 mrcode mrcode 231 Oct 13 21:54 bashrc

```

那么 touc 中最常用的功能是：

- 建立一个空的文件
- 将某个文件日期秀固定为目前（mtime 与 atime）
- 比较重要的是 mtime，关心这个文件内容是什么时候被更新的

-------

## 文件与目录的默认权限与隐藏权限

前面讲解过文件有若干的属性，读写执行等基本权限（`rwx`）， 是否为目录（`d`）、文件（`-`）或则是链接（`l`）等属性，修改属性也可通过 `chgrp`、`chown`、`chmod`。

除了基本的 `rwx` 权限外，在传统的 ext2、3、4 文件系统下，还可以设置其他的系统隐藏属性， 可以使用 `chattr` 来设置，以 `lsattr` 来查看，最重要的属性就是可以设置不可修改的特性， 让连文件的拥有者都不能进行修改。

在安全机制方面特别的重要，但是在 CentOS7 中利用 `xfs` 作为预设文件系统， 该文件系统就不支持 `chattr` 参数了，仅有部分参数还有支持

### 文件预设权限 umask

`umask`：指定目前用户在建立文件或目录时候的默认权限

```bash
# 以数值形态显示
[mrcode@study tmp]$ umask
0002   # 与一般权限有关的是后面三个数字

# 还可以以符号来显示
[mrcode@study tmp]$ umask -S
u=rwx,g=rwx,o=rx

```

在数值形态下有 4 组，第一组是特殊权限用的，先不看，因此预设情况如下：

- 文件

  没有可执行（x）权限、只有 rw 两个项目，也就是最大为 666 分 `-rw-rw-rw-`

- 目录

  由于 x 与是否可以进入此目录有关，因此默认所有权限均开发，即 777 分 `drwxrwxrwx`

注意：umask 的分数指的是，该默认值需要 **减掉** 的权限！也就是需要从预设的权限中减掉

使用上面的示例来说明：

```bash
r、w、x 分别是 4、2、1 分。

002，也就是 others 的权限被拿掉了 2 也就是 w，那么权限如下：

建立文件时：预设 -rw-rw-rw-，减掉 2 变成 -rw-rw-r--
建立目录时：预设 drwxrwxrwx，减掉 2 变成 drwxrwxr-x

```

不信吗？可以实践看下

### umask 的利用与重要性：专题制作

你和你同学在同一个目录下 /home/class 合作一个专题，那么有没有可能你制作的文件， 你的同学无法编辑？

如果 `umask` 设置为 0022 ，那么相当于 `group` 默认创建只有 r 属性，除了拥有者， 其他人只能读，不能写。所以需要修改 `umask` 的值

```bash
# 修改语法是 umask 后接数值
# 由于笔者的 centos 较新，默认已经是 002 的了，这里就更改回 022 来测试
[mrcode@study tmp]$ umask
0002
[mrcode@study tmp]$ umask 022   # 更改为 022
[mrcode@study tmp]$ umask
0022
[mrcode@study tmp]$ touch test3
[mrcode@study tmp]$ mkdir test4
[mrcode@study tmp]$ ll -d test[34]  # 使用正则来匹配 test3和4
# 可以看到 文件 group 和 ohters 已经没有了 w
-rw-r--r--. 1 mrcode mrcode 0 Oct 13 22:23 test3
drwxr-xr-x. 2 mrcode mrcode 6 Oct 13 22:23 test4

```

> TIP
>
> umask 对于新建文件与目录的默认权限很重要，这个概念可以用在任何服务器上面， 尤其是未来假设文件服务器（file server），如 SAMBA Server 或则是 FTP server 时， 牵涉到你的使用者是否能够将文件进一步利用的问题

原来在预设的情况下，身份不同默认值也是不同的，**root** 的 `umask` 默认是 022，一般账户是 002。 关于预设设定可以参考 `/etc/bashrc` 这个文件的内容，不过这里不建议修改该文件， 后续讲解 bash shell 环境参数配置中再详解

### 文件隐藏属性

除了基本的 9 个权限外，还有隐藏属性，而隐藏属性对系统有很大的帮助，尤其是在安全上面。

#### chattr 配置文件隐藏属性

强调：在 ext2/3/4 中完全支持，而在 xfs 上部分支持

```bash
chattr [+-=][ASacdistu] 文件或目录名称
```

- `+`：增加一个特殊参数，其他参数不变
- `-`：移除一个特殊参数
- `=`：设定为后面接的参数
- **A**：若有存取此文件/目录时，它的访问时间 atime 将不会被修改
- **S**：对文件的修改变成同步写入磁盘中，一般默认是异步写入（前面章节讲到过 sync）
- **a**：该问价只能增加数据，不能删除也不能修改数据，只有 root 才能设置该属性
- **c**：自动将此文件压缩，在读取的时候也将会自动解压缩，但是在存储的时候，会先压缩后再存储（对大文件似乎有用）
- **d**：当 dump 程序被执行的时候，可使该标记的文件或目录不被 dump 备份
- **i**：让文件不能被删除、改名、设置连接、写入或新增数据，完完全全就是只读文件了。只有 root 能设置该属性
- **s**：当文件被删除时，将会被完全的移除这个硬盘空间，所以如果误删，就找不回来了
- **u**：与 **s** 相反，删除后，其实数据还在磁盘中，可以用来救援该文件

注意：

- 属性设置常见的是 **a** 与 **i** 的设置，而且很多设置值必须要 **root**
- **xfs** 文件系统仅支持 **AadiS** 选项

实践练习

```bash
[root@study tmp]# cd /tmp/
[root@study tmp]# touch attrtest
# 添加 i 属性
[root@study tmp]# chattr +i attrtest
# 尝试删除，发现不能删除，连 root 也无法删除
[root@study tmp]# rm attrtest
rm: remove regular empty file ‘attrtest’? y
rm: cannot remove ‘attrtest’: Operation not permitted
[root@study tmp]# rm -rf attrtest
rm: cannot remove ‘attrtest’: Operation not permitted

# 移除 -i 属性
[root@study tmp]# chattr -i attrtest
[root@study tmp]# rm attrtest
rm: remove regular empty file ‘attrtest’? y
# 这次再删除就成功了

```

个人觉得 `+i` 和 `+a` 属性最重要：

- `i`：无法被更动
- `a`：不能修改旧的数据，只能新增

那么 a 属性在后续的登录档（log file）这种登录日志类的场景就很适合了

#### lsattr 显示文件隐藏属性

```bash
lsattr [-adR] 文件或目录

```

- **a**：将隐藏文件的属性也秀出来
- **d**：如果接的是目录，仅列出目录本身的属性而非目录内的文件名
- **R**：连同子目录的数据也列出来

```bash
# 这里创建一个文件，然后观察他的特殊属性
[root@study tmp]# touch attrtest
[root@study tmp]# ll attrtest
-rw-r--r--. 1 root root 0 Oct 13 22:50 attrtest
[root@study tmp]# lsattr attrtest
# 发现是一片空白
---------------- attrtest
# 添加之后再观察
[root@study tmp]# chattr +aiS attrtest
[root@study tmp]# lsattr attrtest
--S-ia---------- attrtest

```

### 文件特殊权限 SUID、SGID、SBIT

除了前面的 9 个权限之外，还有特殊的权限，如下面两个目录

```bash
[mrcode@study ~]$ ls -ld /tmp/;ls -l /usr/bin/passwd
# 尾部多了一个 t
drwxrwxrwt. 38 root root 4096 Oct 16 21:37 /tmp/
# 拥有者里面多了一个 s
-rwsr-xr-x. 1 root root 27856 Aug  9 09:39 /usr/bin/passwd

```

`s` 与 `t` 这两个的权限与后续的 「系统的账户」及系统的程序（**process**）较为相关， 关于概念需要再后续两个章节讲完之后，才会了解，这里只需要知道 **SUID**、**SGID** 如何设定即可

#### Set UID

当 `s` 标志出现在文件拥有者 `x` 权限上时，就被称为 Set UID，简称 SUID 特殊权限， 对于文件的特殊功能如下：

- SUID 权限仅对二进制程序（binary program）有效
- 执行者对于该程序需要具有 x 的可执行权限
- 本权限仅在执行该程序的过程中有效（run-time)
- 执行者将具有该程序拥有者（owner）的权限

比如：linux 中，所有的账户的密码都记录在 /etc/shadow 文件中，既然该文件仅有 root 可以修改，那么我自己的 mrcode 一般账户使用者能否自行修改自己的密码呢？

```bash
[mrcode@study ~]$ passwd
Changing password for user mrcode.
Changing password for mrcode.
(current) UNIX password:

```

使用如上命令，发现可以修改，那么： **shadow** 一般账户不能读取，为什么还能修改密码呢？（也就是间接的修改了 **shadow** 中的数据），这就是 **SUID** 的功能了。

- **mrcode** 对于 **/usr/bin/passwd** 这个程序来说具有 **x** 权限的，表示 **mrcode** 能执行 **passwd**
- **passwd** 的拥有者是 **root** 账户
- **mrcode** 执行 **passwd** 的过程中，会暂时获得 **root** 的权限
- **/etc/shadow** 就可以被 **mrcode** 所执行的 **passwd** 所修改

那么使用 cat 去读取 /etc/shadow 可以吗？通过查看 cat 的权限，会发现 cat 没有包含 SUID 特殊权限，就是为什么不能读取的原因

```bash
[mrcode@study ~]$ ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 27856 Aug  9 09:39 /usr/bin/passwd
[mrcode@study ~]$ ll /usr/bin/cat
-rwxr-xr-x. 1 root root 54080 Aug 20 14:25 /usr/bin/cat
```

> TIP
>
> SUID 仅可用在 binary program 上，不能用在 shell script 上面， 因为 shell script 只是将很多的 binary 执行档叫进来执行而已。
>
> 所以 SUID 的权限部分需要看脚本中执行的指令是否具有 SUID ，而不是脚本自身。 对目录页是无效的

#### Set GID

**s** 在群组的 **x** 时称为 **Set GID**

```bash
[mrcode@study ~]$  ls -l /usr/bin/locate
-rwx--s--x. 1 root slocate 40520 Apr 11  2018 /usr/bin/locate

```

**SGID** 可以针对文件或目录来设置，针对文件来说有如下功能含义：

- **SGID** 对二进制程序有用
- 程序执行者对于该程序来说，需要具备 x 的权限
- 执行者在执行的过程中将会获得该程序群组的支持

例如：**/usr/bin/locate** 这个程序可以搜索 **/var/lib/mlocate/mlocate.db** 文件内容， 权限如下

```bash
[root@study ~]# ll /usr/bin/locate /var/lib/mlocate/mlocate.db
-rwx--s--x. 1 root slocate   40520 4月  11 2018 /usr/bin/locate
-rw-r-----. 1 root slocate 3468856 10月 13 15:36 /var/lib/mlocate/mlocate.db

```

如果使用 **mrcode** 账户去执行 **locate** 时，**mrcode** 将会取得 **slocate** 群组的支持; (这里有点懵逼，使用 **locate -A /var/lib/mlocate/mlocate.db** 没有报错，但是没有内容， 但是直接使用 **ll /var/lib/mlocate/** 却提示没有权限，只能后续的课程讲了后才知道是什么意思了)

除了 **binary program** 外，**SGID** 还能用在目录上，当一个目录设置了 **SGID** 的权限后，将具有如下的功能：

- 用户若对于此目录具有 `r` 与 `x` 的权限时，该用户能够进入此目录
- 用户在此目录下的有效群组（effective group）将会变成该目录的群组
- 用途：若用户在此目录下具有 `w` 的权限（可以新建文件），则使用者所建立的新文件，该新文件的群组与此目录的群组相同

**SGID** 对于项目开发来说非常重要，涉及到群组权限的问题。可以参考下后续的「情景模拟的案例」， 能加深一点了解

#### Sticky Bit

**Sticky Bit**简称为 **SBT** ，目前只针对目录有效，对于文件没有效果了

作用是：当用户对于此目录具有 `w`、`x` 权限，即具有写入的权限时，当用户在该目录下简历文件或目录时， 仅有自己与 `root` 才有权利删除该文件

例如：**mrcode** 用户在 **A** 目录是具有 `w` 的权限（群组或其他人类型权限），这表示 **mrcode** 对该目录 内任何人简历的目录或则文件均可进行删除、更名、搬移等动作，但是将 **A** 目录加上了 **SBIT** 的权限时，则 **mrcode** 只能够针对自己建立的文件或目录进行删除、更名、搬移等动作，而无法删除他人的文件

> **TIP**
>
> 这部分内容在后续章节「关于程序方面」的只是后，再回过头来看，才能明白讲的是什么

#### SUID、SGID、SBIT 权限设定

可以使用数值权限更改方法来设置，他们代表的数值是：

- **SUID**：**4**
- **SGID**：**2**
- **SBIT**：**1**

下面演示具体这个数值加载哪里

```bash
[root@study tmp]# cd /tmp/
[root@study tmp]# touch test
# -rwsr-xr-x 拥有者权限 rwx 都有分数为 7，后面的都是5，原本权限为 755
# 那么久在 755 前增加特殊权限数值即可
# 这里添加 SUID 的权限
[root@study tmp]# chmod 4755 test; ls -l test
-rwsr-xr-x. 1 root root 0 10月 16 22:16 test

```

下面再来演示几个

```bash
# 添加 SUID + SGID 权限
[root@study tmp]# chmod 6755 test; ls -l test
-rwsr-sr-x. 1 root root 0 10月 16 22:16 test

# 添加 SBIT
[root@study tmp]# chmod 1755 test; ls -l test
-rwxr-xr-t. 1 root root 0 10月 16 22:16 test

# 添加具有空的 SUID SGID 权限
# 这里出现了大写的 SST
[root@study tmp]# chmod 7666 test; ls -l test
-rwSrwSrwT. 1 root root 0 10月 16 22:16 test


```

上面最后一个例子出现了大写的三个特殊权限 **S**、**S**、**T**，这里是这样的，因为 **666** 的权限中 不包含 `x` 权限，所以当特殊权限出现在 `x` 中的时候（又不拥有 `x`）则会出现大写的，表示空。 **SUID** 表示该文件在执行的时候，具有文件拥有者的权限，但是文件拥有者都无法执行了， 哪里来的权限给其他人使用呢？

除了数值，还可以使用符号来处理：

- **SUID**：**u+s**
- **SGID**：**g+s**
- **SBIT**：**o+t**

```bash
# 设置为 -rws--x--x
[root@study tmp]# chmod u=rwxs,go=x test; ls -l test
-rws--x--x. 1 root root 0 10月 16 22:16 test

# 在上面的权限基础上，增加 SGID 与 SBIT
[root@study tmp]# chmod g+s,o+t test; ls -l test
-rws--s--t. 1 root root 0 10月 16 22:16 test


```

### 观察文件类型 file

想知道某个文件的基本数据，例如属于 **ASCII** 或则是 **data** 文件、**binary** 、是否用到动态函数库（**share library**）等信息，可以使用 `file` 指令来检阅

```bash
# ASCII 文本文件
[root@study tmp]# file ~/.bashrc
/root/.bashrc: ASCII text

# 执行文件的数据就很多了，包括这个文件的 suid 权限、兼容于 intel x86-64 等级的硬件平台
# 使用的是 linux 核心 2.6.32 的动态函数库链接
[root@study tmp]# file /usr/bin/passwd
/usr/bin/passwd: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=471dad50eb96512f90dd9394adbd7513ae60f072, stripped

# data 文件
[root@study tmp]# file /var/lib/mlocate/mlocate.db
/var/lib/mlocate/mlocate.db: data


```

通过这个指令可以简单的判断文件的格式，包括判断使用 tar 文档是使用的哪一种压缩功能

## 指令与文件的搜寻

很有用的功能之一，需要搜索某个文件在哪个位置，因为很多软件的配置文件名是不变的， 但是各 distribution 放置的目录则不同。要把位置找出来才能修改配置

### 脚本文件名的搜索

我们已经知道在终端模式下，连续两次「tab」有指令补全的功能，能展示出想匹配的指令， 那么这些指令在哪里呢?

#### which 搜索执行文档

```bash
which [-a] command

-a：将所有 PATH 目录中可以找到的指令均累出，而不止第一个被找到的指令名称

```

```bash
# 搜索 ifconfig 这个指令完整文件名
[root@study tmp]# which ifconfig
/sbin/ifconfig

# 查看 which 在哪个位置
[root@study tmp]# which which
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
        /bin/alias
        /usr/bin/which

# 这里发现了两个 which，其中一个是 alias
# alias 是指令的别名，输入 which 后，就等于属于了等于后面的那一串指令
# 更多的数据在后续的 bash 章节中讲解

# 找出 history 指令的完整文档名
[root@study tmp]# which history
/usr/bin/which: no history in (/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)
# 上面在列出的目录中没有找到
# 下面报错没有 --help 这个选项
[root@study tmp]# history --help
-bash: history: --: 无效选项
history: 用法:history [-c] [-d 偏移量] [n] 或 history -anrw [文件名] 或 history -ps 参数 [参数...]


```

上面 history 为什么找不到？

- which 根据 PATH 环境变量中的目录来搜索的
- 只能找出执行文件
- history 是 bash 内置的指令

history 不在 PATH 内的目录中，是 bash **内置的指令**， 但是可以通过 type 指令，后续章节 bash 详解

### 文件名的搜索

linux 中有许多搜索指令，通常 find 不很常用，因为速度慢，操硬盘（啥意思？）， 一般先用 whereis 或则是 locate 来检查，如果找不到，则用 find 来搜索。

- whereis 只找系统中某些特定目录下的文件，速度快
- locate 则利用数据库来搜索文件名的，速度块
- find 搜索全磁盘内的文件系统状态，耗时

#### whereis 由一些特定的目录中搜索文件名

```bash
whereis [-bmsu] 文件或目录名

```

- **l**：列出 **whereis** 会去查询的几个主要目录
- **b**：只找 **binary** 格式的文件
- **m**：只找在说明文件 **manual** 路径下的文件
- **s**：只找 **source** 来源文件
- **u**：搜索不在上述三个选项中的其他特殊文件

```bash
# 找到 ifconfig 文件名
# 下面发现找到了两个包含 ifconfig 的
[root@study tmp]# whereis ifconfig
ifconfig: /usr/sbin/ifconfig /usr/share/man/man8/ifconfig.8.gz

# 找到与 passwd 有关的说明文件文件名（man page）
[root@study tmp]# whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz

# 这里添加 -m 参数就只找说明文件了
[root@study tmp]# whereis -m passwd
passwd: /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz


```

**whereis** 主要是针对 **/bin/sbin** 下的执行文件、**/usr/share/man** 下的 **man page** 文件、和几个特定的目录，所以速度块很多，由于不是全盘查找，可能找不到你想要的文件，可以使用 `whereis -l` 来显示具体会找那些目录

#### locate / updatedb

```bash
locate [-ir] keyword

```

- **i**：忽略大小写的差异
- **c**：不输出文件名，仅计算找到的文件数量
- **l**：仅输出几行，例如输出五行则是 **-l 5**
- **S**：输出 `locate` 所使用的数据库文件相关信息，包括该数据库记录的文件/目录数量等
- **r**：后面可接正规表示法的显示方式

```bash
# 找出系统中所有与 passwd 先关的文件名，且只列出 5 个
[root@study tmp]# locate -l 5 passwd
/etc/passwd
/etc/passwd-
/etc/pam.d/passwd
/etc/security/opasswd
/usr/bin/gpasswd
# 可以看到找到了 9 前多个相关的
[root@study tmp]# locate -c 5 passwd
9863

# 列出 locate 查询所使用的数据库文件与各数据数量
[root@study tmp]# locate -S
数据库 /var/lib/mlocate/mlocate.db:
        11,623 文件夹
        153,170 文件
        7,466,565 文件名中的字节数
        3,468,856 字节用于存储数据库

```

**locate** 可以其实就是模糊搜索，只要包含关键词的文件名都会被匹配，他是他有一个限制， 查找的数据是已建立的数据库 **/var/lib/mlocate** 里面的数据来搜索的。

该数据库建立默认是每天执行一次（每个 distribution 不同，CentOS 7 是每天更新一次数据库）， 所以能搜索到的结果是有延迟的

可以手动触发数据库的更新，直接使用 **updatedb** 指令就可以

- **updatedb**

  根据 **/etc/updatedb.config** 的设置去搜索系统盘内的文件名，并更新到 **/var/lib/mlocate** 数据库文件内

- **locate**：从 **/var/lib/mlcate** 内的数据库中搜索关键词

#### find

```bash
find [path] [option] [action]

```

**与时间有关的参数**

与时间有关的参数有 **-atime**、**-ctime**、**-mtime**，以 **-mtime** 说明：

- **mtime n**：在 n 天前的「一天之内」被修改过内容的文件
- **mtime +n**：列出在 n 天之前（不含 n 本身）被修改过内容的文件
- **mtime -n**：列出在 n 天之内（含 n 天本身）被修改过内容的文件
- **newer file**：**file** 为一个存在的文件，列出比 **file** 还要新的文件

```bash
# 将过去系统上 24 小时内有更动过内容（mtime）的文件列出
find / -mtime 0
# 0 表示当前时间，也就是当前时间开始往前 24 小时，也就是 24 小时内被修改过的文件

# 3 天前，24 小时内，如下
find / -mtime 3

# 寻找 /etc 下的文件，如果文件日期比 /etc/passwd 新旧列出
find /etc -newer /etc/passwd

# 列出 4 天内被更动多的文件
find / -mtime -4

```

**mtime** 选项的 n 正负数差别表示不同的含义，图示如下

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-find.png" style="zoom:67%;" />

- `+4`：表示大于等于 5 天前的
- `-4`：表示小于等于 4 天内的
- 4：表示 4~5 哪一天的文件

#### 与使用者或组名有关的参数

- **uid** **n**：**n** 为数字，是用户的账户 ID（UID)，UID 记录在 /etc/passwd 里面与账户名称对于的数字。后续介绍

- **gid** **n**：**n** 为数字，是组名的 ID，记录在 /etc/group 文件中

- **user name**：name 为使用者账户名称，如 mrcode

- **group name**：name 为组名

- **nouser**：寻找文件的拥有者不存在 /etc/passwd 的人

- **nogroup**：寻找文件的拥有群组不存在 /etc/group 的文件

  当你自行安装软件时，很可能该软件的属性当中并没有文件拥有者，这个时候就可以使用 nouser 与 nogroup

实践与练习

```bash
# 查找 /home 下属于 mrcode 的文件
find /home/ -user mrcode
# 比较有用，如找到一个用户在系统中的所有文件时

# 查找系统中不属于任何人的文件
[root@study ~]# find / -nouser
find: ‘/proc/19655/task/19655/fd/5’: 没有那个文件或目录
find: ‘/proc/19655/task/19655/fdinfo/5’: 没有那个文件或目录
find: ‘/proc/19655/fd/6’: 没有那个文件或目录
find: ‘/proc/19655/fdinfo/6’: 没有那个文件或目录
find: ‘/proc/19657’: 没有那个文件或目录
find: ‘/proc/19668’: 没有那个文件或目录
find: ‘/proc/19669’: 没有那个文件或目录
find: ‘/proc/19670’: 没有那个文件或目录

# 这里没有找出来，但是报错了一些目录不存在，不知道是啥原因
# 透过这个指令，可以轻易的找出那些不太正常的文件，如果找到了，那么有可能是正常的，比如你以源码编译软件时

```

`nouser` 和 `nogroup` 的选项，除了你自行由网络上面下载文件时会发生之外，如果你将系统里面某个账户删除了， 但是该账户以及在系统内建立了很多文件，那么就可能发生 `nouser` 和 `nogroup` 的文件

#### 与文件权限及名称有关的参数

- **name filename**：查找文件名为 **filename** 的文件

- **size** **[-+]SIZE**：查找比 **SIZE** 还要大（**+**）或则小（**-**）的文件

  **SIZE** 支持的单位有：

  - **c**：**byte**
  - **k**：**1024 byte**

  所以要查找 比 **50 KB** 还要大的文件，指令为 `find /home/ -size +50ks`

- **type** **TYPE**：查找文件类型为 **TYPE** 的。主要有

  - **f**：一般正规文件
  - **b,c**：装置文件
  - **d**：目录
  - **l**：连接
  - **s**：**socket**
  - **p**：**FIFO**

- **perm mode**：查找文件权限「刚好等于」**mode** 的文件，**mode** 为类似 **chmod** 的属性。

  例如：**-rwsr-xr-x** 的属性为 **4755**

- **perm -mode**：查找文件权限「必须要全部包括 mode 的权限」的文件

  例如：查找 **-rwxr--r--** ，即 **0744** 的文件，使用 **-perm -0744**

- **perm /mode**：查找文件权限「包含任意 mode 的权限」的文件

  例如：**-rwxr-xr-x**，即 **-perm /755** 时，但一个属性属性为 **-rw------** 也会被列出来， 因为他有 **-rw** 的属性存在

实践与练习

```bash
# 找出文件名为 passwd 的文件
find / -name passwd

# 找出包含了 passwd 关键词的文件
find / -name "*passwd*"

# 找出 /run 目录下，文件类型为 socket 的文件
find /run -type s
# -type 属性也很有用，可以找出那些怪异的文件
# 列入 socket 与 FIFO 文件，可以使用 find /run -type p 或 -type s 来找

# 查找文件中含有 SGID 或 SUID 或 SBIT 的属性
find / -perm /7000
# 7000 就是 ---s--s--t

```

上面范例中比较有趣的是 **-perm** 可以找出特殊权限的文件，**SUID** 与 **SGID** 都可以设置在二进制文件上

```bash
# 找出 /usr/bin /usr/sbin 具有 SUID 或 SGID 的文件
find /usr/bin /usr/sbin -perm /6000
# SUID=4、SGID=2、SBIT=1

```

#### 额外可以进行的动作

- **exec command**：`command` 为其他指令，**-exec** 后面可再接额外的指令来处理搜索到的结果
- **print**：将结果打印到屏幕上，这个动作是预设的，不然不会看到结果

实践与练习

```bash
# 将上个范例找到的文件使用 ls -l 列出来
find /usr/bin /usr/sbin -perm /6000 -exec ls -l {} \;
# -exec 后面的 ls -l 就是额外的指令，指令不支持命令别名
# 所以只能使用 ls -l 而不能使用 ll

# 找出系统中，大于 1MB 的文件
find / -size +1M

```

`find` 的特殊功能就是可以进行额外的动作（**action**），图解一个范例

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-find-exec.png)

- `{}`：表示由 find 找到的内容
- `-exec 开头到 \; 结尾`：中间的表示指令额外动作
- `;`：在 bash 环境下又特殊意义的，用斜杠 `\` 来跳脱

说使用 find 在寻找数据的时候相当操硬盘是啥意思？耗费硬盘？所以能用 whereis 与 locate 操作的尽量用

#### 删除 n 天前的文件

笔者这里既然学习了 `find` 知识点，现在拿出之前在工作中经常用到删除 **n** 天前的指令来分析下， 看能不能看懂

```bash
find /usr/local/backups -mtime +10 -name "*.*" -exec rm -rf {} \;

-mtime ：表示文件修改时间
+10 ：表示 10 天前的（不含 10 哪一天）
name：查找文件名，后面使用了通配符，查找所有的文件
-exec rm -rf {} \； 使用执行额外动作，将查找到的文件执行了 rm -rf 删除操作

```

#### find 常用命令收集

以下收集一些常用的使用方式

```bash
# 搜索文件内容，并显示命中的文件 与 内容所在行
find . -type f -name "*.conf" -print0 | xargs -0 grep -n "8081"

```

## 权限与指令间的关系

权限对于使用者账户来说是非常重要的，因为可以限制使用者不能读取、建立、删除、修改文件或目录。

那么什么指令在什么样的权限下才能够运行？

### 让用户能进入某目录称为「可工作目录」的基本权限

- 可使用的指令：例如 cd 等变换工作目录的指令
- 目录所需权限：用户对这个目录至少具有 x 的权限
- 额外需求：如果想在该目录内利用 ls 查阅文件名，则还需要有目录的 r 权限

### 用户在某个目录内读取一个文件的基本权限

- 可使用的指令：例如 cat、more、less 等
- 目录所需权限：至少具有 x 权限
- 文件所需权限：至少具有 r 权限

### 让使用者可以修改一个文件的基本权限（修改文件内容）

- 可使用的指令：例如 nano 或未来要介绍的 vi 编辑器等
- 目录所需权限：至少具有 x 权限
- 文件所需权限：至少具有 r、w 权限

### 让一个使用者可以建立一个文件的基本权限

- 目录所需权限：至少具有 w、x 权限，重点是 x 权限

### 让用户进入某目录并执行该目录下的某个指令之基本权限

- 目录所需要的权限：至少具有 x 权限
- 文件所需要的权限：至少具有 x 权限

----------

# 文件与文件系统的压缩、打包与备份

## 压缩文件的用途与技术

文件压缩技术一般用于的场景是：当文件容量很大的时候，想要降低一些容量，在网络中传输时间少，当然下载的人就能更快的下载完，还有数据归档使用 cd 或则 dvd 来存储，但是某些单一文件比这些传统的一次性存储媒体还要大、等等的场景。

简单说就是：这些大型文件通过压缩技术之后，可以将他的磁盘使用量降低，达到减低文件容量的效果

## 文件压缩的原理是什么？

计算机最小的计量单位是 bits，不过目前我们使用的计算机系统中都是使用 bytes 单位来计量的，1 bytes=8bits，计算机存储文件是二进制的，当这个 8 bits 中没有被填满时，就会出现大量的 bit 被 0 填充，实际上他们是没有什么意义的，一些工程师利用一些复杂的计算方式，将这些没有使用到的空间去掉，来达到让文件占用空间变小的目的，这就是 **压缩技术**

还有一种压缩技术是将重复的数据进行统计记录。比如：你的数据为「1111....」有 100 个 1，压缩技术会记录「100 个 1」，而不是真的写了 100 个 1 出来。这样也能达到减少文件体积的目的

简单说：文件里面有相当多的「空间」存在，并不是完全填满的，而压缩技术就是将这「空间」填满，让整个文件占用的容量下降。但是被压缩过的文件无法被直接使用，需要还原回未压缩前的模样，这就是 **解压缩** 技术。

压缩后与压缩的文件所占用的磁盘空间大小，就可以被称为是「压缩比」，更多的技术可以查阅 [GZIP 文件格式规范](https://www.ietf.org/rfc/rfc1952.txt)

解压缩有什么好处呢？Linux 3.10.81（CentOS 7 用的延伸版本）完整核心大小约 570MB 左右，由于核心主要多是 ASCII code 的纯文本形态文件，这种文件的「多余空间」是比较多的。那么压缩之后的核心仅有 76MB 左右，相差几倍。网络传输时间减少，你的磁盘占用也减少。

## Linux 系统常用的压缩指令

在 linux 环境中，压缩文件的扩展名大多是：`.tar`、`.tar.gz`、`.tgz`、`.Z`、`.bz2`、`.xz` ，为什么会有这么多？

虽然在 linux 下扩展名没有啥作用，但是支持的压缩指令非常多，彼此之间无法互通压缩或解压缩，扩展名用于分别是使用哪种软件来解压缩。常用的扩展名如下：

- `.Z`：**compress** 程序压缩的文件
- .`zip`：**zip** 程序压缩的文件
- `.gz`：**gzip** 程序压缩的文件
- `.bz2`：**bzip2** 程序压缩的文件
- `.xz`：**xz** 程序压缩的文件
- `.tar`：**tar** 程序打包的数据，并没有压缩过
- `.tar.gz`：**tar** 程序打包的数据，并经过 gzip 的压缩
- `.tar.bz2`：同上，经过了 **bzip2** 压缩
- `.tar.xz`：同上，经过了 **xz** 的压缩

linux 上常见的压缩指令是 `gzip`、`bzip2` 以及最新的 `xz`，还有支持 windows 的 `zip`，至于其他的压缩指令基本上都淘汰了。这些指令通常仅能针对一个文件来压缩与解压缩，如此一来每次压缩与解压缩都要一大堆文件，所以 `tar` （打包）软件就出现了

`tar` 可以将很多文件「打包」成为一个文件，将很多文件集结为一个文件，但是没有提供压缩的功能，后来 GNU 计划中，将整个 `tar` 与压缩的功能结合在一起，提供了更强大的压缩与打包功能。

### gzip,zcat/zmore/zless/zgrep

`gzip` 可以说是应用最广的压缩指令，目前可用解开 `compress`、`zip` 与 `gzip` 等软件所压缩的文件，语法如下

```bash
gzip [-cdtv#] 文档名
zcat 文档名.gz

```

选项与参数：

- **c**：将压缩的数据输出到屏幕上，可通过数据流重导向来处理
- **d**：解压缩的参数
- **t**：可以用来检验一个压缩文件的一致性，看看文件有无错误
- **v**：可以显示出原文件、压缩文件的压缩比等信息
- **#**：为数字的意思，代表压缩等级
  - **-1**：最快，但是压缩比最差
  - **-9**：最慢，但是压缩比最好
  - **-6**：默认值

实践练习

```bash
# 找出 /etc 下（不含子目录）容量最大的文件，并将他们复制到 /tmp,然后以 gzip 压缩
# S 排序时按文件 size，r 翻转，倒序；对于参数，笔者这里使用 man ls 查看的参数说明，记不住的时候就可以这样做
# ls -ldSr /etc/* 最大的排在最后，莫非使用了 S 就是降序排列的吗？
[mrcode@study ~]$ ls -ldS /etc/*
-rw-r--r--.  1 root root   670293 Jun  7  2013 /etc/services
-rw-r--r--.  1 root root   104251 Oct  4 18:28 /etc/ld.so.cache

[mrcode@study ~]$ cd /tmp/
[mrcode@study tmp]$ cp /etc/services .
[mrcode@study tmp]$ gzip -v services 
services:        79.7% -- replaced with services.gz
[mrcode@study tmp]$ ll /etc/services /tmp/services*
-rw-r--r--. 1 root   root   670293 Jun  7  2013 /etc/services
-rw-r--r--. 1 mrcode mrcode 136088 Oct 28 22:39 /tmp/services.gz
# 可以看到压缩比为 79.7，压缩之后变成了 130 多 k

```

这里需要注意，使用 `gzip` 进行压缩时，默认状态下原本的文件会被压缩成 `.gz` 的文件，并且原始文件不存在了（文案已经提示了）；另外 `gzip` 压缩的文件在 **windows** 中可以被 **WinRAR**、**7zip** 软件解压缩

```bash
# 由于 services 是文件内容，将压缩后的文件内容读出来
zcat services.gz  
# 该文件过大，直接读完，我们看不到最前面的内容了
# 可以使用 zmore、zless 去读取

# 将 services.gz 加压缩
# 这里使用 -d 来解压缩，还有一个 gunzip 指令也可以解压缩，但是有点难记住它
[mrcode@study tmp]$ gzip -d services.gz 
[mrcode@study tmp]$ ll -l services*
# 同样，默认会将 .gz 的删除，剩下原来的文件名
-rw-r--r--. 1 mrcode mrcode 670293 Oct 28 22:39 services

# 将上面解开的文件，使用最佳压缩比压缩，并保留原文件
# 这个例子就明白 -c 使用数据流重导来处理是啥意思了，压缩输出到指定文件中
# 这里的 > 后续再 bash 章节会详细讲解
[mrcode@study tmp]$ gzip -9 -c services > services.gz
[mrcode@study tmp]$ ll -l services*
-rw-r--r--. 1 mrcode mrcode 670293 Oct 28 22:39 services
-rw-rw-r--. 1 mrcode mrcode 135489 Oct 28 22:50 services.gz

# 还可以在 services.gz 找那个找出 http 关键词在哪里（关键词搜索）
[mrcode@study tmp]$ zgrep -n 'http' services.gz 
# 下面会输出好多包含 http 的信息
14:#       http://www.iana.org/assignments/port-numbers
89:http            80/tcp          www www-http    # WorldWideWeb HTTP

```

在压缩文档中搜索字符的话可以使用 `zgrep`、`egrep` 等指令

### bzip2、bzcat/bzmore/bzgrep

`bzip2` 可以说是取代了 `gzip` 并提供更佳的压缩比。使用方式几乎与 `gzip` 相同

```bash
bzip2 [-cdkzv#] 文档名
bzcat 文档名.bz2

```

选项与参数：

- **c**：将压缩的过程产生的数据输出到屏幕上
- **d**：解压缩的参数
- **k**：保留源文件
- **z**：压缩的参数（默认值，可以不加）
- **v**：可以显示出源文件/压缩文件的压缩比信息
- `#`：与 gzip 一样，-9 最佳、-1 最快

实践练习

```bash
[mrcode@study tmp]$ bzip2 -v services
  services:  5.409:1,  1.479 bits/byte, 81.51% saved, 670293 in, 123932 out.
[mrcode@study tmp]$ ll -l services*
-rw-r--r--. 1 mrcode mrcode 123932 Oct 28 22:39 services.bz2
-rw-rw-r--. 1 mrcode mrcode 135489 Oct 28 22:50 services.gz
# bz2 的压缩率比 gz 的压缩率还要好，看文件大小

# 读取 bz2 文件内容
bzcat services.bz2
# 解压,默认都是会删除掉原文件，包括压缩也是
[mrcode@study tmp]$ bzip2 -d services.bz2 
[mrcode@study tmp]$ ls -l services*
-rw-r--r--. 1 mrcode mrcode 670293 Oct 28 22:39 services
-rw-rw-r--. 1 mrcode mrcode 135489 Oct 28 22:50 services.gz

# 这里使用 -k 参数来保留源文件，并使用最优压缩比
# 还可以使用 -c 来输出 bzip2 -9 -c services > services.gz
[mrcode@study tmp]$ bzip2 -9 -k services
[mrcode@study tmp]$ ls -l services*
-rw-r--r--. 1 mrcode mrcode 670293 Oct 28 22:39 services
-rw-r--r--. 1 mrcode mrcode 123932 Oct 28 22:39 services.bz2


```

使用方式 bzip2 与 gzip 几乎一模一样，不过压缩率好的一般都会更耗时

### xz、xzcat/xzmore/xzless/xzgrep

`xz` 比 `bzip2` 压缩比更高，用法也与 `bzip2`、`gzip` 就一模一样

```bash
xz [-dtlkc#] 文档名
xcat 文档名.xz
 
```

选项与参数：

- **d**：解压缩
- **t**：测试压缩文件的完整性，看是否有错误
- **l**：列出压缩文件的相关信息
- **k**：保留原本的文件
- **c**：将数据由屏幕上输出
- `#`：同样，压缩比数值

```bash
# 压缩
[mrcode@study tmp]$ xz -v services
services (1/1)
  100 %        97.3 KiB / 654.6 KiB = 0.149                                    
[mrcode@study tmp]$ ls -l services*
-rw-r--r--. 1 mrcode mrcode 123932 Oct 28 22:39 services.bz2
-rw-rw-r--. 1 mrcode mrcode 135489 Oct 28 22:50 services.gz
-rw-r--r--. 1 mrcode mrcode  99608 Oct 28 22:39 services.xz
# 看看上面的压缩，果真 xz 压缩比是最好的
# 列出压缩文件信息
[mrcode@study tmp]$ xz -l services.xz 
Strms  Blocks   Compressed Uncompressed  Ratio  Check   Filename
    1       1     97.3 KiB    654.6 KiB  0.149  CRC64   services.xz

# 读取压缩文件内容
 xzless services.xz 、xzcat services.xz 都可以
# 解压缩
[mrcode@study tmp]$ xz -d services.xz 
# 使用 -k 压缩并保留源文件
[mrcode@study tmp]$ xz -k services


# 可以使用 time 指令统计他们的时间
# time [gzip|bzip2|xz] -c services > services.[gz|bz2|xz]
# 上面的语法，下面是实际的指令
[mrcode@study tmp]$ time gzip -c services > services.gz;\
> time bzip2 -c services > services.bz2;\
> time xz -c services > services.xz ;
real    0m0.023s	# 看这个汇总时间
user    0m0.021s
sys     0m0.002s

real    0m0.043s
user    0m0.036s
sys     0m0.007s

real    0m0.232s		# 看这个汇总时间
user    0m0.227s
sys     0m0.005s

一个 0.023s 一个 0.232s，相差 10 倍

```

## 打包指令：tar

前面讲解的 `gzip`、`bzip2`、`xz` 也能够针对目录进行压缩，但是是将目录内所有文件 **分别** 压缩的。而在 windows 下可以使用 winRAR 之类的压缩文件，将好多数据包成一个文件的样式。

这种将多个文件或目录包成一个大文件的指令功能，就可以称呼为 **打包指令**，`tar` 就是这样一个功能的打包指令，同时还可以通过压缩指令将该文件进行压缩。windows 中的 WinRAR 也支持 `.tar.gz` 的解压缩

`tar` 的选项与参数非常多，这里只接受几个常用的选项

```bash
打包与压缩：`tar [-z|-j|-J][cv][-f 待建立的文件名] filename`
观察文件：  `tar [-z|-j|-J][tv][-f file.tar]`
解压缩：	  `tar [-z|-j|-J][xf][-f file.tar] [-C 目录]`

特别注意：`[-z|-j|-J]` 不可同时出现在一串指令中
特殊注意：c、t、x 也不可同时出现在一串指令中

```

选项与参数

- **c**：建立打包文件，可搭配 `-v`来观察过程中被打包的文件名
- t：查看打包文件的内容含有哪些文件，重点在查看文件名
- **x**：接打包或解压缩的功能，可搭配 -C 在特定目录解开，特别注意 **c、t、x 不能同时出现在一起**
- **z**：通过 gzip 的支持进行压缩、解压缩；此时文件名最好为 `*.tar.gz`
- **j**：通过 bzip2 的支持进行压缩、解压缩；此时文件名最好为 `*.tar.bz2`
- **J**：通过 xz 的支持进行压缩、解压缩；此时文件名最好为 `*.tar.xz`
- **v**：在压缩、解压缩的过程中，将正在处理的文件名显示出来
- **f**：后面要立刻接要被处理的文件名，建议 -f 单独写一个选项（不容易忘记）
- **C**：在指定目录解压缩
- **p**：保留备份数据的原本权限与属性，常用语备份（-c）重要的配置文件
- **P**：保留绝对路径，保留 **root** 跟路径
- `--exclude=FILE`：在压缩过程中，排除指定的文件，不打包

最常用的是以下命令：

- 压 缩：`tar -jcv -f filename.tar.bz2 要被压缩的文件或目录`
- 查 询：`tar -jtv -f filename.tar.bz2`
- 解压缩：`tar -jxv -f filename.tar.bz2 -C 指定目录解开`

小提示：上面 `-jcvf` 可以写一起，但是阅读起来就没有上面这样分开好理解

### 使用 tar 加入 -z、-j 或 -J 的参数备份 /etc/ 目录

```bash
# 备份 /etc/ 需要 root 权限，否则会出现一堆错误
[mrcode@study ~]$ su -
Password:
Last login: Sun Oct 27 20:38:34 CST 2019 on pts/0

[root@study ~]# time tar -zpcv -f /root/etc.tar.gz /etc
tar: 从成员名中删除开头的“/”		# 注意这里的警告
/etc/
/etc/fstab
/etc/crypttab
...
real    0m2.329s		# 耗时 2.329 秒
user    0m1.322s
sys     0m0.308s

# -p 重点是保留文件的权限与属性
# 下面去掉了 -v，所以不会显示处理的文件名
[root@study ~]# time tar -jpc -f /root/etc.tar.bz2 /etc
tar: 从成员名中删除开头的“/”

real    0m3.012s
user    0m2.710s
sys     0m0.078s

[root@study ~]# time tar -Jpc -f /root/etc.tar.xz /etc
tar: 从成员名中删除开头的“/”

real    0m14.836s
user    0m13.511s
sys     0m0.224s

[root@study ~]# ll -h /root/etc*
-rw-r--r--. 1 root root  11M 10月 29 00:05 /root/etc.tar.bz2
-rw-r--r--. 1 root root  12M 10月 29 00:01 /root/etc.tar.gz
-rw-r--r--. 1 root root 8.2M 10月 29 00:06 /root/etc.tar.xz

# etc 占用 42M
[root@study ~]# du -sh /etc/
42M     /etc/


```

前面讲解 `cp` 指令复制的时候也涉及到复制后的文件权限与属性问题，这里的 `-p` 选项也是这样

### 查阅 tar 文件的数据内容（可查看文件名）与备份文件名是否有根目录的意义

```bash
# -v 把权限属性也列出来了
# 这里查看文件名前面无根路径的
[root@study ~]# tar -jtv -f /root/etc.tar.bz2
drwxr-xr-x root/root         0 2019-10-04 18:38 etc/
-rw-r--r-- root/root       808 2019-10-27 22:43 etc/fstab
-rw------- root/root         0 2019-10-04 18:20 etc/crypttab
lrwxrwxrwx root/root         0 2019-10-04 18:20 etc/mtab -> /proc/self/mounts
-rw-r--r-- root/root        51 2019-10-04 18:20 etc/resolv.conf

```

为什么需要拿到根目录呢？主要是为了安全，使用 **tar** 备份的数据可能会需要解压缩回来使用，在 **tar** 所记录的文件名（上面 **-jtv** 显示的文件名）就是解压缩后的实际文件名。如果拿到了根目录，则会在当前目录解压。比如现在在 **/tmp** ，解压后就变成 **/tmp/etc/xxx**；如果不拿掉根目录，源文件就被覆盖了

```bash
[root@study ~]# tar -jPc -f /root/etc.and.root.tar.bz2 /etc
[root@study ~]# tar -jtv -f /root/etc.and.root.tar.bz2
[root@study ~]# tar -jtv -f /root/etc.and.root.tar.bz2
tar: 从成员名中删除开头的“/”
drwxr-xr-x root/root         0 2019-10-04 18:38 /etc/
-rw-r--r-- root/root       808 2019-10-27 22:43 /etc/fstab
-rw------- root/root         0 2019-10-04 18:20 /etc/crypttab
lrwxrwxrwx root/root         0 2019-10-04 18:20 /etc/mtab -> /proc/self/mounts
# 对比下，确实是带上了根路径

```

### 将备份的数据解压缩，并考虑指定目录压缩（-C 选项的应用）

```bash
[root@study ~]# pwd
/root
[root@study ~]# tar -jx -f etc.tar.bz2
[root@study ~]# ll -d etc*
drwxr-xr-x. 143 root root     8192 10月  4 18:38 etc
-rw-r--r--.   1 root root 10520237 10月 29 00:15 etc.and.root.tar.bz2
-rw-r--r--.   1 root root 10518433 10月 29 00:05 etc.tar.bz2
-rw-r--r--.   1 root root 12212046 10月 29 00:01 etc.tar.gz
-rw-r--r--.   1 root root  8580036 10月 29 00:06 etc.tar.xz

# 解压到指定目录
tar -zx -f etc.tar.gz -C /tmp
# 记得删除解压后的文件
rm -rf /tmp/etc/ /root/etc

```

### 仅解开单一文件

前面讲解的都是解开该压缩包中的所有文件。

```bash
# 利用 -t 查看文件名，接管道查找 shadow
[root@study ~]# tar -jtv -f /root/etc.tar.bz2 | grep 'shadow'
---------- root/root      1271 2019-10-04 18:31 etc/shadow-
---------- root/root       797 2019-10-04 18:31 etc/gshadow
---------- root/root      1266 2019-10-04 18:31 etc/shadow 		# 假设要提取出这个文件
---------- root/root       791 2019-10-04 18:31 etc/gshadow-

# 后面接需要提取出来的文件路径
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 etc/shadow
etc/shadow
[root@study ~]# ll etc
总用量 4
----------. 1 root root 1266 10月  4 18:31 shadow


```

### 打包某目录，但不包含该目录下的某些文件

```bash
[root@study ~]# tar -jc -f /root/system.tar.bz2 --exclude=/root/etc* --exclude=/root/system.tar.bz2 /etc /root
tar: 从成员名中删除开头的“/”
tar: 从硬连接目标中删除开头的“/”


```

### 仅备份比某个时刻还要新的文件

```bash
# 先找出比 /etc/passwd 还要新的文件
# 前面 touch 中介绍过 --newer 和 --newer-mtime
# newer 包含 mtime 和 ctime，而 --newer-mtime 只包含 mtime
[root@study ~]# find /etc -newer /etc/passwd
/etc
/etc/fstab
/etc/group
/etc/gshadow
...

[root@study ~]# ls --full-time  /etc/passwd
-rw-r--r--. 1 root root 2323 2019-10-04 18:31:08.332738182 +0800 /etc/passwd

[root@study ~]# tar -jcv -f /root/etc.newer.the.passwd.tar.bz2 --newer-mtime="2019-10-04" /etc/*
tar: 选项 --newer-mtime: 将日期 ‘2019-10-04’ 当作 2019-10-04 00:00:00
tar: 从成员名中删除开头的“/”
/etc/abrt/
tar: /etc/abrt/abrt-action-save-package-data.conf: 文件未改变；未输出
tar: /etc/abrt/abrt.conf: 文件未改变；未输出
tar: /etc/abrt/gpg_keys.conf: 文件未改变；未输出

# 验证下是否被打进去了，这里搜索都搜不到，确实没有被打进去
[root@study ~]# tar -jtv -f etc.newer.the.passwd.tar.bz2 | grep 'etc/abrt/abrt.conf'
[root@study ~]# tar -jtv -f etc.newer.the.passwd.tar.bz2 | grep 'abrt.conf'

```

### 基本名称: tarfile, tarball ?

**tar** 可以只打包不压缩 `tar -c -f file.tar`，这种文件称为 **tarfile**，如果有压缩就称为 **tarball**。

此外 **tar** 还可以将文件打包到特别的装置中去，例如，`tar -c -f /dev/st0/home /root/etc` ,把 etc 打包到磁带机去（磁带机是一次性读取、写入装置，因此不能使用 **cp** 等指令）

### 特殊应用：利用管线命令与数据流

关于数据流重导向与管线命令在 bash 章节再详细讲解

```bash
[mrcode@study ~]$ cd /tmp/
[mrcode@study tmp]$ tar -cv -f - /etc/ | tar -xv -f -
# 前面是将 /etc/ 打包到 - ，后面是吧 - 解压
# 这里的 - 表示标准的输出 和输出，可以吧 - 想成是内存中的一个缓冲区
# 这里命令像  cp -r /etc /tmp 的效果
# 这里不想用 -r 命令，所以使用 tar 打包到特殊的装置 - 中，然后管线前面输出的作为后面用来解压，没有产生中间文件，完成了复制的功能

 
```

### 例题：系统备份范例

系统上有非常多的目录需要进行备份，也不建议将备份数据放到 **/root** 目录下，假设目前已经知道重要的目录有：

- `/etc/`：配置文件
- `/home/` ：用户的家目录
- `/var/spool/mail/`：系统中所有的邮件信箱
- `/var/spool/cron/`：所有账户的工作排成配置文件
- `/root/`：系统管理员的家目录

前面做过的练习，**/home/loop*** 不需要备份，**/root** 下的压缩文件也不需要备份，假设需要将备份的数据放到 **/backups** 中，并且该目录仅有 **root** 权限进入，此外，每次备份的文件名希望不相同。

```bash
# 创建备份目录，并修改权限
[root@study ~]# mkdir /backups
[root@study ~]# chmod 700 /backups/
[root@study ~]# ll -d /backups/
drwx------. 2 root root 6 10月 29 01:33 /backups/

# 这里的 xxx 需要手动写上想要的日期等字符串每次就不一样了，并不是用脚本变量啥的
tar -zcv -f /backups/xxx.tar.gz --exclude="/home/loop*" --exclude="/root/*.gz" --exclude="/root/*.bz2" --exclude="/root/*.xz" /etc/ /home/ /var/spool/mail /var/spool/cron /root 

[root@study ~]# ll -h /backups/
总用量 13M
-rw-r--r--. 1 root root 13M 10月 29 01:37 xxx.tar.gz
```

### 解压缩后的 SELinux 课题

假如你的系统必须要以备份的数据来回填到原本的系统中，那么需要特别注意复原后的系统 **SELinux** 问题，尤其是在系统文件上面。比如：**/etc** 下的文件群。**SElinux** 是比较特别的细部权限设定，具体的会在第 16 章介绍。**SELinux** 的权限问题，可能让你的系统无法存取某些配置文件内容，导致影响到系统的正常使用权。

有一个例子，通过上面的 **tar** 备份，然后在另外一部系统上还原回来，但是无法正常的登录系统，在单位维护模式去操作系统，看起来一切都正常，但是这里就是无法登录。大部分原因就是因为 **/etc/shadow** 密码文件的 **SELinux** 类型在还原时被更改了，简单的处理方式有如下几个：

- 通过各种可行的救援方式登录系统，修改 **/etc/seliux/config** 文件，将 **SELinux** 改成 **permissive** 模式，重新启动系统就可以了
- 在第一次复原系统后，不要立即重新启动，先使用 `restorecon -Rv /etc` 自动修复下 **SELinux** 的类型即可
- 通过各种可行的方式登录系统，建立 **/.autorelabel** 文件，重新启动后系统会自动修复 **SELinux** 的类型，并且又会再次重新启动，之后就正常了

-------

# vim 程序编辑器

系统管理员的重要工作就是需要修改与设置某些重要软件的配置文件，因此至少得学会一种以上的文字模式下的文本编辑器。所有的 **Linux distribution** 上都有一套文本编辑器 **vi**，而且很多软件默认也是使用 **vi** 作为他们编辑器的接口。此外 **vim** 是进阶版的 **vi**，不但可以用不同颜色显示文字内容，还能够进行诸如 **shell script**、**C program** 等程序编辑功能，可以将 **vim** 视为一种程序编辑器

## vi 与 vim

在 **LInux** 的世界中，绝大部分的配置文件都是以 **ASCII** 的纯文本形态存在的，因此利用简单的文字编辑软件就可以修改配置了

在 **linux** 的文本模式下的编辑器有：**emacs**、**pico**、**nano**、**joe**、**vim** 等，那么为何就要学 **vi** 呢？

## 为何要学 vim

为什么需要学习 **vi** ？原因如下：

- 所有 **Unix Like** 系统都会内置 **vi** 编辑器，其他的编辑器则不一定会存在
- 很多各别软件的编辑接口都会主动调用 **vi** (例如未来会讲解的 **crontab**、**visudo**、**edquota** 等指令)
- **vim** 具有程序编辑的能，可以主动的以字体颜色辨别语法的正确性，方便程序设计
- 因为程序简单，编辑速度相当快

可以将 **vim** 视作是 **vi** 的进阶版，有语法高亮等功能。比如当使用 **vim** 编辑一个 **shell script** 脚本时，**vim** 会依据文件的扩展名或则是文件内的开头信息，判断该文件的内容而自动调用该程序的语法判断。甚至一些 **Linux** 基础配置文件内的语法，都能用 **vim** 来检查，例如第 7 章谈到的 **/etc/fstab** 文件内容

简单说，**vi** 是老式的文字处理器，**vim** 则是程序开发工具（https://www.vim.org/ 官网也是这样介绍的）而不是文字处理软件。因为 **vm** 里面加入了很多额外的功能，例如支持正规表示法的搜索架构、多文件编辑、区块复制等等。

## vi 的使用

基本上 **vi** 共分为三种模式：一般指令模式、编辑模式、指令列命令模式

- 一般指令模式（**command mode**）

  以 **vi** 打开一个文件就直接进入一般指令模式了（默认模式，也简称一般模式）。

  在该模式中，可以使用「上下左右」按键移动光标，可以使用「删除字符」或「删除整列」来处理文件内容，也可以使用「复制、粘贴」

- 编辑模式（**insert mode**）

  在一般模式中可以进行删除、复制、粘贴等动作，但是无法编辑文件内容。

  需要按下「**i**、**I**、**o**、**O**、**a**、**A**、**r**、**R**」等任意按键后才会进入编辑模式，通常会在左下方出现 **INSERT** 或 **REPLACE** 的字样，可以通过 **esc** 按键退出编辑模式，回到一般指令模式

- 指令列命令模式（**commadn-line mode**）

  在一般模式中，输入「**:**、**/**、**?**」任意字符，则光标会移动到最底下的一列。

  在这个模式中，可以提供你搜索、读取、存盘、大量取代字符、离开 **vi**、显示行号等功能

简单说，可以将这三个模式想象成下面的图标来表示

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-vi-vim.png" style="zoom:67%;" />

注意这里互换，编辑模式不能直接换到指令列模式！

### 按键说明

### 第一部分：一般指令模式可用的按钮说明

移动光标的方法

|          按键           | 说明                                                         |
| :---------------------: | :----------------------------------------------------------- |
|  **h** 或 左箭头 **←**  | 光标向左移动一个字符                                         |
|  **j** 或 下箭头 **↓**  | 光标向下移动一个字符                                         |
| **k** 或 向上箭头 **↑** | 光标向上移动一个字符                                         |
|  **i** 或 右箭头 **→**  | 光标向右移动一个字符                                         |
|        特别说明         | **hjki** 在键盘上是排列在一起 的，适合移动光标，移动多个的话可以加上数值再按方向键，比如 **30↓** ，向下移动 **30** 行（注意是一般指令模式下） |
|      **ctrl + f**       | **常用**；向下移动一页，相当于 Page Down 按键                |
|      **ctrl + b**       | **常用**；向上移动一页                                       |
|      **ctrl + d**       | 向下移动半页                                                 |
|      **ctrl + u**       | 向上移动半页                                                 |
|          **+**          | 光标移动到非空格符的下一列                                   |
|          **-**          | 光标移动到非空格符的上一列                                   |
|       `n<space>`        | n 表示数字，如按下 20 ，再按空格键，光标会向右移动 n 个字符  |
| **0** 或功能键 **Home** | **常用**；移动到这一行的最前面字自字符处                     |
|   `$`或功能键 **End**   | **常用**；移动到这一行的后面字符处                           |
|          **H**          | 光标移动到这个屏幕的最上方那一行的第一个字符                 |
|          **M**          | 光标移动到这个屏幕的中央那一行的第一个字符                   |
|          **L**          | 光标移动到这个屏幕的最下方那一行的第一个字符                 |
|          **G**          | **常用**；移动到这个文件的最后一行                           |
|         **nG**          | n 为数字，移动到这个文件的第 n 行。可配合 `:set nu` 显示行号，再移动到具体的行 |
|         **gg**          | **常用**；移动到这个文件的第一行，相当于 1G 的功能           |
|       `n<Enter>`        | **常用**；n 为数字，光标向下移动 n 行                        |

搜索与取代

|           按键           | 说明                                                         |
| :----------------------: | ------------------------------------------------------------ |
|        **/word**         | **常用**；向光标之下寻找一个名称为 word 的字符串             |
|        **?word**         | 向光标之上寻找 word                                          |
|          **n**           | n 为键盘的 n 按键。代表「重复前一个搜索动作」比如找到多个搜索结果的时候，可以按 n 来跳到下一个下一个 |
|          **N**           | 大写的 N 按键，与 n 相反                                     |
|           说明           | 使用 /word 配合 n 或 N 是非常方便的，可以让你重复的找到一些你搜寻的关键词 |
| `:n1，n2s/word1/word2/g` | **常用**；n1 与 n2 为数值。在第 n1 与 n2 列之间查找 word1 这个字符串，并将该字符串替换为 word2；比如：`:100,200s/mrcode/MRCODE/g` 就是在 100 到 200 列之间寻找 mrcode 并替换成大写的 |
|   `1,$s/word1/word2/g`   | **常用**；从第一行到最后一行，将 word1 替换成 word2          |
|  `1,$s/word1/word2/gc`   | **常用**；从第一行到最后一行，将 word1 替换为 word2，在替换前，显示字符让用户确认（confirm）是否需要替换 |

删除、复制、粘贴

|     按键     | 说明                                                         |
| :----------: | ------------------------------------------------------------ |
|   **x,X**    | **常用**；在一行字当中，x 为向后删除一个字符（相当于 del 按键），X 向前删除一个字符 |
|    **nx**    | n 为数值，连续向后删除 n 个字符                              |
|    **dd**    | **常用**；删除光标所在列（这一行文本）                       |
|   **ndd**    | **常用**；删除光标所在的向下 n 行，例如 20dd 则是删除 20 行  |
|   **d1G**    | 删除光标所在到第一行的所有数据                               |
|    **dG**    | 删除光标所在到最后一行的所有数据                             |
|    **d$**    | 删除光标所在处，到该行最后一个字符                           |
|    **d0**    | 删除光标所在处，到该行最前面一个字符，这个 0 就是数值 0      |
|    **yy**    | **常用**；复制光标所在处的行                                 |
|   **nyy**    | **常用**；n 为数值，复制光标所在的向下 n 行                  |
|   **y1G**    | 复制光标所在处到第一行的所有数据                             |
|    **yG**    | 复制光标所在处到最后一行的所有数据                           |
|    **y0**    | 复制光标所在处那个字符到该行第一个字符的数据                 |
|    **y$**    | 复制光标所在处那个字符到该行最后一个字符的数据               |
|   **p,P**    | **常用**；**p** 为将以复制的数据在光标的下一行粘贴上，**P** 则为贴在光标的上一行。 |
|    **J**     | 将光标所在行与下一行的数据结合成同一行                       |
|    **c**     | 重复删除多个数据，例如向下删除 10 行，**10cj**               |
|    **u**     | **常用**；复原前一个动作                                     |
| **ctrl + r** | **常用**；重做上一个动作                                     |
|     说明     | **u** 与 **ctrl + r** 是很常用的执行，一个是复原，一个是重做一次 |
|    **.**     | **常用**；小数点，重复前一个动作。例如先要重复删除、重复粘贴等，按下小数点就可以了 |

### 第二部分：一般指令模式切换到编辑模式可用的按键说明

|  按键   | 说明                                                         |
| :-----: | ------------------------------------------------------------ |
| **i,I** | **常用**；进入插入模式（insert mode）：i 从当前光标所在处插入，I 从当前所在行的第一个非空格符号处插入 |
| **a,A** | **常用**；进入插入模式，a 从当前光标所在的下一个字符开始插入，A 从光标所在行的最后一个字符处开始插入 |
| **o,O** | **常用**；进入插入模式，o 从当前光标所在的下一行插入新行，O 从光标所在处的上一行插入新的行 |
| **r,R** | **常用**；进入取代模式（Replace mode）：r 只会取代光标所在的那个字符一次；R 会一直取代光标所在的文字，直到按下 ESC 为止 |
|  说明   | 上面这些按键中，在 vi 画面左下角会出现 **---INSERT---** 或 **---REPLACE---** 的字样。 |
| **Esc** | **常用**；退出编辑模式，回到一般指令模式中                   |

### 第三部分：一般指令模式切换到指令模式的可用按钮说明

指令列模式的存储、离开等指令

|          按键           | 说明                                                         |
| :---------------------: | ------------------------------------------------------------ |
|         **:w**          | **常用**；将编辑的数据写入硬盘文件中                         |
|         **:w!**         | 若文件属性为「只读」时，强制写入该文件。不过，到底能不能写入，还是跟你对该文件的文件权限有关 |
|         **:q**          | **常用**；离开 vi                                            |
|         **:q!**         | 不想存储，强制离开                                           |
|          说明           | ！惊叹号在 vi 中，常常具有「强制」的意思                     |
|         **:wq**         | **常用**；存储后离开，后面加 ！则表示强制存储后离开          |
|         **ZZ**          | 若文件没有改动，则不存储离开，若文件已经被改动过，则存储后离开 |
|    **:w[filename]**     | 将编辑的数据存成另一个文件，类似另存为                       |
|    **:r[rilename]**     | 在编辑的数据中，读取另一个文件的数据。即将 filename 文件内容加到光标所在处后面 |
| **:n1,n2 w [filename]** | 将 n1 到 n2 的内容存储成 filename 这个文件。（n 说的是行数把？） |
|     **:! command**      | 暂时离开 vi 到指令模式下执行 comman 的显示结果！例如 「:! ls /home」即可再 vi 中查看 /home 下以 ls输出的文件信息；这个笔者感觉很常用，在编辑中往往会忘记路径啥的，通过这个就可以查看了 |

**vi** 环境的变更

|     按键      | 说明                   |
| :-----------: | ---------------------- |
|  **:set nu**  | 在每一行最前面显示行号 |
| **:set nonu** | 取消行号               |

特别注意，在 **vi** 中 「数字」是很有意义的，数字通常代表重复做几次的意思，也有可能是代表去到第几个什么什么的意思。

比如：要删除 **50** 行，使用 「**50dd**」；向下移动 **20** 行，使用 「**20j**」或「**20↓**」

会上面这些指令就已经很厉害了，因为常用到的指令也只有不到一半，除了上面列举到常用的之外，其他的都不用死记硬背，用到再查询即可

### 一个案例练习

http://linux.vbird.org/linux_basic/0310vi/man_db.conf 可以使用这个文件来测试

```bash
[mrcode@study ~]$ cd /tmp/
[mrcode@study tmp]$ mkdir vitest; cd vitest;
# 开启文件
[mrcode@study vitest]$ vi man_db.conf
# 按 i 进入插入模式，把上面网址里面的内容复制，然后粘贴到这里
# 按 Esc 键，回到一般指令模式，并输入「:wq」保存并退出
# 由于该虚拟机还没有网络，不知道怎么下载文件，上面间接的完成了下载的功能，下面开始练习

# 设置行号
:set nu
# 温馨提示，上面的文件其实比书上的的多一行，就是头尾的 # 少一行，下面的第 43,59 才能看到 as

# 移动到第 43 行，然后向右移动 59 个字符，找到小括号里面的单词
「43G」跳转到第 43 行，			「59→」 可以看到光标跳转到了第 59 个字符后面，能看到 "(as 开头的文案"

# 移动到第 1 行，并且向下搜索「gzip」字符，请问在第几行？
「gg」或则 「1G」跳转到第一行，「/gzip」向下搜索，回车后，会定位到 93,23

# 将 29 到 41 行之间的「小写 man 字符串」改为「大写 MAN 字符串」，并且一个一个挑选是否需要修改
# 如果在挑选过程中一直按 y，结果会在最后一列出现改变了几个 man ？
「:29,41s/man/MAN/gc」会高亮被选中的 man，并且在最下面出现提示是否要替换。这里一直按 y
最后会提示：13 substitutions on 13 lines ，改变了 13 个；
注意的是：高亮是所有的都高亮，但是替换只是在指定的行之间

# 修改之后，突然反悔了，要全部复原，有哪些方法？
1. 简单的方法可以一直按 u 一个一个的撤销刚刚的替换
2. 使用强制不存储离开 「:q!」，之后再读取一次文件

# 复制 66 到 71 这 6 行的内容，（含有 MANDB_MAP），并且贴到最后一行之后
先跳转到 66 行 「66G」，再向下复制 6 行 「6yy」，此时会在指令列中显示 6 lines yanked
再跳转到最后一行「G」光标会定位到第 132 行，使用「p」粘贴到当前光标所在的下一行上
注意：粘贴多行的话，先会粘贴第一行，然后在指令列显示有 6 行需要粘贴，需要手动按下回车键确认粘贴，一次回车粘贴一行。粘贴 6 行后，光标会定位到第 138 行

# 113 行到 128 行之间的开头为 # 号的批注数据不要了，要如何删除?
先跳转到第 113 行：「113G」
再输入 「16dd」删除，其实这里为什么是 16 而不是 128 - 113 = 15，这里面的包含头不包含尾而来的，要注意这个是否包不包含当前一行，在上面的文档中有些描述可能就不太准确

# 将整个文件另存为 man.test.config；上面删除 16 行之后，只剩下 116 行了，待会对比两个文件
使用指令 「:w man.test.config」
会在指令列提示："man.test.config" [New] 116L, 4862C written
如果使用 「:wq! man.test.config」 则你没有机会看到上述的提示，另存后就强制退出当前文件了

特别注意：使用 :w man.test.config 指令后，可以直接强制退出当前的文件，因为当前还在 man_db.config 中，强制退出的话，刚才删除的操作等都不会写到 man_db.config 文件中去，而另存里面的文件却保存了刚刚删除等操作后的数据

# 去到第 25 行，并删除 15 个字符，结果出现的第一个单字是什么？
「25G」然后「15x」看到留下的字符串是「_to MANPATH mapping」,下划线是光标所在处

# 在第一行新增一行，该内容输入 「I am a student...」
「gg」，再按「O」大写的 o 会在光标所在处的上一行插入新行

# 存储后离开
:wq
```

上面的练习部分比如删除字符等，与书上的部分内容对不上，我想可能是因为整个文件内容就对不上的原因

### vim 的暂存档、救援恢复与开启时的警告讯息

在你编辑过程中，突然宕机等情况下，在你还诶呦保存的时候，可能就想要是能恢复下刚刚未保存的数据就好了

那么 **vim** 就提供了这样的功能，是通过暂存档来实现的。在使用 **vim** 编辑时，会在被编辑的文件目录下，再建立一个名为 `.filename.swp` 的文件，编辑的数据会被存在该文件中。

来测试这个恢复功能（注：下面的部分指令，现在还未讲解，后续讲解后，再回头来这里练习下）

```bash
[mrcode@study vitest]$ cd /tmp/vitest/
[mrcode@study vitest]$ vim man_db.conf
# 使用 vim 进入文件后，然后按 ctrl + z 组合键，会退出来，并提示下面的信息
# 该组合键的作用是吧 vim man_db.conf 丢到背景去执行（后续在 16 章程序管理中会讲解）
[1]+  Stopped                 vim man_db.conf

# 找到 .swp 的文件
[mrcode@study vitest]$ ls -al
total 48
drwxrwxr-x.  2 mrcode mrcode    96 Oct 29 18:27 .
drwxrwxrwt. 54 root   root    4096 Oct 29 18:26 ..
-rw-rw-r--.  1 mrcode mrcode  4862 Oct 29 17:13 man_db.conf
-rw-r--r--.  1 mrcode mrcode 16384 Oct 29 18:27 .man_db.conf.swp

# 仿真断线停止 vim 工作
# 现在可以暂时理解为宕机了
[mrcode@study vitest]$ kill -9 %1

[1]+  Stopped                 vim man_db.conf
# swp 文件还存在
[mrcode@study vitest]$ ls -al .man_db.conf.swp
-rw-r--r--. 1 mrcode mrcode 16384 Oct 29 18:27 .man_db.conf.swp
[1]+  Killed                  vim man_db.conf

# 再次进入该文件
[mrcode@study vitest]$ vim man_db.conf
E325: ATTENTION		# 错误代码
Found a swap file by the name ".man_db.conf.swp"   # 有暂存当的存在，并显示相关信息
          owned by: mrcode   dated: Tue Oct 29 18:27:34 2019
         file name: /tmp/vitest/man_db.conf  # 这个暂存当实际属于哪个文件
          modified: no
         user name: mrcode   host name: study.centos.mrcode
        process ID: 2259
While opening file "man_db.conf"
             dated: Tue Oct 29 17:13:28 2019

# 下面说明可能发生这个错误的两个主要原因与解决方案
(1) Another program may be editing the same file.  If this is the case,
    be careful not to end up with two different instances of the same
    file when making changes.  Quit, or continue with caution.
(2) An edit session for this file crashed.
    If this is the case, use ":recover" or "vim -r man_db.conf"
    to recover the changes (see ":help recovery").
    If you did this already, delete the swap file ".man_db.conf.swp"
    to avoid this message.

# 下面说明你可以进行的动作
Swap file ".man_db.conf.swp" already exists!
[O]pen Read-Only, (E)dit anyway, (R)ecover, (D)elete it, (Q)uit, (A)bort:

```

上面翻译成中文有如下的主要信息：由于暂存文件的存在，**vim** 会主动判断你的这个文件可能有些问题，上面列出的两个主要原因与解决方案翻译如下：

1. 可能有其他人或程序同时在编辑这个文件

   - 找到另外那个程序或人员，请他将该 **vim** 的工作结束，然后你再继续处理
   - 如果只是想要看该文件的内容并不会有任何修改编辑的行为，那么可以选择开启成为只读文件（**O**），就是那个 **[o] pen Read-Only** 选项

2. 在前一个 **vim** 环境中，可能因为某些不知名的原因导致 **vim** 中断（**crashed**）

   这就是常见的不正常结束 **vim** 产生的后果。解决方案依据不同的情况不同，常见的处理方法为：

   - 如果之前的 **vim** 处理动作尚未存储，此时应该按下 **R** （使用 **(R)ecover** 选项），此时 **vim** 会载入 **.man_db.conf.swp** 的内容，让你自己来决定要不要存储！不过需要你离开 **vim** 后手动删除 **.man_db.conf.swp** 文件，避免下次打开还出现这样的警告
   - 如果你确定这个暂存文件是没有用的，可以直接按下 **D**（**(D)elete it**）删除它

下面是出现的 **6** 个选项的说明：

- `[O]pen Read-Only`：以只读方式打开。不能编辑
- `(E)dit anyway`：以正常方式打开文件，不会载入暂存文件中的内容。不过很容易出现两个使用者互相改变对方的文件等问题。不推荐（如果是多人编辑的情况下）
- `(R)ecover`：加载暂存文件的内容，用在恢复之前未保存的内容，恢复之后记得手动删除暂存文件
- `(D)elete it`：确定暂存文件是无用的，删除它
- `(Q)uit`：离开 **vim**，不会进行任何动作
- `(A)bort`：忽略这个编辑行为，感觉上与 **quit** 非常类似。

## vim 额外功能

其实，目前大部分的**distribution** 都以 **vim** 取代 **vi** 的功能了，因为 **vim** 具有颜色显示、支持许多程序语法（**syntax**）等功能

那么怎么分辨是否当前 **vi** 被 **vim** 取代了呢？

通过 **alias** 分辨

```bash
[mrcode@study vitest]$ alias
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias vi='vim'			# 可以看到这里 vi 调用的就是 vim
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
# 原来上一个章节，笔者使用的不是 vi 而是 vim

```

通过界面分布

### 区块选择（Visual Block)

上面提到的简单 **vi** 操作过程中，几乎提到的都是以行为单位来操作的。那么如果想要搞定一个区块范围呢？如下面这个文件内容

```
192.168.1.1    host1.class.net
192.168.1.2    host2.class.net
192.168.1.3    host3.class.net
192.168.1.4    host4.class.net
192.168.1.5    host5.class.net
192.168.1.6    host6.class.net
192.168.1.7    host7.class.net
192.168.1.8    host8.class.net
192.168.1.9    host9.class.net

```

假设想要将 **host1**，**host2** 等复制，并且加到每一行的后面，即每一行的结果变成 `192.168.1.1 host1.class.net host2.class.net...` 。在传统或现代的窗口型编辑器似乎不容易达到这个需求，在 **vim** 中可以使用 **Visual Block** 区块功能。当按下 **v** 或 **V** 或则 **ctrl+v** 时，光标移动过的地方就会开始反白，按键含义如下

|   按键   | 含义                                 |
| :------: | ------------------------------------ |
|    v     | 字符选择，会将光标经过的地方反白选择 |
|    V     | 行选择，会将光标经过的行反白选择     |
| ctrl + v | 区块选择，可以用长方形的方式选择     |
|    y     | 将反白的地方复制起来                 |
|    d     | 将反白的地方删除                     |
|    p     | 将刚刚复制的区块，在光标所在处贴上   |

实践练习区块怎么使用

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-block-practice-1.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-block-practice-2.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-block-practice-3.png)

### 多文件编辑

想象这样一个场景：要将刚刚 **host** 内的 **IP** 复制到 **/etc/hosts** 这个文件去，那么该如何编辑？我们知道在 **vi** 内可以使用 **:r filename** 来读入某个文件的内容，不过是将整个文件读入，如果只想要部分内容呢？这个时候就可以使用 **vim** 的多文件编辑功能了。使用 **vim** 后面同时接好几个文件来同时开启，相关按键有

|    按键    | 含义                            |
| :--------: | ------------------------------- |
|   **:n**   | 编辑下一个文件                  |
|   **:N**   | 编辑上一个文件                  |
| **:files** | 列出目前这个 vim 开启的所有文件 |

没有多文件编辑的话，实现将 **A** 文件内的 **10** 条消息移动到 **B** 文件中，通常需要开两个 **vim** 窗口来复制，但是无法在 **A** 文件下达 **nyy** 再跑到 **B** 文件去 **p** 的指令。

练习多文件编辑

```bash
# 开启两个文件，host 是我们刚刚编辑的那个
vi host /etc/hosts
# 使用 files 指令查看编辑的文件有哪些
# 在一般指令模式下输入 :files 指令
:files
  1 %a   "host"                         line 1
  2      "/etc/hosts"                   line 0
Press ENTER or type command to continue
# 上面列出了两个，并告知按下任意键会回到 vim 的一般指令模式中

1. 回到一般指令模式中，跳转到第一行，输入 4yy 复制 4 行数据
2. 输入 :n 会来到第二个编辑的文件，也就是 /etc/hosts
3. 按下 G 跳转到最后一行，再按 p 贴上 4 行数据
4. 按下多次 u 来取消刚才的操作，也就是恢复 /etc/hosts 中数据到原样
5. 最终按下 :q 离开 vim 编辑器
```

### 多窗口功能

在开始逐个小节前，先来想象两个情况：

1. 当我有一个文件非常的大，查阅到后面的数据时，想要*对照*前面的数据，是否需要使用 **ctrl + f** 与 **ctrl + b** 或 **pageup**、**pagedown** 功能键来前后翻页对照？
2. 我有两个需要对照看的文件，不想使用前一小节提到的多文件编辑功能

vim 有「分区窗口」的功能，在指令行模式输入 `:sp filename`即可，**filename** 存在则开启另一个文件，不存在则出现的是相同的文件内容

使用 **vim /etc/man_db.conf**，然后输入 「**:sp**」就会出现上下各一个窗口，两个窗口都是同一个文件内容

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-vi-muti-window1.png)

再次输入 `:sp /etc/hosts` 则会再分出来一个窗口

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-vi-muti-window2.png)

可以使用 ctrl + w + ↑ 和 ctrl + w + ↓ 组合键来切换窗口（笔者测试使用 ctrl + w 可以切换 ctrl + w + 箭头触发了宿主机的 ui 切换功能）

多窗口情况下的按键功能

|        按键         | 说明                                                         |
| :-----------------: | ------------------------------------------------------------ |
| **:sp [filename]**  | 开启一个新窗口，不加 **filename** 则默认打开当前文件，否则打开指定文件 |
| **ctrl + w + j/↓**  | 使用方法：先按下 **ctrl** 不放，再按下 **w** 后放开所有的按键，再按下 **j** 或向下的箭头键，则光标可移动到下方的窗口 |
| **ctrl + w + k/上** | 同上                                                         |
|  **ctrl + w + q**   | 就是 :**q** 结束离开。比如：想要结束下方的窗口，先使用 **ctrl + w + j** 移动到下方窗口，输入 **:q** 或则按下 **ctrl + w + q** 离开 |

### vim 的挑字补全功能

我们知道在 **bash** 环境下可以按下 **tab** 按钮来达成指令、参数、文件名的补全功能，还有 **windows** 系统上的各种程序编辑器，如 **notepad++**，都会提供：语法检验和根据扩展名来挑字的功能。

在语法检验方面，**vim** 已经使用颜色来达成了，建议可以记忆的 **vim** 补齐功能如下：

|       组合按键       | 补齐内容                                                  |
| :------------------: | --------------------------------------------------------- |
| ctrl + x -> ctrl +n  | 通过目前正在编辑的这个「文件的内容文件」作为关键词，补齐; |
| ctrl + x -> ctr + f  | 以当前目录内的「文件名」作为关键词，予以补齐              |
| ctrl + x -> ctrl + o | 以扩展名作为语法补充，以 vim 内置的关键词，予以补齐       |

用法：先输入关键词如 host 再按 ctrl + x，再按 ctrl + n，如果有可补齐的待选文案，会显示下拉列表给你选择

实践练习：使用 css 美化功能时，突然想到有个北京的东西要处理，但是忘记了背景 CSS 关键语法，就可以用如下的模式来处理

```bash
# 一定要是 .html 否则不会使用正确的语法检验功能
vi htmltest.html  
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/image-20191103234338120.a3984c1f.png)

### vim 环境设置与记录：`~/.vimrc`、`~/.viminfo`

有没有发现：如果以 **vim** 软件来搜寻一个文件内部的某个字符串时，这个字符串会被反白，而下次我们再次以 **vim** 编辑这个文件时，该搜索的字符串反白的情况还是存在的，甚至于在编辑其他文件时，如果也存在该字符，也会主动反白。另外，当我们重复编辑同一个文件时，当第二次进入该文件时，光标竟然在上次离开的那一行上面

这是因为 **vim** 会主动将你曾经做过的行为记录在 `~/.viminfo` 文件中，方便你下次可以轻松作业

此外，每个 **distribution** 对 **vim** 的预设环境都不太相同，例如：某些版本在搜寻关键词时并不会高亮度反白，有些版本则会主动帮你进行缩排的行为。这些其实都可以自定设置的，**vim** 的环境设置参数有很多，可以在一般模式下输入「**:set all**」来查询，不过可设置的项目太多了，这里仅列出一些平时比较常用的一些简单设置值，供你参考

**vim** 的环境设置参数

|                    item                    | 含义                                                         |
| :----------------------------------------: | ------------------------------------------------------------ |
|         **:set nu**、**:set nonu**         | 设置与取消行号                                               |
|   **:set hlsearch**、:**set nohlsearch**   | hlsearch 是 high light search （高亮度搜索）。设置是否将搜索到的字符串反白设置。默认为 hlsearch |
| **:set autoindent**、**:set noautoindent** | 是否自动缩排？当你按下 Enter 编辑新的一行时，光标不会在行首，而是在于上一行第一个非空格符处对齐 |
|              **:set backup**               | 是否自动存储备份文件，一般是 nobackup 的，如果设置为 backup，那么当你更改任何一个文件时，则源文件会被另存一个文件名为 filename~ 的文件。如：编辑 hosts，设置 :set backup ，那么修改 hosts 时，在同目录下就会产生 hosts~ 的文件 |
|               **:set ruler**               | 右下角的状态栏说明，是否显示或不显示该状态的显示             |
|              **:set shwmode**              | 是否要显示 ---INSERT-- 之类的提示在左下角的状态栏            |
|          **:set backpace=(012)**           | 一般来说，如果我们按下 i 进入编辑模式后，可以利用退格键（baskpace）来删除任意字符的。但是某些 distribution 则不允许如此。此时，可以通过 backpace 来设置，值为 2 时，可以删除任意值；0 或 1 时，仅可删除刚刚输入的字符，而无法删除原本就已经存在的文字 |
|                **:set all**                | 显示目前所有的环境参数设置                                   |
|     **:syntax on** 、 **:syntax off**      | 是否依据程序相关语法显示不同颜色                             |
|    **:set bg=dark**、**:set bg=light**     | 可以显示不同颜色色调，预设是 light。如果你常常发现批注的字体深蓝色是在很不容易看，就可以设置为 dark |

总之这些常用的设置非常有用处，但是在行模式下设置只是针对当前打开的 **vim** 有效果；想要修改默认打开就生效的话，可以修改 `~/.vimrc` 这个文件来达到（如果此文件不存在，请手工创建）

```bash
vi ~/.vimrc
" 该文件的注释是使用双引号表达
set hlsearch    "高亮度反白
set backspace=2 "可随时用退格键删除
set autoindent  "自动缩进
set ruler       "可现实最后一列的状态
set showmode    "左下角那一列的状态
set nu          "在每一行的最前面显示行号
set bg=dark     "显示不同的底色色调
syntax on       "进行语法检验，颜色显示


# 保存后，再次打开最明显的就是自动显示行号了，可见是生效了的

```

### vim 常用指令示意图

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-commands.jpeg)

----

## 其他 vim 使用注意事项

**vim** 功能很强大，但是上手不是那么容易，下面分享一些需要注意地方

### 中文编码的问题

在 **vim** 里面无法显示中文，那么你需要考虑：

1. **Linux** 系统默认支持的语系数据，与 `/etc/locale.conf` 有关
2. 终端界面（**bash**）的语系；与 **LANG**、**LC_ALL** 几个参数有关
3. 文件原本的编码
4. 开机终端机的软件，例如在 **GNOME** 下的窗口

上面最重要的是第 3 和 4 点，只要这两点编码一致，就能不乱码；

可以使用如下的方式来暂时修改 **tty** 的语系（前面都讲过的）

```bash
LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```

### DOS 与 Linux 的断行字符

[cat 命令](https://zq99299.github.io/linux-tutorial/tutorial-basis/06/03.html) 中讲解过 DOS（windows 系统）建立的文件的特殊格式，发现 DOS 为 `^M$`，而 linux 是 `$`，windows 是 CR（^M） 与 LF（$） 两个符号组成的，Linux 是 LF ；对于 Linux 的影响很大

在 Linux 指令开始执行的时候，判断依据是 Enter 按键（也就是换行符，回车一下就会出现换行符），由于两个系统的换行符不一致，会导致 shell script 程序文件无法执行

可以使用 dos2unix 指令来一键转换，但是目前为止，虚拟机还没有网络，无法安装，笔者这里只记录用法

```bash
dos2unix [-kn] file [newfile]
unix2dos [-kn] file [newfile]

-k：保留该文件原本的 mtime 时间格式（不更新文件上次内容经过修订的时间）
-n：保留原本的旧文件，将转换后的内容输出到新文件，如：dos2unix -n old new
    
```

练习

```bash
# 将 /etc/man_db_conf 重新复制到 /tmp/vitest 下，并将其修改为 dos 断行
cd /tmp/vitest
cp -a /etc/man_db_conf .
ll man_db.conf
unix2dos -k man_db.conf

# 将上述的 man_db.conf 转成 linux 换行符，并保留旧文件，新文件防御 man_db.conf.linux
dos2unix -k -n man_db.conf man_db.conf.linux
ll man_db.conf*
file man_db.conf*
man_db.conf:	ASCII text,with CRLF line terminators   # 说明了是 CRLF 换行
man_db.conf.linux: ASCII text
```

### 语系编码转换

文件编码转换，可以使用 **iconv** 指令来做，比如下面这一段文字内容（没有网络下载不了，直接粘贴复制保存把）

```bash
每個系統管理員都應該至少要學會一種文字介面的文書處理器，以方便系統日常的管理行為。
在 Linux 上頭的文書處理軟體非常的多，不過，鳥哥還是建議使用 vi 這個正規的文書處理器。
這是因為 vi 幾乎在任何一個 Unix Like 的機器都存在，學會他，輕鬆很多啊！
而且後來的計畫也有推出 vim 這個 vi 的進階版本，可以用的額外功能更多了！
vi 是未來我們進行 shell script 程式的編寫與伺服器設定的重要工具喔！
而且是非常非常重要的工具，一定要學會才行啊！ ^_^

無論如何，要管理好 Linux 系統時，純文字的手工設定仍是需要的！那麼在 Linux 底下有哪些文書編輯器呢？
可多了～例如 vi, emacs, xemacs, joe, e3, xedit, kedit, pico .... 多的很～
各家處理器各有其優缺點，您當然可以選擇任何一個您覺得適用的文書處理器來使用。不過，鳥哥還是比較建議使用 vi 
啦！這是因為 vi 是 Unix Like 的機器上面預設都有安裝的軟體，也就是說，您一定可以接觸到這個軟體就是了。
另外，在較新的 distributions 上，您也可以使用較新較先進的 vim 這個文書處理器！
vim 可以看做是 vi 的進階軟體，他可以具有顏色顯示，很方便程式開發人員在進行程式的撰寫呢！

簡單的來說， vi 是老式的文書處理器，不過功能已經很齊全了，但是還是有可以進步的地方。
vim 則可以說是程式開發者的一項很好用的工具，就連 vim 的官方網站 (http://www.vim.org) 
自己也說 vim 是一個『程式開發工具』而不是文書處理軟體～^_^。
因為 vim 裡面加入了很多額外的功能，例如支援正規表示法的搜尋架構、多檔案編輯、區塊複製等等。
這對於我們在 Linux 上面進行一些設定檔的修訂工作時，是很棒的一項功能呢！

底下鳥哥會先就簡單的 vi 做個介紹，然後再跟大家報告一下 vim 的額外功能與用法呢！

 
```

```bash
[root@study ~]# cd /tmp/
[root@study tmp]# vi big5.txt
# 把上面的内容保存到该文件中，然后使用这个文件来练习

```

语法

```bash
iconv --list
iconv -f 原本编码 -t 新编码 filename [-o newfile]

--list：列出 iconv 支持的语系数据
-f：from 来源，原本的编码格式
-t：to，即要转换的编码格式
-o file：如果要保留原本的文件，使用 -o 新文件名，可以建立新编码文件
    
```

实践练习

```bash
# 查看原本文件编码，这里由于刚刚终端机是 utf-8 的，保存后就是 utf-8 了
[root@study tmp]# file big5.txt 
big5.txt: UTF-8 Unicode text
# 那么将 UTF8 转成 big5
[root@study tmp]# iconv -f utf8 -t big5 big5.txt -o big5.txt.big5
[root@study tmp]# file big5.*
big5.txt:      UTF-8 Unicode text
big5.txt.big5: ISO-8859 text
# 不过笔者测试，由 utf8 转成 big5 会乱码，但是还可以转回来

# 下面的指令可以将上面繁体中文，转为简体中文，并且还是 utf8 格式
# 看指令是吧 utf8 转成 big5，再转成 gb2312，再转成 utf8
[root@study tmp]# iconv -f utf8 -t big5 big5.txt | \
> iconv -f big5 -t gb2312 | iconv -f gb2312 -t utf8 -o vi.gb.utf8
```

-----

# 认识与学习 BASH

在 **Linux** 环境下，如果你不懂 **bash** 是什么，那么其他的东西就不用学习了，所以 **bash** 非常重要

**bash** 的东西非常多：变量的设置与使用、**bash** 操作环境的建立、数据流重导向功能、管线命令 等

本章几乎是所有指令模式（**commadn line**）与未来主机维护与管理的重要基础。

## 认识 BASH 这个 Shell

在第一章讲到：管理整个计算机硬件的其实是操作系统的核心（**kernel**），一般使用者只能通过 **shell** 来与核心沟通。那么有系统有多少 **shell** 可用呢？以及为什么要使用 **bash**？本章告诉你答案

### 硬件、核心与 Shell

什么是 **Shell**？几乎上都听听到过，因为只要有操作通，那么就离不开 **Shell** 这个东西。在讨论 **Shell**之前，先来了解一下计算机的运作状况。举个例子：当你要计算机传输出来「音乐」的时候，你的计算机需要什么东西呢？

1. 硬件：有「声卡芯片」设备，才能发声
2. 核心管理：操作系统支持这个芯片组，以及提供芯片的驱动程序
3. 应用程序：需要使用者（就是你）输入发生声音的指令

这就是基本的一个输出声音所需要的步骤，可以用如下图示来说明：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-shell.png" style="zoom:67%;" />

在第 0 章的操作系统章节曾提到过：操作系统其实是一组软件，控制整个硬件与管理系统的活动检测，如果这组软件能被用户随意的操作，若使用者应用不当，将会使得整个系统崩溃！所以不能随便被一些没有管理能力的终端用户随意使用

但是可以考虑使用程序来指挥核心，在第 0 章所提供的操作系统图示中，可以发现应用程序其实是在最外层，就如同鸡蛋的外壳一样，因此也就被称呼为壳程序（**Shell**）

其实壳程序的功能只是提供用户操作系统的一个接口，因此整个壳程序需要可以呼叫其他软件的功能，如前面提到过的很多指令，包括 **man**、**chmod**、**chown**、**vi**、**fdisk**、**mkfs** 等指令，这些指令都是独立的应用程序，但是可以通过壳程序（指令行模式）来操作这些应用程序

也就是说，只要能够操作应用程序的接口都能够成为壳程序。狭义的壳程序指的是指令方面的软件，包括本章要介绍的 **bash** 等。广义的壳程序包括图形界面软件。

### 为何要学习文字接口的 shell？

文字接口的 **shell** 不好学，但是学了之后好处多多，因此需要克服这个困难

### 文件接口的 shell：大家都一样

为什么要学习 **shell**？**x window** 下的 **ui** 工具点一点就可以达到目的，比如 **Webmin** 是真的好用，他可以帮我很建议的设置我们的主机，甚至是一些很进阶的设置都可以帮我们搞定

但是这里还是需要再强调下：**x window** 与 **web** 接口的工具，它虽然功能强大，只是把所有利用到的软件整合到一起的一组程序而已，并非一个完整的套件，所以某些当你升级或则是使用其他套件管理模块（如 **tarball** 而非 **npm** 文件等）时，就会造成设置的困扰了。甚至不同的 **distribution** 所设计的 **x window** 接口也都不相同，这样也造成学习方面的困扰

而几乎各家 **distribution** 使用的 **bash** 都是一样的，如此一来几乎上能够轻轻松松的转换不同的 **distribution**

### 远程管理：文字接口就是比较快

**Linux** 的管理常常需要通过远程联机，而联机时文字接口的传输速度一定比较快，而且不容易出现断线或则是信息外流的问题，因此 **shell** 真的是得学习的一项工具，而且会让你更深入 **Linux**。

### Linux 的任督二脉：shell

所谓技多不压身，书到用时方恨少。此外，如果你真的有心要将你的主机管理好，那么良好的 **shell** 程序编写是一定需要的！

例如作者的经验来说，管理的主机不到十台，但是如果每台书籍都要花上几十分钟来查询他的登录文件信息以及相关的信息，可能会疯掉，太没有效率。但是通过 **shell** 提供的数据流重导向以及管线命令，分析登录信息只要花费不到十分钟就可以看完所有的主机的重要信息了

由于学习 **shell** 的好处真的多多，想要管理好系统的话，**shell** 就像是打通任督二脉一样，任何武功都能随你应用

## 系统的合法 **shell** 与 **/etc/shells** 功能

由于早年 **Unix** 年代，发展众多，所以 **shell** 依据发展者的不同就有许多版本，例如 **Bourne SHell**（**sh**）、在 **Sun** 里头预设的 **CSHell**、商业上常用的 **K Shell**、**TCSH** 等，每一种 **Shell** 都各有其特点。而 **Linux** 使用的这一种版本就称为「**Bourne Again SHell**（简称 **bash**），是 B**ourne Shell** 的增强版，也是基于 **GNU** 的架构下发展出来的

### shell 简单历史

第一个流行的 **shell** 是由 **Steven Bourne** 发展出来的，所以称为 **Bourne shell**（简称 sh）。后来另一个广泛流传的 **shell** 是由柏克莱大学的 **Bill Joy** 设计依附于 **BSD** 版的 **Unix** 系统中的 **shell**，该 **shell** 语法类似 c 语言，所以才得名为 **C shell**（简称 **csh**）。 **Sun** 主要是 **BSD** 的分支之一，而且 **Sun** 主机势力庞大，所以 **csh** 流传广泛

### 目前 Linux 可以使用的 shells

以 **CentOS 7** 为例，有多少我们可以使用的 **shells** 可以通过检查 `/etc/shells` 文件，至少有以下几个

- `/bin/sh`：已被 `/bin/bash` 所取代
- `/bin/bash`：**Linux** 预设的 **shell**
- `/bin/tcsh`：整合 **C Shell** 提供更多的功能
- `/bin/csh`：已被 `/bin/tcsh` 取代

虽然各家 **shell** 的功能都差不多，但是在某些语法下达方面则所有不同，因此建议需要选择一种 **shell** 来熟悉。**Linux** 预设就是使用的 **bash**，因此最初学会它就非常了不起了。

为什么系统上合法的 **shell** 要写入 `/etc/shells` 这个文件？因为系统某些服务在运行过程中，回去检查使用者能够使用的 shells

举例来说：某些 **FTP** 网站回去检查使用者的可用 **shell**，而如果你不想让这些用户使用 **FTP** 以外的主机资源时，可能会给予该使用者一些怪怪的 **shell**，让使用者无法以其他服务登录主机。这个时候，你就可以将那些怪怪的 **shell** 写到 `/etc/shells` 中。举例来说， **CentOS 7** 的 `/etc/shells` 里头就有个 `/sbin/nologin` 文件的存在，这个就是我们说的怪怪的 **shell**

我这个使用者上面时候可以取得 **shell** 来工作呢？还有预设会取得哪一个 **shell** 呢？在登录终端的时候，系统就会给一个 **shell** 进行工作，而这个登录取得的 **shell** 就记录在 `/etc/passwd` 文件内

```bash
[mrcode@study ~]$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
...

```

如上所示，每一行的最后一个数据，就是你登录后可以取得的预设 **shell**，系统账户 **bin** 与 **daemon** 等就是使用哪个怪怪的 `/sbin/nologin` ，关于使用者这部分的内容，在后续 第十三章的账户管理中讲解

## Bash shell 的功能

**Linux** 预设的 `/bin/bash` 是 **GNU** 计划中重要的工具软件之一，目前也是 **Linux distribution** 的标准 **shell**，主要兼容于 **sh**，并且依据一些使用者需求加强的 **shell** 版本。主要有点有如下几个

### 命令编修能力（history）

能记录使用过的指令，只要在指令列按「上下键」可以浏览历史记录，默认的指令记忆条数可达 1000 个。

指令记录在你的家目录内的 `.bash_history` ，该文件记录的是前一次登录以前所执行过的指令，而当前这一次的指令被暂存在内存中，当你成功注销系统后，指令记录才会存入该文件中

这种工作机制的优点：最大好处可以查询曾经做过的举动，如此可以知道你的执行步骤，那么就可以追踪你曾经下达过的指令，以作为除错的重要流程，但是如果被黑客入侵，只要翻阅你曾经执行过的指令，刚好你的指令又与系统有关（比如登录 **mysql** 的密码在指令列上），那么很容易数据库密码就被泄露了

那么该功能和历史记录数是好是坏？只能是仁者见仁智者见智了

### 命令与文件补全功能（tab 按键的好处）

在 **bash** 中常常使用 **tab** 补全功能，可以让你效率提升，并且减少输入时数据错误的几率，

- 命令补全：**tab** 接在一串指令的第一个字的后面
- 文件补全：**tab** 接在一串指令的第二个字以后时
- 若安装 **bash-completion** 软件，则在某些指令后面使用 **tab** 按键时，可以进行「选项**/**参数的补齐」功能

### 命令别名设置功能（alias）

假如我需要知道这个目录下的所有文件（包含隐藏文件）以及所有的文件属性，那么必须下达 `ls -al`这样的指令，可以通过 **alias** 来自定义命令取代上面的命令

```bash
alias lm='ls -al'
# 这里使用 lm 取代了 ls -al
```

### 工作控制、前景背景控制（job control、foreground、background）

这部分在 第十六章 **Linux** 过程控制中详细讲解。使用前、背景可以让工作进行得更为顺利，而工作控制（**jobs**）用途则更广，可以让我们随时将工作丢到背景中执行，而不怕不小心使用了 **ctrl + c** 来停掉该程序。此外，可以在单一登录的环境中，达到多任务的目的

### 程序化脚本（shell scripts）

在 **DOS** 年代将一堆指令写在一起的批处理文件，在 **Linux** 下的 **shell scripts** 则发挥更强大的功能，可以将你平时管理系统常需要下达的连续指令写成一个文件，该文件并且可以通过对谈交互式的方式来进行主机的侦测工作。也可以借由 **shell** 提供的环境变量及相关指令来进行设计，以前在 **DOS** 下需要程序语言才能写的东西，在 **Linux** 下使用简单的 **shell scripts** 就可以实现，这部分在 第十二章 讲解

### 通配符（wildcard）

举例来说：想要知道 `/usr/bin` 下有多少以 X 开头的文件，使用`ls -l /usr/bin/X*` 就可以知道，此外还有其他可用的通配符

## 查询指令是否为 Bash shell 的内置命令：type

可以通过 **man bash** 查看联机帮助文档，内容很多，让你看几天几夜也无法看完，不过该 **bash** 的 **man page** 中，还有其他文件的说明，比如 **cd** 指令也在该 **man page** 内。在输入 **man cd** 时，最上方也出现一堆的指令介绍，这是由于方便 **shell** 的操作内置了这些指令

可以通过 **type** 指令来观察某个指令是否是内置指令

```bash
type [-tpa] name

- 不加任何选项与参数时，type 会显示出 name 是外部指令还是 bash 内置指令
- t：type 会将 name 以下面这些字眼显示出他的意义
	file：表示为外部指令
	alias：为别名
	builtin：bash 内置指令
- p：如果后面接的 name 为外部指令时，才会显示完整文件名
- a：根据 PATH 变量定义的路径中，将含有 name 的指令都列出来，包含 alias

```

实践练习

```bash
# 查询 ls 这个指令是否为 bash 内置
# 未加任何参数，列出 ls 的最主要使用情况
[mrcode@study ~]$ type ls
ls is aliased to `ls --color=auto'
# 仅列出执行时的依据
[mrcode@study ~]$ type -t ls
alias
[mrcode@study ~]$ type -a ls
ls is aliased to `ls --color=auto'	# 最先使用 aliased
ls is /usr/bin/ls		# 还找到外部指令在 /bin/ls

# 查看 cd 的情况
[mrcode@study ~]$ type cd
cd is a shell builtin			# cd 是 shell 内置指令
 
```

## 指令的下达与快速编辑按钮

前面讲过怎么下达指令，这里仅以反斜杠来说明下指令下达方式

```bash
# 如果指令串太长的话，如何使用两行来输出
[mrcode@study ~]$ cp /var/spool/mail/root /etc/crontab \
> /etc/fstab /root
    
```

使用 `\` 来跳脱回车键，前面的 `>` 是跳脱模式下的标识符，还有组合按键帮助我们快速实现功能

|         组合键         | 功能与示范                                               |
| :--------------------: | -------------------------------------------------------- |
| `ctrl + u`、`ctrl + k` | 快速删除：分别是从光标处向前删除指令串，及向后删除指令串 |
|  `ctrl + a、ctrl + e`  | 快速移动：分别是让光标移动到整个指令串的最前面和最后面   |

总之，当我们顺利的在终端机 **tty** 上面登录后，**Linux** 就会依据 `/etc/passwd` 文件的设置给我一个 **shell**（预设是 **bash**），可以通过 **man** 来查询指令的使用方式，根据上面下达指令的方式来操作 **shell**

## Shell 的变量功能

变量是 **bash** 环境中非常重要的一个概念

### 什么是变量

简单说，某一个特定字符串代表不固定的内容；比如：`y = ax+b` 等号左边的是变量，右边的是变量的内容，使用简单的变量来取代另一个比较复杂或则是容易变动的数据，这样做的好处就是方便！

### 变数的可变性与方便性

举例来说，我们每个账户的邮件信箱预设是以 **MAIL** 这个变量来进行存取的，当不同的账户登录取得的变量内容如下所示

```bash
dmtsai 的 MAIL = /var/spool/mail/dmtsai 
root 的 MAIL = /var/spool/mail/root
mrcode 的 MAIL = /var/spool/mail/mrcode
```

好处是则是邮件处理程序读取 **MAIL** 变量就能为对应的账户处理了

### 影响 bash 环境操作的变量

某些特定变量会影响到 **bash** 的环境，例如前面多次提到的 **PATH** 变量，它会影响指令是否能找到。

为了区别与自定义变量的不同，环境变量通常以大写字符来表示

### 脚本程序设计（shell script）的好帮手

写过程序的都知道，变量在程序中的重要性，比如在 **shell script** 中，前面几行定义变量，后面的大量逻辑处理使用变量，那么修改变量的内容，就能让后续的处理逻辑改变，达到非常方便的效果

## 变量的取用与设置：echo 、变量设置规则、unset

### 变量的取用：echo

```bash
echo $variable
echo $PATH
echo ${PATH}			# 作者推荐使用这种方式取用

```

关于 **echo** 的功能也较多，自行 **man echo**，这只是用来显示变量内容

```bash
# 在屏幕上显示你的环境变量 HOME 与 MAIL
[mrcode@study ~]$ echo $HOME
/home/mrcode
[mrcode@study ~]$ echo ${MAIL}
/var/spool/mail/mrcode

```

变量的修改使用等号赋值

```bash
[mrcode@study ~]$ echo ${myname}
											# 这里没有任何数据，该变量不存在或未设置
[mrcode@study ~]$ myname=mrcode
[mrcode@study ~]$ echo ${myname}
mrcode

# 在 bash 中，当一个变量名称未被设置时，预设内容就是 空
    
```

需要注意的是：每一种 **shell** 的语法都不相同，在 **bash** 中 **echo** 一个不存在的变量不会保存，并显示空，其他的可能就会报错了

### 变量的设置规则

- 变量与变量内容以一个「**=**」来连接

  ```bash
  myname=Mrcode
  ```

- 等号两边不能直接接空格符号

  ```bash
  myname = Mrcode		# java 语法格式强迫症不要这样写
  ```

- 变量名称只能是英文字母与数字

- 变量内容若有空格可以使用双引号或单引号限定，但是以下除外

  - 「**$**」在双引号中可以保留原本的特性

    ```bash
    var="lang is $LANG"
    则使用 echo $var 则得到输出信息为：lang is utf8 等的字样
    ```

  - 「**‘**」在单引号内的特殊字符仅为一般字符

    ```bash
    var='lang is $LANG'
    则输出信息为：lang is $LANG
    ```

- 可用跳脱字符「`\`」把特殊字符变成一般字符

  ```bash
  # 就是转义符
  myname=mrcode\ Tsai  # 这里将空格转义成普通字符了
  ```

- 在一串指令的执行中，还需要使用其他额外的指令所提供的信息时，可以使用反单引号「`指令`」或「**$(指令)**」

  ```bash
  # 取得核心版本的设置
  [mrcode@study ~]$ echo  $version
  
  [mrcode@study ~]$ version=$(uname -r); echo $version
  3.10.0-1062.el7.x86_64
  
  ```

- 若该变量为扩增变量内容时，则可使用如下方式累加变量

  ```bash
  PATH="$PATH:/home/bin"
  PATH=${PATH}:/home/bin
      
  ```

- 若该变量需要再其他子程序执行，则需要以 **export** 来使变量变成环境变量

  ```bash
  export PATH
  
  ```

- 通常大写字符为系统默认变量，自行设定变量可以使用小写字符，方便判断（纯粹按个人风格决定）

- 取消变量使用 **unset**

  ```bash
  # 如取消 myname 的设置
  unset myname
  ```

实践练习

```bash
# 练习 1：设置变量 name，内容为Mrcode
[mrcode@study ~]$ 12name=mrcode			# 变量命名语法问题
bash: 12name=mrcode: command not found...
[mrcode@study ~]$ name = Mrcode
bash: name: command not found...		# 变量命名语法问题
[mrcode@study ~]$ name=Mrcode			# 成功

# 练习 2：接上面，把内容修改为 Mrcode's name，就是内容中含有特殊字符
[mrcode@study ~]$ name=Mrcode's name
> c^C
# 单引号与双引号必须要成对出现，上面只有一个单引号，按下回车键时，还可以继续输入内容
# 不能达到题目要求，记得使用 ctrl + c 结束
[mrcode@study ~]$ name="Mrcode's name"
# 在双引号中，单引号变为一般字符
[mrcode@study ~]$ name=Mrcode\'s\ name
#使用转义符转义特殊字符

# 练习 3： 在 PATH 变量中累加 /home/dmtsai/bin 这个目录
PATH=$PATH:/home/dmtsai/bin
PATH="$PATH":/home/dmtsai/bin
PATH=${PATH}:/home/dmtsai/bin
# 上面三种格式都可以，但是下面的例子就不见得了

# 练习 4：将 name 的内容多出 yes
[mrcode@study ~]$ echo $name
Mrcode's name
[mrcode@study ~]$ name=$nameyes
[mrcode@study ~]$ echo $name

[mrcode@study ~]$ 
# 如没有双引号或则 {} 则完全变成了一个变量 nameyes
# 正确的如下
name="$name"yes
name=${name}yes

# 练习 5：如何让我刚刚设置的 name=Mrcode 可以用在下个 shell 程序？
[mrcode@study ~]$ name=Mrcode
[mrcode@study ~]$ bash				# 进入所谓的子程序
[mrcode@study ~]$ echo $name
		# 这里并没有获取到刚刚设置的值
[mrcode@study ~]$ exit			# 退出子程序
exit
[mrcode@study ~]$ echo $name
Mrcode
[mrcode@study ~]$ export name		# 导出变量
[mrcode@study ~]$ bash
[mrcode@study ~]$ echo $name		# 在子程序中找到了
Mrcode
[mrcode@study ~]$ exit
exit
```

什么是子程序？像上面那样，在当前这个 **shell** 下，去启用另一个新的 **shell**，新的哪个 **shell** 就是子程序了。在一般的状态下，父程序的自定义变量是无法在子程序内使用的。可以通过 **export** 将变量变成环境变量，就可以在子程序中使用了。

至于子程序相关概念，在第十六章程序管理中讲解。

```bash
# 练习 6：如何进入到你目前核心的模块目录？
cd /lib/modules/3.10.0-1062.el7.x86_64/kernel/
# 由于每个 linux 能够拥有多个核心版本，且几乎 distribution 的核心版本都不相同
# 所以上面的指令无法通用，这个时候可以使用其他额外指令语法来达成
cd /lib/modules/`uname -r`/kernel
cd /lib/modules/$(uname -r)/kernel

```

其实上面的指令可以说是做了两次动作：

1. 先进行反单引号内的动作「`uname -r`」，并得到核心版本 3.10.0-1062.el7.x86_64
2. 在上述结果带入原指令，得到 `cd /lib/modules/3.10.0-1062.el7.x86_64/kernel/`

> **TIP**
>
> 为啥推荐`${}` 方式？方便识别 在复杂的变量引用中，没有分割符来分割非常的不方便识别

```bash
# 练习 7：取消刚刚设置的 name 变量内容
[mrcode@study kernel]$ unset name
[mrcode@study kernel]$ echo $name

[mrcode@study kernel]$ 

# 练习 8：输出 locate crontab 所找到的相关文件的权限
# locate 可以查找文件，并同时查看他们的文件权限
[mrcode@study kernel]$ locate crontab
/etc/anacrontab
/etc/crontab
/usr/bin/crontab
/usr/share/doc/man-pages-overrides-7.7.3/crontabs
/usr/share/doc/man-pages-overrides-7.7.3/crontabs/COPYING
/usr/share/man/man1/crontab.1.gz
/usr/share/man/man1p/crontab.1p.gz
/usr/share/man/man4/crontabs.4.gz
/usr/share/man/man5/anacrontab.5.gz
/usr/share/man/man5/crontab.5.gz
/usr/share/vim/vim74/syntax/crontab.vim

[mrcode@study kernel]$ ls -ld `locate crontab`
-rw-------. 1 root root   541 Aug  9 07:07 /etc/anacrontab
-rw-r--r--. 2 root root   451 Jun 10  2014 /etc/crontab
-rwsr-xr-x. 1 root root 57656 Aug  9 07:07 /usr/bin/crontab
# 这个是个目录，上面使用 -d 参数的效果就是，不输出该目录下的明细，只输出目录信息
drwxr-xr-x. 2 root root    21 Oct  4 18:25 /usr/share/doc/man-pages-overrides-7.7.3/crontabs
-rw-r--r--. 1 root root 17738 Aug  9 08:47 /usr/share/doc/man-pages-overrides-7.7.3/crontabs/COPYING
-rw-r--r--. 1 root root  2626 Aug  9 07:07 /usr/share/man/man1/crontab.1.gz
-rw-r--r--. 1 root root  4229 Jun 10  2014 /usr/share/man/man1p/crontab.1p.gz
-rw-r--r--. 1 root root  1121 Jun 10  2014 /usr/share/man/man4/crontabs.4.gz
-rw-r--r--. 1 root root  1658 Aug  9 07:07 /usr/share/man/man5/anacrontab.5.gz
-rw-r--r--. 1 root root  4980 Aug  9 07:07 /usr/share/man/man5/crontab.5.gz
-rw-r--r--. 1 root root  2566 Aug  9 11:17 /usr/share/vim/vim74/syntax/crontab.vim

# 练习 9：如何简化一条命令
# cd /cluster/server/work/taiwan_2015/003 假设这条命令是经常用到的，但是特别长，如何简化？
work="/cluster/server/work/taiwan_2015/003"
cd work
# 使用变量方式，来达成效果
# 该变量可以记录在 bash 的配置文件 「~/.bashrc」中，那么以后可随时使用 cd $work 进入该目录

```

## 环境变量的功能

环境变量可以帮我打到很多功能，如：家的目录变换、提示字符的显示、执行文件搜寻的路径等，可以使用 **env** 与 **export** 来查询当前 **shell** 环境中有多少默认的环境变量

### 用 env 观察环境变量与常见环境变量说明

```bash
[mrcode@study kernel]$ env
XDG_SESSION_ID=5			
HOSTNAME=study.centos.mrcode		# 主机名
SELINUX_ROLE_REQUESTED=
TERM=xterm											# 终端机使用的环境是什么类型
SHELL=/bin/bash									# 目前这个环境下，使用的 Shell 是哪一个程序？
HISTSIZE=1000										# 历史指令记录数量
SSH_CLIENT=192.168.0.105 53699 22
SELINUX_USE_CURRENT_RANGE=
QTDIR=/usr/lib64/qt-3.3
OLDPWD=/home/mrcode							# 上一个工作目录所在
QTINC=/usr/lib64/qt-3.3/include
SSH_TTY=/dev/pts/0
QT_GRAPHICSSYSTEM_CHECKED=1
USER=mrcode											# 使用者名称
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;...		# 颜色显示
MAIL=/var/spool/mail/mrcode
PATH=/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin:/home/dmtsai/bin
PWD=/lib/modules/3.10.0-1062.el7.x86_64/kernel #该用户目前所在的工作目录,使用 pwd 取出
LANG=en_US.UTF-8								# 语系设置
KDEDIRS=/usr
SELINUX_LEVEL_REQUESTED=
HISTCONTROL=ignoredups
SHLVL=1
HOME=/home/mrcode								# 登录用户家的目录
LOGNAME=mrcode									# 登录者登录的账户名称
QTLIB=/usr/lib64/qt-3.3/lib
XDG_DATA_DIRS=/home/mrcode/.local/share/flatpak/exports/share:/var/lib/flatpak/exports/share:/usr/local/share:/usr/share
SSH_CONNECTION=192.168.0.105 53699 192.168.0.128 22
LESSOPEN=||/usr/bin/lesspipe.sh %s
XDG_RUNTIME_DIR=/run/user/1000
QT_PLUGIN_PATH=/usr/lib64/kde4/plugins:/usr/lib/kde4/plugins
_=/usr/bin/env								# 上一次使用的指令最后一个参数（或指令本身）
```

**env** 是 **environment** 环境 的简写，上面列出来所有的环境变量，使用 **export** 也是一样的内容，只不过 **export** 还有其他额外的功能，上面这些变量的作用如下

- **HOME**

  代表用户的家目录。使用 `cd 或 cd ~` 也能回到自己的家，这个就是取用的 **HOME** 变量

- **SHELL**

  目前这个环境使用的 **SHELL** 是哪个程序，**Linux** 预设使用 **/bin/bash**

- **HISTSIZE**：历史命令可记录的总数量

- **MAIL**：使用 **mail** 指令收信时，系统会读取的邮件信箱文件（**mailbox**）

- **PATH**

  执行文件搜索的路径，目录与目录中间以冒号「:」分割，由于文件搜索是按 PATH 变量内的目录查询的，所以目录的顺序也很重要

- **LANG**

  语系信息，很多程序都会用到。比如，启动某些 perl 的程序语言文件，会主动分析语系数据文件，如果发现有他无法解析的编码语系，可能会产生错误

- **RANDOM**

  随机树生成器的变量，目前大多数 **distribution** 都会有随机数生成器，就是 `/dev/random` 文件。可以通过该随机数文件相关的变量（`$RANDOM`）来获取随机数值。

  在 **BASH** 环境下，该变量范围为 0~32767 之前

```bash
[mrcode@study kernel]$ echo $RANDOM
9229
# 想要 0 ~ 9 怎么办？
# 使用 declare 指令来让字符串转成计算公式 6593*10/32768 然后就能得到数值了
[mrcode@study kernel]$ declare -i number=$RANDOM*10/32768 ; echo $number
6
```

### 用 set 观察所有变量（含环境变量与自定义变量）

**bash** 不只有环境变量，还有一些与操作接口有关的变量，以及用户自己定义的变量存在。

```bash
# 使用 set，除了环境变量之外，还会将其他咋 bash 内的变量都显示出来
# 下面只是其中一部分重要的，内容太多
[mrcode@study kernel]$ set
BASH=/bin/bash		# bash 的主程序放置路径
# bash 的版本
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(2)-release'
COLUMNS=126				# 在目前终端机环境下，使用的字段有几个字符长度
HISTFILE=/home/mrcode/.bash_history # 历史命令记录文件
HISTFILESIZE=1000		# 上面那个文件能存储历史命令的数量
HISTFILE=1000			# 在目前环境中，内存中能记录的历史命令最大数量
IFS=$' \t\n'		# 预设的分隔符
LINES=20				# 目前的终端机下的最大行数
MACHTYPE=x86_64-redhat-linux-gnu	# 安装的机器类型
OSTYPE=linux-gnu		# 操作系统的类型
PS1=’[\u@\h \W]\$ ‘	# PS1 就厉害了，是命令提示字符，也就是我们常见的 [root@www ~]#、 [mrcode ~]$ 的设置，可以修改的
PS2=’> ‘		# 使用跳脱符号 \，在第二行开始显示的提示字符
$		# 目前这个 shell 使用的 PID
?		# 刚刚执行完指令的回传值
...
其他的请自行查阅


```

一般来说，无论是否为环境变量，只要跟我们这个 **shell** 的操作接口有关的变量，通常都会被设置为大写字符。也就是说，基本上，在 **Linux** 预设的情况中，使用`{大写的字母}`来设置的变量一般为系统内定需要的变量。上面的变量中有如下几个比较重要

#### PS1 提示字符的设置

命令提示字符，当我们每次按下 Enter 键去执行某个指令后，最后要再次出现提示字符时，就会主动去读取这个变数值了。相关设置可以通过 man bash 查询 PS1 的相关说明，下面列出一些符号含义：

- `\d`：可显示出「星期、月、日」的日期格式，如：「**Mon Feb 2**」
- `\H`：完整的主机名。如：本次练习机名称「**study.centos.mrcode**」.
- `\h`：仅取主机名第一个小数点之前的名字，如上面的则取「**study**」
- `\t`：显示时间，**24** 小时格式的 **HH:MM:SS**
- `\T`：显示时间，**12** 小时格式
- `\A`：显示时间，**24** 小时格式 **HH:MM**
- `\@`：显示时间，**12** 小时格式 **am/pm** 格式
- `\u`：目前使用者的账户名称，如 **mrcode**
- `\v`：**BASH** 的版本信息，如 **4.2.46(1)-release** 仅取「4.2」
- `\w`：完整的工作目录名称，由根目录写起的目录名称。但家目录会以 ~ 取代
- `\W`：利用 **basename** 函数取得工作目录名称，所以仅会列出最后一个目录名
- `\#`：下达的第几个指令
- `\$`：提示字符，如果是 **root** 时，则为 `#` ,否则就是 `$`

预设内容为 `[\u@\h \W]\$`，对照上表来看，`[mrcode@study ~]$` 这个为啥会显示成这样了

假设我们需要有类似如下的提示符号时，可以通过以下方式设置

```bash
# [mrcode@study /home/mrcode 16:50 #12]
[mrcode@study ~]$ cd /home/
[mrcode@study home]$  PS1='[\u@\h \w \A #\#]\$ '
[mrcode@study /home 02:26 #6]$ 
# 后面的 #6 信息，更新频率为 1 秒一次，输入一次命令算一次

```

#### `?`关于上个执行指令的回传值

在 **bash** 中该变量非常重要，表示「**上一个**执行的指令所**回传**的值」，当我们执行某些指令时，这些指令都会回传一个执行后的代码。一般来说，如果成功的执行该指令，则会回传一个 **0** 值，如果执行过程中发生错误，则会回传「错误代码」。简单说：非 **0** 则执行有错误

```bash
[mrcode@study /home 02:31 #13]$ echo $SHELL
/bin/bash				# 执行成功
[mrcode@study /home 02:31 #14]$ echo $?
0								# 显示 0
[mrcode@study /home 02:32 #15]$ 12name=mrcode
bash: 12name=mrcode: command not found...				# 执行失败
[mrcode@study /home 02:32 #16]$ echo $?
127							# 显示非 0
[mrcode@study /home 02:32 #17]$ echo $?
0								# 显示 0，? 只取代上一个命令的执行返回代码，不会累积，只能被使用一次


```

#### `OSTYPE、HOSTTYPE、MACHTYPE`主机硬件与核心的等级

在第 **0** 章中谈到过 **CPU** 等级，个人主机的 **CPU** 主要分为 **32/64** 位，其中 32 位又分为 i386、i586、i686 ，而 64 位则称为 **x86_64**。由于不同等级的 **CPU** 指令集不太相同，因此你的软件可能会针对某些 **CPU** 进行优化，以取得更佳的软件性能。所以软件就有 **i386** 、**x86_64** 之分了。

要留意的是，较高阶的硬件通常会向下兼容旧的软件，但较高阶的软件可能无法在旧机器上面安装

#### `export` 自定义变量转成环境变量

**evn** 与 **set** 表示环境变量与自定义变量，他们的差异在于「该变量是否会被子程序所继续引用」。

当你登录 **Linux** 并取得一个 **bash** 之后，你的 **bash** 就是一个独立的程序，这个程序的识别使用的是一个称为程序标识符（**PID**）。接下来你再这个 **bash** 下下达的任何指令都是由这个 **bash** 所衍生出来的，那些被下达的指令就被称为子程序了。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-bash-shell.jpg" style="zoom:67%;" />

如上，在原本的 **bash** 下执行另一个 **bash**，结果操作的环境接口会跑到第二个 **bash** 去（就是子程序），原本的 **bash** 就 **sleep** 了。整个指令运作的环境是实线的部分！若要回到原本的 **bash** 去，只有将第二个 bash 结束掉（exit 或 logout）才行。更多的程序概念后续讲解

因为**子程序仅会继承**父程序的**环境变量**，子程序**不会继承**父程序的**自定义变量**；这里就会出现在这种父子切换中可能一不小心就会出现找不到变量等的情况发生

可以使用 **export** 将自定义变量变成环境变量，那么子程序就会继承了。

```bash
export 变量名称
# 如果 export 后面不带任何值，则会显示所有的环境变量
```

## 影响显示结果的语系变量 locale

笔者在使用 **man** 命令等指令时，**mrcode** 和 **root** 账户一个显示英文，一个显示中文，使用 **locale** 查询如下

```bash
[mrcode@study /home 02:59 #20]$ locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
[mrcode@study /home 02:59 #21]$ su -
Password: 
Last login: Tue Oct 29 20:45:07 CST 2019 on pts/0
[root@study ~]# man bash
[root@study ~]# locale
LANG=zh_CN.UTF-8							# 主语言环境
LC_CTYPE="zh_CN.UTF-8"				# 字符（文字）编码
LC_NUMERIC="zh_CN.UTF-8"			# 数字系统
LC_TIME="zh_CN.UTF-8"					# 时间系统
LC_COLLATE="zh_CN.UTF-8"			# 字符串的比较与排序
LC_MONETARY="zh_CN.UTF-8"			# 货币显示
LC_MESSAGES="zh_CN.UTF-8"			# 信息显示内容，如菜单、错误信息等
LC_PAPER="zh_CN.UTF-8"
LC_NAME="zh_CN.UTF-8"
LC_ADDRESS="zh_CN.UTF-8"
LC_TELEPHONE="zh_CN.UTF-8"
LC_MEASUREMENT="zh_CN.UTF-8"
LC_IDENTIFICATION="zh_CN.UTF-8"
LC_ALL=											# 整体语系
[root@study ~]# 
# locale -a 可以显示 linux 主机内有的语系文件，文件放置在 /usr/lib/locale 

```

发现一个账号是 **en_US.UTF-8** 一个是 **zh_CN.UTF-8** ，以上可单独设置的变量有好多个，但是有 **LANG** 和 **LC_ALL** 是全局的，当其他都没有设置的时候，就会以这两个的语系为准

默认的语系配置文件在下面文件中

```bash
[root@study ~]# cat /etc/locale.conf 
LANG="zh_CN.UTF-8"

```

如果只是暂时在 tty 中显示指定的语系，而不是持久化的更改时，直接使用环境变量赋值方式

```bash
[mrcode@study /home 03:09 #22]$ LANG="zh_CN.UTF-8"
# 作者说在 Centos7 中可能需要使用如下的方式才可以
# 从 zh_TW.UTF-8 修改为 en_US.UTF8;
LANG=en_US.UTF8;
export LC_ALL=en_US.UTF8
```

## 变量的有效范围

在 **export** 指令中就提到了这个概念，如：父子变量不会被继承，需要使用 **export** 导出为环境变量。

某些书籍中会谈到全局变量（**global variable**）与局部变量（**local variable**），在本章：

- 环境变量 = 全局变量
- 自定义变量=局部变量

为啥环境变量的数据可以被子程序所引用呢？是因为内存配置的关系，理论上是这样的：

- 当启动一个 **shell**，操作系统会分配一块内存给 **shell** 使用，此内存变量可让子程序取用
- 若在父程序中利用 **export** 功能，可以让自定义变量的内容写到上述的区块中（环境变量）
- 当加载另一个 **shell** 时，子 **shell** 可以将父 **shell** 的环境变量所在的区块导入自己的环境变量区块中

但是需要注意的是：这里的环境变量与「**bash** 的操作环境」不太一样，如 **PS1** 并不是环境变量，可以看成是对 **bash** 程序的配置

## 变量键盘读取、数组与宣告：read、array、declare

上述的变量都是由指令直接设置的，可以让用户使用键盘输入，如某些程序执行过程中，会等待使用者输入 **yes/no** 之类的信息。

### read

交互式指令，阻塞等待用户输入信息。该指令在 **shell script** 中经常用到。关于 **script** 在 第十三章介绍

```bash
read [-pt] variable

选项与参数

-p：后面可以接提示符
-t：后面可以接等待的秒数
```

实践练习

```bash
# 范例 1：让用户由键盘输入一个内容，将该内容变成名为 atest 的变量
[mrcode@study ~]$ read atest
this is a test				# 光标闪烁，等待你的输入
[mrcode@study ~]$ echo ${atest}		# 这里打印刚刚用户输入的信息
this is a test

# 范例 2：提示使用者 30 秒内输入自己的大名，将该输入字符串作为名为 named 的变量内容
[mrcode@study ~]$ read -p "Please keyin your name: " -t 30 named
Please keyin your name: mrcode		# -p 的提示信息
[mrcode@study ~]$ echo ${named}
mrcode

# -t 30 ，如果 30 秒之后没有输入，则自动略过
```

### declare 、 typeset

**declare** 或 **typeset** 都是声明变量的类型。如果使用 **declare** 后面并没有接任何参数，那么 **bash** 会主动将所有变量名称与内容显示出来，就好像使用 **set** 一样。语法如下

```bash
declare [-aixr] variable

选项与参数

-a：将后面的 variable 的变量定义为数组 array 类型
-i：定义为整数数字 integer 类型
-x：用法与 export 一样，将后面的 variable 变成环境变量
+x：将环境变量变成普通的自定义变量
-r：将变量设置为 readonly 类型，该变量不可被更改内容，也不能 unset
-p：显示变量的定义和类型

```

实践与练习

```bash
# 范例 1 ：让变量 sum 进行 100 + 300 + 50 的加总结果
[mrcode@study ~]$ sum=100+300+50
[mrcode@study ~]$ echo ${sum}
100+300+50			# 发现没有生效，变成了字符串
# 使用 declare 声明后，成功
[mrcode@study ~]$ declare -i sum=100+300+50
[mrcode@study ~]$ echo ${sum}
450

```

在默认的情况下， **bash** 对于变量有几个基本的定义：

- 变量类型默认为字符串
- **bash** 环境中的数值运算，预设最多仅能达到整数形态，所以 **1/3** 结果是 **0**

```bash
# 范例 2：将 sum 变成环境变量
[mrcode@study ~]$ declare -x sum
[mrcode@study ~]$ export | grep sum    # 这里是在 export 指令的信息下搜索 sum
declare -ix sum="450"			# 看到使用了 declare -ix 来声明

# 范例 3：将 sum 变成只读属性，不可更动
[mrcode@study ~]$ declare -r sum
[mrcode@study ~]$ sum=123
-bash: sum: readonly variable

# 范例 4：将 sum 变成非环境变量的自定义变量
[mrcode@study ~]$ declare +x sum			# 将 - 变成 + 就是去掉环境变量
[mrcode@study ~]$ declare -p sum			# -p 显示某个变量的定义和类型
declare -ir sum="450"

```

**declare** 功能很有用，在 **shell script** 中经常使用。如果不小心将变量设置为「只读」，通常需要注销再登录才能复原该变量的类型

### array

废话不多说，笔者是个程序员，就不记录那么低级的概念

```bash
# 语法
var[index]=countent
```

实践与练习

```bash
# 范例：设置 var[1] ~ var[3] 的变量
[mrcode@study ~]$ var[1]="small min"
[mrcode@study ~]$ var[2]="big min"
[mrcode@study ~]$ var[3]="nice min"
[mrcode@study ~]$ echo "${var[1]},${var[2]},${var[3]}"
small min,big min,nice min
```

## 与文件系统及程序的限制关系：ulimit

想象一个状况：**Linux** 主机同时登陆了 **10** 个人，同时开启了 **100** 个文件，每个文件约 **10MBytes**，那么需要的内存则是 `10*100*10=100000MBytes=10GBytes`，耗费太多内存，系统很容易崩溃；为了预防这种情况，**bash** 可以「限制用户的某些系统资源」，包括可以开启的文件数量、**CPU** 可以使用的时间、可用内存总量等。

```bash
ulimit [-SHacdfltu] [配额]

```

选项与参数：

- **H**：**hard limit**，严格的设定，必定不能超过这个设定的数值

- **S**：**soft limit**，警告的设定，可以超过该设定值，超过则出现警告信息

  在设置上，通常 **soft** 会比 **hard** 小。比如：**soft=80**，**hard=100**，那么你可以使用到 **90**（因为没有超过 **100**）， 但是介于 **80 ~ 100** 之间，系统会有警告信息通知你

- **a**：后面不接任何选项与参数，可列出所有的限制额度

- **c**：限制每个核心文件的最大容量

  当某些程序发生错误时，系统可能会将该程序再内存中的信息写成文件（排除用），这种文件被称为核心文件（**core file**）。

- **f**：此 **shell** 可以建立的最大文件容量（一般可能设置为 **2GB**）单位为 **Kbytes**

- **d**：程序可使用最大断裂内存（**segment**）容量

- **l**：可用于锁定（**lock**）的内存量

- **t**：可使用最大 **CPU** 时（单位为妙）

- **u**：单一用户可以使用的最大程序（**process**）数量

实践与练习

```bash
# 范例 1：列出你目前身份（假设为一般账户）的所有限制数据值

[mrcode@study ~]$ ulimit -a
core file size          (blocks, -c) 0				# 只要为 0 则表示没有限制
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited		# 可建立的单一文件的大小
pending signals                 (-i) 4519
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024					# 同时可开启的文件数量
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 4096
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

# 范例 2：限制用户仅能建立 10MBytes 以下的容量文件
[mrcode@study ~]$ ulimit -f  10240
[mrcode@study ~]$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) 10240		# 已经修改为 10 MBytes
# 尝试创建大于 10MBytes 的文件，报错
[mrcode@study ~]$ dd if=/dev/zero of=123 bs=1M count=11
File size limit exceeded (core dumped)
# 记得删除 123 这个文件，同时需要注销再次登录才能让 10M 的限制消失
# 这里为什么需要删除？错误是报了，但是文件是生成了的，只是文件大小只有限制的 10M 大小

```

在第七章 **Linux** 磁盘文件系统中提到过，单一 **filesystem** 能够支持单一文件大小与 **block** 的大小有关系。但是文件系统的限制容量都允许太大了，可以使用 **ulimit -f** 来限制使用者建立的文件不要太大。

> **TIP**
>
> 此外，**ulimit** 除了重新登录账户外，还可以重新设置 **ulimit**，但是普通用户只能降低，而不能增加文件容量，
>
> 若想要管控使用者的 **ulimit** 限值，可以参考第十三章的 pam 介绍

## 变量内容的删除、取代与替换（Optional）

除了可以设置修改原本的内容外，还可以对变量进行微调，如删除、取代、替换

### 变量内容的删除

下面的范例以此进行，比较能理解到这里想表达的意思

```bash
# 范例 1：让小写的 path 自定义变量设置与 PATH 内容相同
[mrcode@study ~]$ path=${PATH}
[mrcode@study ~]$ echo ${path}
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin

# 范例 2：假设不喜欢 local/bin，所以将前一个目录删除掉(/usr/local/bin:)
# 这里的语法其实就是：将 符合 /*local/bin:(含) 之前的目录都删掉
[mrcode@study ~]$ echo ${path#/*local/bin:}
/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin

```

上面的语法示意解析如下

```bash
${变量#/*local/bin;}   

- ${} ：这种华括弧必须在，这种删除模式只有括弧起来才能识别
- 变量：原本的变量名称
- # ：代表从变量内容的最前面开始向右删除，且仅删除最短的那个
- /*local/bin：代表要删除的部分，由于 # 代表由前面开始删除，所以这里便由开始的 / 写起
	需要注意的是：还可以通过通配符 * 来取代 0 到无穷多个任意字符

```

```bash
# 范例 3：想要删除前面所有的目录，仅保留最后一个目录
[mrcode@study ~]$ echo ${path#/*:}
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
# # 表示删除前面最短的那一个，这里通配符了前面的一个路径，也就是那个 /usr/lib64/qt-3.3/bin: 被删除了

[mrcode@study ~]$ echo ${path##/*:}
/home/mrcode/bin
# 多增加了一个 # 字符，则只剩下最后一个路径了
```

在 **PATH** 变量中的内容都是以冒号「**:**」隔开的，所以要从头删除掉目录就是介于「**/**」到「**:**」之间，但是 **PATH** 中不止一个冒号，所以需要以 **#** 与 **##** 分表表示

- `#`：符合取代文字的「最短的」那一个
- `##`：符合取代文字的「最长的」那一个

`#` 是由后面往前删除内容，`%`则是由前往后删除内容

```bash
# 范例 4：假设你的 MAIL 变量是 /var/spool/mail/mrcode，只想要 mrcode 这个字符串，也就是前面的目录都不要了
[mrcode@study ~]$ echo ${MAIL}
/var/spool/mail/mrcode
[mrcode@study ~]$ echo ${MAIL##/*/}
mrcode

# 要删除目录，只要前面的
[mrcode@study ~]$ echo ${MAIL%/*}
/var/spool/mail

# 下面再来看看这两个到底是什么意思，前面的演示，笔者自己觉得没有明白是什么意思
# # 是从头开始匹配，下面演示是什么意思
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL#/*}
/var/spool/mail/mrcode
var/spool/mail/mrcode			# 匹配到的是 /,并且只匹配最短的那一个，那么最短的就是最前面的 /
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL##/*}
/var/spool/mail/mrcode	 
												# 被完全删掉了，/* 最常的就是整个字符串
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL##/*/}
/var/spool/mail/mrcode
mrcode									# 匹配到的是 /var/spool/mail/ 因为是匹配最常的结果，mrcode 后面没有没有 / 所以不会被匹配
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL##/*spool/}
/var/spool/mail/mrcode
mail/mrcode			# 匹配到的是 /var/spool/ ,按最常的匹配，这里唯一能匹配上的就是整个了

# 那么相反的是：% 是从尾部开始匹配
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL%/*}
/var/spool/mail/mrcode
/var/spool/mail			# 「%/*」匹配到的是 /mrcode ，并且是最短的这一个
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL%%/*}
/var/spool/mail/mrcode
										# 匹配了整个路径
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL%%/*/}
/var/spool/mail/mrcode
/var/spool/mail/mrcode			# 注意看这里，`%%/*/` /*/ 没有匹配上，是因为从 mrcode 开始匹配，然而 mrcode 后面没有 / 所以从开始就匹配不上了，所以没有删除成功
[mrcode@study ~]$ echo ${MAIL};echo ${MAIL%%/spool*}
/var/spool/mail/mrcode
/var				# 「%%/spool*」 * 代表了 /mail/mrcode 这一串，所以能匹配上


```

- `#`：从字符串头部往后匹配，匹配上则删除这一串，按最短匹配原则
- `##`：同上，按最长匹配原则
- `%`：从字符串尾部往前匹配，匹配上则删除这一串，按最短匹配原则
- `%%`：同上，按最常匹配原则

### 变量内容的替换

```bash
# 范例 1：将 path 的变量内容内的 sbin 替换成大写的 SBIN
[mrcode@study ~]$ echo ${path}; echo ${path/sbin/SBIN}
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/SBIN:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
[mrcode@study ~]$ echo ${path}; echo ${path/"local/sbin"/SBIN}
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/SBIN:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
# 看下上面的替换前和替换后的对比，这个就很好理解了

# 如果是两条斜线，则替换所有的
[mrcode@study ~]$ echo ${path}; echo ${path//sbin/SBIN}
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/SBIN:/usr/SBIN:/home/mrcode/.local/bin:/home/mrcode/bin

```

- `\`：替换首次出现的字符串为指定字符串; `${path\关键字\替换成目标字符串}`
- `\\`：替换所有匹配的字符串为指定字符串

### 变量的测试与内容替换

```bash
# 范例 1： 测试 username 变量，若不存在则给定默认内容为 root
[mrcode@study ~]$ echo ${username}
						# 可以看到没有内容输出
[mrcode@study ~]$ username=${username-root};			# 使用短横线设置默认值
[mrcode@study ~]$ echo ${username}
root
[mrcode@study ~]$ echo ${username-root2}
root					# 可以看到，当有值的时候，给定的默认值不会生效

# 范例 2：当内容为空串时，会出现什么？
[mrcode@study ~]$ username=""
[mrcode@study ~]$ username=${username-root}
[mrcode@study ~]$ echo ${username}
								# 可以看到，当内容为空串时，变量其实已经定义了，只是内存为空串
[mrcode@study ~]$ username=${username:-root}
[mrcode@study ~]$ echo ${username}
root						# 这里使用 : 来识别空串

```

除了以上的是否为空判定之外，还有其他的功能，总结如下

| 变量设置方式       | str 不存在         | str 为空字符串     | str 已存在且不为空字符串 |
| ------------------ | ------------------ | ------------------ | ------------------------ |
| `var=${str-expr}`  | var=expr           | var=               | var=$str                 |
| `var=${str:-expr}` | var=expr           | var=expr           | var=$str                 |
| `var=${str+expr}`  | var=               | var=expr           | var=expr                 |
| `var=${str:+expr}` | var=               | var=               | var=expr                 |
| `var=${str=expr}`  | str=expr; var=expr | str 不变；var=     | str 不变；var=$str       |
| `var=${str:=expr}` | str=expr; var=expr | str=expr; var=expr | str 不变；var=$str       |
| `var=${str?expr}`  | expr 输出至 stderr | var=               | var=$str                 |
| `var=${str:?expr}` | expr 输出至 stderr | expr 输出至 stderr | var=$str                 |

总结：`:` 冒号都是把空字符串识别为不存在，其他的按功能如下：

 

- `-`：不存在则给默认值，存在则使用原始值
- `+`：存在则给默认值，不存在不给值
- `=`：不存在则**改变变量值**，会影响原始变量的值；存在则使用原始值
- `?`：不存在则**报错**，存在则使用原始值

实践练习：

```bash
# 测试：- ，str 不存在
[mrcode@study ~]$ unset str; var=${str-newvar};
[mrcode@study ~]$ echo "var=${var}, str=${str}"
var=newvar, str=				# 因为 str 不存在，所以 var 被赋值了

# 测试：- ，str 存在
[mrcode@study ~]$ str="oldvar"; var=${str-newvar}; 
[mrcode@study ~]$ echo "var=${var}, str=${str}"
var=oldvar, str=oldvar	# 因为 str 存在，所以不赋值

# 测试：= 
[mrcode@study ~]$ unset str; var=${str?newvar};
-bash: str: newvar				# 不存在，直接报错
[mrcode@study ~]$ str="oldstr"; var=${str?newvar};
[mrcode@study ~]$ echo "var=${var}, str=${str}"
var=oldstr, str=oldstr		# 存在则使用存在的值，所以这个变量没有用

```

这里其实还是稍微有点难以理解，没有 `if else` 这样看的明白，追求极致代码简洁的情况下可以使用这种方式。

## 命令别名与历史命令

### 命令别名设置：alias、unalias

命令别名就是你可以把一长串指令指定一个简短的名称，在键入指令的时候使用简短的名称来达到调用一长串指令的目的。例如：`ls -al|more` 查看隐藏文档并且翻页查看，觉得这串指令太长了，可以通过下面的指令来设置别名

```bash
[mrcode@study ~]$ alias lm='ls -al | more'
[mrcode@study ~]$ lm
total 68
drwx------. 18 mrcode mrcode  4096 Nov 11 10:20 .
drwxr-xr-x.  4 root   root      42 Oct  8 23:01 ..
-rw-------.  1 mrcode mrcode 10279 Nov 11 14:12 .bash_history
-rw-r--r--.  1 mrcode mrcode    18 Aug  8 20:06 .bash_logout
-rw-r--r--.  1 mrcode mrcode   193 Aug  8 20:06 .bash_profile
```

别名的定义规则与变量定义规则几乎相同，另外可以取代已经存在的变量名

```bash
alias rm='rm -i'
```

**root** 可以移除（**rm**）任何数据，所以当使用 **rm** 的时候需要小心，可以使用上面的别名指令覆盖掉原始的 **rm** 指令，执行的时候就是执行 **rm -i** 指令了

```bash
[mrcode@study ~]$ alias
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias lm='ls -al | more'
alias ls='ls --color=auto'
alias rm='rm -i'
alias vi='vim'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

在 root 用户下是没有 `vi='vim'` 的，一般用户会默认添加该别名

想取消别名可以使用 **unalias** 指令

```bash
unalias lm
```

例题：**DOS** 年代，列出目录与文件用 **dir**，清除屏幕用 **cls**，在 **linux** 如何达到这个效果？

```bash
[mrcode@study ~]$ alias cls='clear'
[mrcode@study ~]$ alias dir='ls -l'
```

## 历史命令：history

前面提过 bash 有提供指令历史的服务，可以使用 history 来查询曾经下达过的指令

```bash
history [n]
history [-c]
history [-raw] histfiles

```

选项与参数：

- **n**：数字，列出最近 **n** 条命令
- **c**：将目前的 **shell** 中的所有 **history** 内容全部消除
- **a**：将目前新增的 **history** 指令新增如 **histfiles** 中，若没有加 **histfiles** 则预设写入 `~/.bash_history`
- **r**：将 **histfiles** 的内容读到目前这个 **shell** 的 **history** 记忆中
- **w**：将目前的 **history** 记忆内容写入 **histfiles** 中

实践与练习

```bash
# 范例 1：列出目前内存内的所有 history 记忆
[mrcode@study ~]$ history
    1  cd /root/
    2  sudo
    3  sudo cd /root/
    .... 中间省略
  666  alias 
  667  alias cls='clear'
  668  alias dir='ls -l'
  669  dir
  670  history

# 范例 2：列出们目前最近的 3 条指令
[mrcode@study ~]$ history 3
  669  dir
  670  history
  671  history 3

# 范例 3：like将目前的资料写入 histfile 中
[mrcode@study ~]$ history -w
# 默认情况会将记录记录写入 ~/.bash_history 中
[mrcode@study ~]$ echo ${HISTSIZE}
1000		# 这里不是现实文件中有多少条，而是最大可存储多少条


```

正常情况下历史命令的读取记录是这样的：

- 当以 **bash** 登录 **Linux** 主机后，系统会主动的由家目录的 `~/.bash_history` 读取
- 假设这次登录后，共下达过 **100** 次命令，等你注销时，系统就会将 1**01~1100** 总共 **1000** 条记录**更新**到 `~/.bash_history` 中，因为和能存储最大条数 **HISTSIZE** 有关系，前面的序号会增加，但是总存储条数只有 **HISTSIZE** 条
- 也可以使用 **history -w** 强制写入

**history** 指令不只是提供了查询历史记录而已，还可以利用相关命令来执行指令，如下

```bash
# 执行第几条命令，这里 number 是数值序号
[mrcode@study ~]$ !number
# 由最近的指令向前搜索指令开头为 command 的哪个指令，并执行
[mrcode@study ~]$ !command
# 执行上一个指令（相当于按 ↑ 后，再按 Enter）
[mrcode@study ~]$ !!

# 使用范例
[mrcode@study ~]$ history 4
  681  man rm
  682  alias
  683  man history
  684  history 4
[mrcode@study ~]$  !681		  # 执行第 681 条指令
 man rm			# 这里会显示具体执行的指令是什么
[mrcode@study ~]$ !!				# 执行上一个指令
 man rm
[mrcode@study ~]$ !al				# 从最新的历史指令开始搜索 al 开头的指令并执行他
alias

```

虽然好用，但是需要小心安全问题，尤其是 **root** 的历史记录，这是黑客的最爱。另外使用 **history** 配合 `!` 曾经使用过的指令下达是很有效率的一个指令下达方式

### 同一账户同时多次登录的 history 写入问题

常常会同时开几个 **bash** 窗口，这些 **bash** 的身份都是 **root**。这样会有 `~/.bash_history`的写入问题吗？

自动写入的条件是注销 **bash** 时，自动写入，那么最后一个被注销的 **bash** 窗口中的历史记录会存下来，如果记录大于了 **1000** 的话，后注销的会覆盖前面先注销的（会有同时注销的情况导致错乱的吗？书上没有说）

由于多重登录有这样的问题，很多朋友都习惯单一 **bash** 登录，再用后续要讲解的 「工作控制 job control 来切换不同的工作」，这样才能将所有曾经下达过的指令记录下来，也方便未来系统管理员进行指令的 **debug**

### 无法记录时间

history 有一个问题就是无法记录指令下达时间。按序号记录的，但是没有记录时间。如果有兴趣，其实可以通过 `~/.bash_logout` 来进行 **history** 的记录，并加上 **date** 来增加时间参数（后续的情景模拟题中会讲到怎么做）

> **TIP**
>
> 有一种情况就是，想不要别人翻阅你的历史记录的话，可以使用 **history** **-c**;**history -w** 强迫清除并立即写入文件来清空历史记录

---

## Bash Shell 的操作环境

在我们登陆主机的时候，屏幕上会有一些说明文字，告知我们的 Linux 版本之类的信息，还可以显示一些欢迎等信息。此外，我们习惯的环境变量、命令别名等，是否可以在登录后就主动帮我设置好？

这些设置又分为系统全局配置和个人账户级配置，仅是文件放置位置不同

### 路径与指令搜寻顺序

前面讲到过使用 **alias** 可以建立别名，比如创建了一个 **ls** 的别名，其实 **ls** 有少的指令，那么到底是哪一个会被选中执行呢？基本上，指令运行顺序可以这样看：

1. 以相对、绝对路径执行命令，例如 `/bin/ls` 或 `./ls`
2. 由 **alias** 找到该指令来执行
3. 由 **bash** 内置的指令来执行
4. 通过 `$PATH` 这个变量的顺序搜索到第一个指令执行

举例来说：

- `/bin/ls`：该指令运行后，没有颜色
- `ls`：该指令运行后输出的内容有颜色，因为是使用别名 `alias ls=‘ls --color=auto’`

也可以使用 `type -a ls` 来查询指令搜寻的顺序

```bash
# 范例：设置 echo 的命令别名为 echo -n，然后观察 echo 执行的顺序
[mrcode@study ~]$ alias echo='echo -n'
[mrcode@study ~]$ type -a echo
echo is aliased to `echo -n'
echo is a shell builtin
echo is /usr/bin/echo

```

可以看到上面的顺序与本节总结的执行顺序一致

### bash 的进站与欢迎信息：/etc/issue、/etc/motd

**进站信息 /etc/issue**

在 **tty1~tty6** 登录时，会有几行提示字符，这个就是进站画面，该字符串在 `/etc/issue` 中配置的

```bash
[mrcode@study ~]$ cat /etc/issue
\S
Kernel \r on an \m

```

如上的变量引用使用的是反斜杠，变量可以通过 **man issue** 中查看到 **agetty** ，再 **man agetty** 得到如下的信息，代码变量信息如下

- `\d`：本地端时间的日期
- `\l`：显示第几个终端机接口
- `\m`：显示硬件的等级（i386、i486、i586...）
- `\n`：显示主机的网络名称
- `\O`：显示 domain name
- `\r`：操作系统的版本（相当于 uname -r）
- `\t`：显示本地端时间的时间
- `\S`：操作系统的名称
- `\v`：操作系统的版本

```bash
# 练习：如果想在 tty3 的进站画面看到如下显示，该如何设置才能达到效果？

CentOS Linux 7 (Core)(terminal：tty3)
Date:2019-12-01 18:00:00
Kernel 3.10.0-229.e17.x86_64 on an x86_64
Welcome!

使用 root 身份参考上面的变量说明修改 /etc/issue 文件达成效果

vim /etc/issue

\S (terminal: \l)
Date: \d \t
Kernel \r on an \m
Welcome!


```

怎么登录 **tty** 和切换 **tty** 请参考之前的章节，记得，进站画面是切换到 **tty** 时顶部显示的信息，而不是登录后显示的信息。

该文件中的规则就是使用反斜杠引用上面的变量，其他的你可以随意操作，比如写个字符画等，搞得个性一点

当使用 **telnet** 登录主机时，是不会显示 `/etc/issue` 中的配置，而是显示 `/etc/issue.net` 中的配置

**欢迎信息 /etc/motd**

想要使用者登录后，取得一些信息，例如使用注意事项信息，就可以修改 **/etc/motd** 文件

```bash
[root@study ~]# vi /etc/motd 
Hello everyone,
这是欢迎信息中文测试

# 重新登录后会看到如下的信息
Last login: Sun Dec  1 17:37:58 2019 from 192.168.0.105
Hello everyone,
这是欢迎信息中文测试
[mrcode@study ~]$ 


```

### bash 的环境配置文件

我们一进入 **bash** 就取得了一堆有用的变量，这是因为系统有一些环境配置文件的存在，让 **bash** 在启动时直接读取这些配置文件，以规划好 **bash** 的操作环境。而这些配置文件分为全局系统配置和用户个人偏好配置

**login 与 non-login shell**

在介绍 **bash** 的配置文件前，一定要先知道 **login shell** 与 **non-login shell** ，重点就在于有没有登录（**login**）

- **login shell**：取得 **bash** 时需要完整的登录流程，就称为 **login shell**

  举例来说，你要由 **tty1~tty6** 登录，需要输入用户的账户与密码，此时取得的 **bash** 就称为「**login shell**」

- **non-login shell**：取得 **bash** 接口的方法不需要重复登录的举动

  比如：你以 **x window** 登录 **linux** 后，再以 **X** 的图形化接口启动终端机，此时该终端机并没有再次输入账户与密码，那么该 **bash** 的环境就称为 **non-login shell**

  再比如：你再原本的 **bash** 环境下再次下达 **bash** 这个指令，同样也没有输入账户密码，那第二个 **bash**（子程序）也是 **non-login shell**

上面两种情况取得的 **bash** 配置文件不一致。由于我们需要登录系统，所以先谈谈 **login shell** 会读取哪些配置文件？一般来说，**login shell** 其实只会读取这两个配置文件

1. /etc/profile：系统整体配置，最好不要修改这个文件
2. `~/.bash_profile` 或 `~/.bash_login` 或 `~/.profile`：属于使用者个人设置

### /etc/profile （login shell 才会读）

该文件相对于现在我们来看，可能还不太能看得懂，里面是利用使用者的标识符（**UID**）来决定很多重要的变量数据，这也是 **每个使用者登录取得 bash 时一定会读取的配置文件** ，也就是系统级全局配置，主要变量如下：

- **PATH**：会依据 **UID** 决定 **PATH** 变量要不要含有 **sbin** 的系统指令目录
- **MAIL**：依据账户设置好使用者的 **mailbox** 到 **/var/spool/mail/**账号名
- **USER**：根据用户的账户设置该变量类容
- **HOSTANME**：依据主机的 **hostname** 指令决定此变量内容
- **HISTSIZE**：历史命令记录数量。**CentOS 7.x** 设置为 **1000**
- **umask**：包括 **root** 默认为 **022** 而一般用户为 **002** 等

**/etc/profile** 可不止会做这些事情，还会呼叫外部的设置数据，在 CentOS 7.x 默认情况下，下面的数据会依序被呼叫进来：

**/etc/profile.d/*.sh**

通配符方式，加载该目录内所有的 **sh** 文件，另外，使用者需要具有 **r** 的权限，那么该文件就会被 **/etc/profile** 调用。

在 **CentOS 7.x** 中，该目录下的文件规范了 **bash** 操作窗口的颜色、语系、**ll** 与 **ls** 指令的命令别名、**vi** 的命令别名、**which** 的命令别名等。如果你需要帮所有使用者设置一些共享的命令别名时，可以在该目录下自行建立后缀为 **.sh** 的文件，并将所需要的数据加入即可

#### /etc/local.conf

该文件是由 `/etc/profile.d/lang.sh` 呼叫进来的，这也是我们决定 **bash** 预设使用何种语系的重要配置文件！文件里最重要的就是 **LANG**、**LC_ALL** 这些变量的设置，前面讨论过

**/usr/share/bash-completion/completions/***

tab 键补全，除了命令补齐、文档名补齐外，还可以进行指令的选项、参数补齐功能。就是从这个目录里面找到对应的指令来处理的。

该目录下的内容是由 **/etc/profile.d/bash_completion.sh** 文件载入的

`~/.bash_profile` （login shell 才会读）

bash 在读完了整体环境设置的 `/etc/profile` ，并借此加载其他配置文件后，接下来则是会读取使用者的个人配置文件。在 login shell 的 bash 环境中，所读取的个人偏好配置文件其实主要有 3 个，依序分别是：

1. `~/.bash_profile`
2. `~/bash_login`
3. `~/,profile`

其实 **bash** 的 **login shell** 设置只会读取上面三个文件中的一个，而读取的顺序则是依照上面的顺序。

什么意思呢？是当第一个文件不存在时，读取第二个，那么当第一个文件存在时，后面的都不读取了

为什么会有这么多的文件？是因为其他 **shell** 转换过来的使用者习惯不同，而做的兼容。

```bash
# 看看 mrcode 的 .bash_profile 的内容
# 具体路径为 /home/mrcode/.bash_profile 
[mrcode@study ~]$ cat ~/.bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then			# 判断并读取 ~/.bashrc
        . ~/.bashrc
fi

# User specific environment and startup programs
# 下面再处理个人化设置
PATH=$PATH:$HOME/.local/bin:$HOME/bin	

export PATH

```

该文件设置了 **PATH**，并使用 **export** 将 **PATH** 变成环境变量，看配置是通过累加方式将用户家目录下的 **~/bin/** 目录添加进 **PATH** 了，这就意味着，你可以将可执行文件放到 `~/bin/` 下，执行时，就不需要写全路径了

上面的文件内容中有一段 **if...then...** 代码，该代码后续再 **shell sript** 中讲解，这里判断 **~/.bashrc** 文件是否存在，存在则加载。

**bash** 配置文件的读入方式是通过 **source** 指令来读取的。这个后续讲解，最后来看看整个 **login shell** 的读取流程

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-login-shell.png" style="zoom:67%;" />

实线的方向是主线流程，虚线的方向则是被加载的配置文件。从上图来看，**CentOS** 的 **login shell** 环境下，最终被读取的配置文件是 `~/.bashrc` 文件，所以可以将自己的偏好设置写入该文件即可。

下面还要讨论 **source** 与 `~/.bashrc`

### source : 读取环境配置文件的指令

由于 `/etc/profile` 与 `~/.bash_profile` 都是在取得 **login shell** 的时候才会读取的配置文件，所以将自己的偏好设置写入上述文件后，通常都是需要注销后再登录，才会生效。可以使用 **source** 指令达到立即生效。

```bash
source 配置文件名
```

```bash
# 范例：将 家目录的 ~/.bashrc 的设置读入目前的 bash 环境中
[mrcode@study ~]$ source ~/.bashrc 
[mrcode@study ~]$ . ~/.bashrc 
# 使用 source 或则 小数点的语法 都能将内容读取到当前的 shell 环境中

```

**source** 还可以用于不同环境配置文件的场景中，比如，我的工作环境分为 **3** 个，那么需要分别编写属于 **3** 个项目的环境变量配置文件，当需要该环境时，直接使用 **source** 加载进来

`~/.bashrc` （**non-login shell** 会读）

在非登录情况下取得 **bash** 环境配置文件时，仅会读取 `~/.bashrc` 文件

```bash
[mrcode@study ~]$ cat ~/.bashrc 
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions

```

注意看，不同身份账户不同，这也解释了个人偏好配置文件是什么

```bash
[root@study ~]# cat ~/.bashrc 
# .bashrc

# User specific aliases and functions
# 使用者的个人设置
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Source global definitions
# 整体环境的设置
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

**CentOS 7.x** 中为什么会主动加载 **/etc/bashrc** 文件呢？是因为 **/etc/bashrc** 帮我们的 **bash** 定义出下面的数据：

1. 依据不同的 **UID** 规范出 **umask** 的值
2. 依据不同的 **UID** 规范出提示字符（就是 `PS1` 变量）
3. 加载 `/etc/profile.d/*.sh` 的设置

需要注意的是，**/etc/bashrc** 是 **CentOS** 特有的（**Red Hat** 系统），其他的 distribution 可能不是该名称。由于 `~/.bashrc`会加载 `/etc/bashrc` 和 `/etc/profile.d/*.sh` 所以，当你不小心删除了 `~/.bashrc` 那么这些都不能读取了，你的 **bash** 提示字符可能就变成下面这个样子了

```bash
-bash-4.2$

```

原因是，没有加载 **/etc/bashrc** 来规范 **PS1 d**的变量，这种情况也不会影响你的 **bash** 使用。可以复制 **/etc/skel/.bashrc** 文件复制到 **~/.bashrc** ，恢复回来

### 其他相关配置文件

事实上还有一些配置文件可能会影响到你的 bash 操作

**/etc/man_db.conf**

该文件对于系统管理员来说，是一个很重要的文件，它规范了使用 **man** 时， **man page** 的路径到哪里去寻找。

如果你是以 **tarball** 的方式来安装你的数据库，那么你的 **man page** 可能会放置在 `/usr/local/softpackage/man` 中，**softpackage** 是套件的名称，这个时候就需要手动将该路径加到 `/etc/man_db.conf` 中。否则 **man** 就会找不到相关的说明文档

`~/bash_history`

在讲解「历史命令」时提到过该文件，预设情况下，历史命令就记录在该文件中。每次登陆 **bash** 后，**bash** 会先读取这个文件，将所有的历史指令读入内存，因此，当我们登陆 **bash** 后就可以查知上次使用过哪些指令

`~/.bash_logout`

该文件则记录了：当我注销 **bash** 后，系统再帮我做完师门动作后才离开的意思。你可以读取下该文件的内容，预设情况下，注销时，**bash** 只是帮我们清掉屏幕的信息而已。

不过，你也可以将一些备份或则是其他你认为重要的工作写在这个文件中（如：清空暂存盘）

### 终端机的环境设置：stty、set

前面讲解过可以在 **tty1~tty6** 这 6 个文字终端机（**terminal**）环境中登录，登录的时候可以取得一些字符设置的功能。比如

- 使用退格键（删除键）来删除命令行上的字符
- **ctrl + c** 来强制终止一个指令的执行
- 当时呼入错误时，会有声音跑出来警告

以上功能都是在登录终端机时，自动获取终端机的输入环境设置实现的

事实上，目前我们使用的 **Linux distributions** 都帮我们制作了最棒的使用者环境了，但是在某些 **Unix like** 机器中，还是可能需要手动修改配置

```bash
# setting tty  
stty [-a]
参数 a：将目前所有的 stty 参数列出来

```

```bash
# 范例 1 ：列出所有的按键与按键内容
[root@study ~]# stty -a
speed 38400 baud; rows 19; columns 126; line = 0;
intr = ^C; quit = ^\; erase = ^?; kill = ^U; eof = ^D; eol = <undef>; eol2 = <undef>; swtch = <undef>; start = ^Q; stop = ^S;
susp = ^Z; rprnt = ^R; werase = ^W; lnext = ^V; flush = ^O; min = 1; time = 0;
-parenb -parodd -cmspar cs8 -hupcl -cstopb cread -clocal -crtscts
-ignbrk -brkint -ignpar -parmrk -inpck -istrip -inlcr -igncr icrnl ixon -ixoff -iuclc -ixany -imaxbel -iutf8
opost -olcuc -ocrnl onlcr -onocr -onlret -ofill -ofdel nl0 cr0 tab0 bs0 vt0 ff0
isig icanon iexten echo echoe echok -echonl -noflsh -xcase -tostop -echoprt echoctl echoke
# 以上特殊字符 ^ 表示 Ctrl，^C 表示 ctrl + c

```

下面是几个重要的含义：

- **intr**：送出一个 **interrupt** 中断信号给目前正在 run 的程序
- **quit**：送出一个 **quit** 信号给目前正在 **run** 的程序
- **erase**：向后删除字符
- **kill**：删除在目前指令列上的所有文字
- **eof**：**End of file** 的意思，代表「结束输入」
- **start**：在某个程序停止后，重新启动它的 **output**
- **stop**：停止目前屏幕的输出
- **susp**：送出一个 **terminal stop** 的喜好给正在 **run** 的程序

比如要设置 **ctrl + h** 来进行字符的删除

```bash
stty erase ^h
# 默认可以看到使用 ^? 但是实际测试的时候，改不回去了

```

```bash
错误操作问题：在 windows 下 ctrl + s 是保存功能，在 Linux 使用 vim 时，使用 ctrl + s 整个画面死锁，不能动了，是什么原因？

通过 stty -a 可以看到 ctrl + s 是 stop 功能，停止目前屏幕的输出了，恢复输出的话就是 start，ctrl + q

```

除了 stty 之外，bash 还有自己的一些终端机设置

```bash
set [-uvCHhmBx]
```

选项与参数：

- **u**：预设不启用。若启用后，当使用未设置变量时，会显示错误信息
- **v**：预设不启用。若启用后，在信息被输出前，会先显示信息的原始内容
- **x**：预设不启用。若启用后，在指令被执行前，会显示指令内容（前面有 ++ 符号）
- **h**：预设启用。与历史命令有关
- **H**：预设启用。与历史命令有关
- **m**：预设启用。与工作管理有关
- **B**：预设启用。与括号`[]` 的作用有关
- **C**：预设不启用。若使用 > 等，则若文件存在时，该文件不会被覆盖

```bash
# 范例 1： 显示目前所有的 set 设置
[mrcode@study ~]$ echo $-
himBH

# 范例 2：若使用未定义变量时，则显示错误信息
[mrcode@study ~]$ set -u  
[mrcode@study ~]$ echo $mrcode   
-bash: mrcode: unbound variable
[mrcode@study ~]$ set +u		# 关闭该功能使用 + 号
[mrcode@study ~]$ echo $mrcode

[mrcode@study ~]$ 

# 范例 3：执行前，显示该指令内容
[mrcode@study ~]$ set -x
++ printf '\033]0;%s@%s:%s\007' mrcode study '~'
[mrcode@study ~]$ echo ${HOME}
+ echo /home/mrcode
/home/mrcode
++ printf '\033]0;%s@%s:%s\007' mrcode study '~'

#要输出的指令都会被先打印到屏幕上，前面会多出 + 号

```

另外，还有其他的按键设置功能，前一小节提到的 **/etc/inputrc** 这个文件里面设置。还有例如 `/etc/DIR_COLORS* 与 /usr/share/terminfo/*` 等，也都是与终端机有关的环境配置文件。但是这里不建议修改 **tty** 的环境，因为 **bash** 的环境以及设置的很亲和了。

**bash** 默认的组合键汇总如下

| 组合按键 | 功能                                |
| -------- | ----------------------------------- |
| ctrl + c | 终止目前的命令                      |
| ctrl + D | 输入结束（EOF），例如邮件结束的时候 |
| ctrl + M | Enter                               |
| ctrl + S | 暂停屏幕的输出                      |
| ctrl + Q | 恢复屏幕的输出                      |
| ctrl + U | 在提示字符下，将整列命令删除        |
| ctrl + Z | 暂停 目前的命令                     |

### 通配符与特殊符号

在 **bash** 操作环境中，通配符（**wildcard**）是非常有用的，利用 **bash** 处理数据就更方便了。下面是一些常用的通配符：

| 符号  | 含义                                                         |
| ----- | ------------------------------------------------------------ |
| `*`   | 代表「0 个到无穷多个」任意字符                               |
| `?`   | 代表「一定有一个」任意字符                                   |
| `[]`  | 代表「一定由一个在括号内」的字符（非任意字符）。例如`[abcd]` 则表示一定由一个字符，可能是 a、b、c、d 中的任意一个 |
| `[-]` | 若有减号在括号中时，表示「在编码顺序内的所有字符」。例如`[0-9]`，表示 0~9 之前所有数字 |
| `[^]` | 若括号中的第一个字符为指数符号 ^，表示反向旋转，例如`[^abc]`，表示不包含 a、b、c |

实践练习

```bash
# 范例 1：找出 /etc 下一 cron 开头的文件名
[mrcode@study ~]$ ll -d /etc/cron*    # -d 仅显示目录
drwxr-xr-x. 2 root root  54 Oct  4 18:25 /etc/cron.d
drwxr-xr-x. 2 root root  57 Oct  4 18:25 /etc/cron.daily

# 范例 2：找出 etc 下刚好是 5 个字母的目录名
[mrcode@study ~]$ ll -d /etc/?????
drwxr-x---. 3 root root   83 Oct  4 18:38 /etc/audit
drwxr-xr-x. 4 root root   71 Oct  4 18:25 /etc/avahi

# 范例 3：找出 etc 下目录名含有数字的目录
[mrcode@study ~]$ ll -d /etc/*[0-9]*    # 记得通过 ** 来模糊匹配
drwxr-xr-x. 4 root root   78 Oct  4 18:22 /etc/dbus-1
-rw-r--r--. 1 root root 5725 Aug  6 21:44 /etc/DIR_COLORS.256color

# 范例 4：找出 etc 下，目录名开头不是小写的目录
[mrcode@study ~]$ ll -d /etc/[^a-z]*
ls: cannot access /etc/[^a-z]*: No such file or directory
# 看到没有找到不是小写的目录，换成非大写的，出来结果了
[mrcode@study ~]$ ll -d /etc/[^A-Z]*
drwxr-xr-x. 3 root root   101 Oct  4 18:23 /etc/abrt
-rw-r--r--. 1 root root    16 Oct  4 18:31 /etc/adjtime

# 范例 5：将范例 4 中找到的文件复制到 /tmp/upper 中
[mrcode@study ~]$ mkdir /tmp/upper; cp -a /etc/[^a-z]* /tmp/upper


```

除了通配符外，bash 环境中的特殊符号还有以下项，这里进行整理：

| 符号      | 含义                                                   |
| --------- | ------------------------------------------------------ |
| `#`       | 批注、注释符号                                         |
| `\`       | 跳脱符号、转义符号                                     |
| `|`       | 管线 **pipe**：分割两个管线命令的节点（后续介绍）      |
| `;`       | 连续指令下达分隔符：连续性命令的节点。与管线命令不相同 |
| `~`       | 用户的家目录                                           |
| `$`       | 取用变量前导符                                         |
| `&`       | 工作控制（**job control**）：将指令变成背景下工作      |
| `!`       | 逻辑运算意义上的「非」**not** 的意思                   |
| `/`       | 目录符号：路径分割的符号                               |
| `>`、`>>` | 数据流重导向：输出导向，分别是「覆盖」和「追加」       |
| `<`、`<<` | 数据流重导想：输入导向（下个章节讲解）                 |
| `''`      | 单引号，不具有变量替换功能，**$** 变为纯文本           |
| `""`      | 双引号，具有变量替换功能，**$** 可保留相关功能         |
| ``        | 两个 「`」中间为可以先执行的指令，也可以使用 `$()`     |
| `()`      | 在中间为 子 shell 的起始与结束                         |
| `{}`      | 在中间为命令区块的组合                                 |

以上是 **bash** 环境中常见的特殊符号整理，理论上，文件名尽量不要使用上述字符

## 数据流重导向

数据流重导向（**redirect**），将数据传导到其他地方去，将某个指令执行后应该要出现在屏幕上的数据，给传输到其他的地方。

例如文件或则是装置（打印机之类的），数据流重导向在 **Linux** 的文本模式下很重要，尤其是想要将某些数据存储下来时，就更有用了

### 什么是数据流重导向？

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-data-redirection.png)

执行一个指令时，这个指令可能会由文件读入资料，经过处理之后，再将数据输出到屏幕上。

- **standard output**：标准输出 **STDOUT**
- **standard error output**：标准错误输出 **STDERR**

**standard output 与 standard error output**

可以简单理解为：

- 标准输出：指令执行所回传的正确的信息
- 标准错误输出：指令执行失败后，所回传的错误信息

比如，我们的系统默认有 **/etc/crontab** 但无 **/etc/mrcode** ，此时若下达 `cat /etc/crontab /etc/mrcode` 指令时，cat 会执行：

- 标准输出：读取 **/etc/crontab** 后，将该文件内容显示到屏幕上
- 标准错误输出：因为无法找到 **/etc/mrcode** ，因此在屏幕上显示错误信息

可见不管正确或错误信息都输出到屏幕上，那么可以通过数据流重导向将 **stdout** 与 **stderr** 分别传送到其他文件或装置去，就达到了分别输出的目的，语法如下：

- 标准输入（**stdin** 简写）：代码为 **0**，使用 `<` 或 `<<`
- 标准输出（**stdout**）：代码为 **1**，使用 `>` 或 `>>`
- 标准错误输出（**stderr**）：代码为 **2**，使用 `2>` 或 `2>>`

为了理解 **stdout** 与 **stderr**，下面进行练习

```bash
# 范例 1：观察你的系统根目录 / 下各目录的文件名、权限与属性，并记录下来
[mrcode@study ~]$ ll / 			# 会把结果输出到屏幕
[mrcode@study ~]$ ll / > ~/rootfile		# 会吧结果输出到指定的 rootfile 文件中
[mrcode@study ~]$ ll ~/rootfile
-rw-rw-r--. 1 mrcode mrcode 1078 Dec  1 22:53 /home/mrcode/rootfile

```

上面的指令流程：

1. 该文件若不存在，系统会自动创建文件
2. 该文件若存在，那么会清空内容，再写入数据

标准输出和标准错误输出，单个符号**是覆盖**数据，2 个符号的是**追加**数据；

```bash
# 范例 2：利用一般身份账户查找 /home 下是否有 .bashrc 的文件存在
# 为了这个练习有效果，我在 abc 目录下用 root 身份创建了 .bashrc 空文件
# 并且，把 abc 目录的权限都改为只有 root 才能读取
[mrcode@study home]$ ll
total 4
drwx------.  2 root   root     32 Dec  1 23:01 abc
drwx------. 18 mrcode mrcode 4096 Dec  1 22:53 mrcode
[mrcode@study home]$ find /home/ -name .bashrc 			
/home/mrcode/.bashrc												# 标准输出
find: ‘/home/abc’: Permission denied				# 标准错误输出

# 使用数据流重导向指令，发现标准输出不显示在屏幕了
# 但是标准错误输出还显示在屏幕上
[mrcode@study home]$ find /home/ -name .bashrc > list
-bash: list: Permission denied			# 这里报错了，是因为 home 目录是普通用户是无法写数据的
# 写在 mrcode 自己的目录下
[mrcode@study home]$ find /home/ -name .bashrc > ./mrcode/list
find: ‘/home/abc’: Permission denied

# 范例 3： 使用标准输出 和 标准错误输出都输出到文件中
[mrcode@study home]$ find /home/ -name .bashrc > ./mrcode/list 2> ./mrcode/listerr

```

#### /dev/null 垃圾桶黑洞装置的特殊写法

就是可以将任何信息吃掉的黑洞装置

```bash
# 范例 4：将错误的数据丢弃，屏幕上显示正确的数据
[mrcode@study ~]$ find /home/ -name .bashrc 2> /dev/null
/home/mrcode/.bashrc

```

那么能否将正确和错误的数据都写到同一个文件呢？需要特殊的写法才行

```bash
# 范例 5：将指令的数据全部写入 list 文件中

# 错误的写法，可能会交叉写入该文件，数据错乱
[mrcode@study ~]$ find /home/ -name .bashrc > list 2> list

# 正确的写法
[mrcode@study ~]$ find /home/ -name .bashrc > list 2>&1
# 正确的写法
[mrcode@study ~]$ find /home/ -name .bashrc &> list

```

#### standard input ：`<` 与`<<`

简单来说：将原本需要由键盘输入的数据，该由文件内容来代替。

```bash
# 范例 6：利用 cat 指令来建立一个文件的简单流程
[mrcode@study ~]$ cat > catfile
testing
cat file test
# 这里使用快捷键 ctrl + d 来离开
```

上面使用 `cat > catfile` ，使用了数据流重导向，**catfile** 文件会被建立，内容是需要键盘输入，也就是上面的两行内容。这里可以使用标准输入来取代键盘的敲击

```bash
# 范例 7：用 stdin 代替键盘输入，建立新文件的简单流程
[mrcode@study ~]$ cat > catfile < ~/.bashrc
[mrcode@study ~]$ ll catfile ~/.bashrc
-rw-rw-r--. 1 mrcode mrcode 231 Dec  1 23:28 catfile
-rw-r--r--. 1 mrcode mrcode 231 Aug  8 20:06 /home/mrcode/.bashrc
# 大小一模一样，几乎像是用 cp 来复制一样
```

而 `<<` 表示接受的输入字符。比如：我要用 **cat** 直接将输入的信息输出到 **catfile** 中，且当由键盘输入 **eof** 时，该次输入就结束

```bash
[mrcode@study ~]$ cat > catfile << 'eof'
> This is a test
> Ok new stop
> eof
[mrcode@study ~]$ cat catfile
This is a test
Ok new stop
# 只有两行数据，不会存在关键词一行
# 这里就有点类似判定结束标准输入的功能

```

`<<`可以代替快捷键 **ctrl + d**，来终止输入，那为什么要使用命令输出重导向呢？

- 屏幕输出的信息很重要，而且我们需要将它存下来的时候
- 背景执行的程序，不希望他干扰屏幕正常的输出结果的时候
- 一些系统的例行命令（例如在 **/etc/crontab** 中的文件）的执行结果，希望他可以存下来时
- 一些执行命令可能已知错误信息时，想以`2>/dev/null`丢弃时
- 错误信息与正确信息需要分别输出时

当然还有其他的使用场景，最简单的就是网友们经常问到：为何我的 **root** 都会受到系统 **crontab** 寄来的错误信息呢？这个是场景的错误，而如果我们已经知道这个错误信息是可以忽略的时，`2> errorfile` 这个功能就很重要了吧

```bash
# 问：假设要将 echo `error message` 以 standard error output 的格式来输出，怎么做？
答：既然有 2>&1 来将 2> 转到 1> 去，
   那么就应该有 1>&2，可以这样做

[mrcode@study ~]$ echo 'error message' 1>&2
error message
[mrcode@study ~]$ echo 'error message' 2> /dev/null 1>&2


```

### 命令执行的判断依据：`;`、`&&`、`||`

很多指令想要一次输入去执行，而不想分此执行，基本上有两种方法：

- 第十二章要介绍的 **shell script** 脚本执行
- 通过本章的知识点来完成

#### `cmd;cmd` 不考虑指令相关性的连续指令下达

比如子关机的时候希望可以执行两次 **sync** 同步写入磁盘后，再 **shutdown** 计算机

```bash
sync; sync; shutdown -h now
```

这个是两个指令之前没有关系的执行，前一个执行完成后，就执行后一个；如果是这样的情况：在某个目录下创建文件，如果目录存在，则创建文件，如果不存在则不做任何操作，该指令就无法完成了

### `$?`(指令回传值)与 `&&` 和 `||`

前面章节讲到过指令回传值：若前一个指令执行的结果为正确，在 Linux 下会回传一个 `$?=0` 的值。可以通过判断这个值来是否执行后面的指令

逻辑操作符这里就不过多解释了

- `&&`：前一个执行正确，后面才会执行
- `||`：前一个执行正确，后面的不会执行

```bash
# 范例 1：使用 ls 查阅 目录 /tmp/abc 是否存在，若存在则用 touch 建立 /tmp/abc/hehe
[mrcode@study ~]$ ls /tmp/abc && touch /tmp/abc/hehe
ls: cannot access /tmp/abc: No such file or directory
# 只有 ls 报错了，后续的指令没有报错，说明没有被执行

[mrcode@study ~]$ mkdir /tmp/abc
[mrcode@study ~]$ ls /tmp/abc && touch /tmp/abc/hehe
[mrcode@study ~]$ ll /tmp/abc/
total 0
-rw-rw-r--. 1 mrcode mrcode 0 Dec  2 00:22 hehe

# 范例 2：测试 /tmp/abc 是否存在，若不存在则建立该目录，若存在则不做操作
[mrcode@study ~]$ rm -r /tmp/abc/

[mrcode@study ~]$ ls /tmp/abc || mkdir /tmp/abc
ls: cannot access /tmp/abc: No such file or directory
# 报错没有找到目录，后面没有报错，但是查看缺创建了，证明执行了

[mrcode@study ~]$ ll -d /tmp/abc/
drwxrwxr-x. 2 mrcode mrcode 6 Dec  2 00:24 /tmp/abc/

# 而下面这个没有报错，后面也没有报错，说明只执行了前面的指令
[mrcode@study ~]$ ls /tmp/abc || mkdir /tmp/abc


# 范例 3：我不清楚 /tmp/abc 是否存在，但就是要建立 /tmp/abc/hehe 文件
[mrcode@study ~]$ ls /tmp/abc || mkdir /tmp/abc/ && touch /tmp/abc/hehe

    
```

范例三，对于的表达式对于 java 或则 js 来说，理解不太一样，如下分析：

- 第一种情况：**/tmp/abc** 不存在
  1. **ls /tmp/abc** 回传 `$?≠0`，结果为 **false**
  2. 则执行创建操作，由于会成功，故 `$?=0`，结果为 **true**
  3. 则执行创建 **hehe** 文件
- 第二种情况：**/tmp/abc** 存在
  1. **ls /tmp/abc** 回传 `$?=0`，结果为 **true**
  2. `||` 遇到 **true** 后面的不会执行，**但是** 结果会往后传递
  3. 前一个结果为 **true**，那么就执行创建

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/78c30e17cd2b0dc155dfc4a784b491a3.png" style="zoom:67%;" />

只要注意：linux 指令是从左往右执行的，只有相邻的指令会被特殊符号阻断

```bash
## 例题：以 ls 测试 /tmp/mrcode 是否存在，存在则显示 exist, 不存在则显示 not exist
[mrcode@study ~]$ ls /tmp/mrcode && echo 'exist' || echo 'not exist'
ls: cannot access /tmp/mrcode: No such file or directory
not exist

[mrcode@study ~]$ mkdir /tmp/mrcode
[mrcode@study ~]$ ls /tmp/mrcode && echo 'exist' || echo 'not exist'
exist
# 可以看到的确只有相邻的两个指令会被阻断

# 如果搞不清楚他们的逻辑的话，就会出现下面这种情况
[mrcode@study ~]$ ls /tmp/mrcode || echo 'exist' && echo 'not exist'
ls: cannot access /tmp/mrcode: No such file or directory
exist
not exist
# 两种都出现了，不存在，则执行 exist，后面并且关系，再次执行
[mrcode@study ~]$ mkdir /tmp/mrcode
[mrcode@study ~]$ ls /tmp/mrcode || echo 'exist' && echo 'not exist'
not exist
# 存在，则不执行 exit，true 往后传递，则执行 not exist

```

只要注意：**linux** 指令是从左往右执行的，只有相邻的指令会被特殊符号阻断

```bash
## 例题：以 ls 测试 /tmp/mrcode 是否存在，存在则显示 exist, 不存在则显示 not exist
[mrcode@study ~]$ ls /tmp/mrcode && echo 'exist' || echo 'not exist'
ls: cannot access /tmp/mrcode: No such file or directory
not exist

[mrcode@study ~]$ mkdir /tmp/mrcode
[mrcode@study ~]$ ls /tmp/mrcode && echo 'exist' || echo 'not exist'
exist
# 可以看到的确只有相邻的两个指令会被阻断

# 如果搞不清楚他们的逻辑的话，就会出现下面这种情况
[mrcode@study ~]$ ls /tmp/mrcode || echo 'exist' && echo 'not exist'
ls: cannot access /tmp/mrcode: No such file or directory
exist
not exist
# 两种都出现了，不存在，则执行 exist，后面并且关系，再次执行
[mrcode@study ~]$ mkdir /tmp/mrcode
[mrcode@study ~]$ ls /tmp/mrcode || echo 'exist' && echo 'not exist'
not exist
# 存在，则不执行 exit，true 往后传递，则执行 not exist

```

## 管线命令（pipe）

**bash** 命令执行的时候有输出数据，如果这群数据必须经过几道手续之后才能得到我们想要的格式，这就可以使用管线命令（**pipe**）来完成了

假设我们想知道 `/etc/` 下有多少文件，可以使用 `ls /etc/` 来查询，不过因为文件太多了，输出占满整个屏幕，导致最开始是什么文件看不到了，这就可以通过管线命令结合 **less** 指令来达成

```bash
[mrcode@study ~]$ ls -al | less

```

如此一来， `ls -al` 指令输出后的内容，能够被 **less** 读取，并且利用 **less** 的功能，可以前后翻动相关信息

管线命令**仅能处理**由前一个指令传来的**正确信息**（standard output），对于 **standard error** 没有直接处理的能力，整体管线命令可以使用下图表示

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/ev6qfqexa2.png" style="zoom:67%;" />

在每个管线后面接的第一个数据必定是「指令」，而且这个指令必须能接受 **standard input** 的数据才可以，这样的指令则是「管线命令」，例如 **less**、**more**、**head**、**tail** 等都是可以接受 **standard input** 的管线命令。而 **ls**、**cp**、**mv** 等就不是管线命令了，因为他们不不会接受来自 **stdin** 的数据。管线命令主要有两个比较需要注意的地方：

- 管线命令仅会处理 **standard output** ，对于 **standard error output** 会忽略
- 管线命令必须要能接受来自前一个指令的数据成为 **standard input** 继续处理才行

如果硬要 **standard error** 可以被管线命令所使用可以使用如下方式

那么下面来玩一些管线命令，以下知识点对系统管理费用有用

### 截取命令 cut、grep

简单说：将一段时间经过分析后，取出我们想要的。或则是经过由分析关键词，取得我们所想要的那一行。一般来说，截取信息通常是针对一行一行来分析的。

#### cut

```bash
cut -d '分割字符' -f fields  # 用于有特定分割字符
cut -c 字符区间							 # 用于排列整齐的信息

```

选项与参数：

- **d**：后面接分割字符。与 `-f` 一起使用
- **f**：依据 **-d** 的分割字符将一段信息分区成数段，用 -f 取出第几段的意思
- **c**：以字符（**characters**）的单位取出固定字符区间

```bash
# 范例 1：将 PATH 变量取出，我要找出第 5 个路径
[mrcode@study ~]$ echo ${PATH}
/usr/lib64/qt-3.3/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/mrcode/.local/bin:/home/mrcode/bin
# 数量是从 1 开始，不是从 0 哟
[mrcode@study ~]$ echo ${PATH} | cut -d ':' -f 5
/usr/sbin

# 取出第 5 个和第 6 个
[mrcode@study ~]$ echo ${PATH} | cut -d ':' -f 5,6
/usr/sbin:/home/mrcode/.local/bin


# 范例 2 ：将 export 输出的信息，取得第 12 字符以后的所有字符串
[mrcode@study ~]$ export
declare -x HISTCONTROL="ignoredups"
declare -x HISTSIZE="1000"
declare -x HOME="/home/mrcode"
declare -x HOSTNAME="study.centos.mrcode"
...

# 以上数据每个都是排列整齐的，有着 declare -x 前缀
# 那么想要把前缀去掉，就可以这样做
[mrcode@study ~]$ export | cut -c 12-
HISTCONTROL="ignoredups"
HISTSIZE="1000"
HOME="/home/mrcode"
# 使用 12-15 则是截取出这个区间的字符
# 使用 12 则只截取 12 这个字符

# 范例 3 ：用 last 将显示的登陆者信息，仅留下用户名
[mrcode@study ~]$ last
# 账户 		终端机					登录 IP					日期时间						
mrcode   pts/1        192.168.0.105    Mon Dec  2 01:25   still logged in   
mrcode   pts/0        192.168.0.105    Mon Dec  2 01:25   still logged in   
mrcode   pts/1        192.168.0.105    Mon Dec  2 00:21 - 01:12  (00:51)  
# 用空格分隔的数据，那么可以这样做
[mrcode@study ~]$ last | cut -d ' ' -f 1
mrcode
mrcode
mrcode
# 其实 账户和终端机之间的空格有好几个，并不是一个所以使用下面的命令并不能把 终端机一列也提取出来
last | cut -d ' ' -f 1,2

```

**cut** 主要的用途：将同一行里面的数据进行分解

常使用在分析一些数据或文字数据的时候，因为有时候会以某些字符当做分区的参数，然后将数据切割，以取得我们所需要的数据，作者常常在分析 **log** 文件的时候，但是 **cut** 在处理多空格相连的数据时，就比较麻烦，所以某些常见可能需要使用下一章节要讲解的 **awk** 来取代

#### grep

**cut** 是将一行信息中，取出某部分我们想要的数据，而 **grep** 则是分析一堆信息，若一行当中有匹配的数据，则将这一行数据拿出来

```bash
grep [-acinv] [--color=auto] '搜索的字符串' filename
    
```

选项与参数：

- **a**：将 **binary** 文件以 **text** 文件的方式搜索数据
- **c**：计算找到「搜索字符」的次数
- **i**：忽略大小写
- **n**：输出行号
- **v**：反向选择，显示出没有搜索字符串的那一行数据
- `--color`：可以将找到的关键词部分加上颜色显示

```bash
# 范例 1：将 last 中，有出现 root 的那一行找出来
[mrcode@study ~]$ last | grep 'root'
root     tty3                          Sun Oct  6 23:16 - crash  (22:40)    
root     tty4                          Fri Oct  4 22:48 - 22:48  (00:00)  
# 会发现 root 被高亮颜色了，我们时候 type 命令查看，发现被自动加上了 color 参数
[mrcode@study ~]$ type grep
grep is aliased to 'grep --color=auto'

# 范例 2：与 范例 1 相反，不要 root 的数据
[mrcode@study ~]$ last | grep -v 'root'
mrcode   pts/1        192.168.0.105    Mon Dec  2 01:25   still logged in   
mrcode   pts/0        192.168.0.105    Mon Dec  2 01:25   still logged in   
mrcode   pts/1        192.168.0.105    Mon Dec  2 00:21 - 01:12  (00:51)
reboot   system boot  3.10.0-1062.el7. Fri Oct  4 18:47 - 03:43  (08:56)

# 范例 3：在 last 的输出信息中，只要有 root 就取出，并且只取第一栏
# 结合 cut 命令取出第一栏
[mrcode@study ~]$ last | grep 'root' | cut -d ' ' -f 1
root
root

# 范例 4：取出 /etc/man_db.conf 内涵 MANPATH 的那几行
[mrcode@study ~]$ grep 'MANPATH' /etc/man_db.conf
# MANDATORY_MANPATH                     manpath_element
# MANPATH_MAP           path_element    manpath_element


```

**grep** 支持的语法很多，用在正规表示法里，能够处理的数据太多。但是这里先不了解正规表示法，下一章再来讲解

这里只需要了解下，**grep** 可以解析一行文字，取得关键词，若改行有存在关键词，就会整行取出来

### 排序命令：sort、wc、uniq

#### sort

可以依据不同的数据形态来排序。例如数字与文字的排序不一样，另外，排序的字符与语系的编码有关，因此，如果需要排序时，建议使用 LANG=C 来让语系统一，数据排序比较好一些

```bash
sort [-fbMnrtuk] [file or stdin]

```

选项与参数：

- **f**：忽略大小写的差异
- **b**：忽略最前面的空格符
- **M**：以月份的名字来排序，例如 **JAN**、**DEC** 等排序方法
- **n**：使用纯数字进行排序，默认是以文字形态来排序
- **r**：反向排序
- **u**：**uniq**，相同的数据中，仅出现一行代表，也就是去重
- **t**：分隔符，预设使用 「**tab**」来分割
- **k**：以那个区间（**field**）来进行排序

``` bash
# 范例 1：个人账户都记录在 /etc/passwd 下，将账户进行排序
[mrcode@study ~]$ cat /etc/passwd | sort
abrt:x:173:173::/etc/abrt:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
bin:x:1:1:bin:/bin:/sbin/nologin
chrony:x:993:990::/var/lib/chrony:/sbin/nologin
# 可以看到按字符排序了

# 范例 2：/etc/passwd 内容是以 : 来分割的，想使用第三栏进行排序
[mrcode@study ~]$ cat /etc/passwd | sort -t ':' -k 3
root:x:0:0:root:/root:/bin/bash
mrcode:x:1000:1000:mrcode:/home/mrcode:/bin/bash
qemu:x:107:107:qemu user:/:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
# 第三栏是数字，但是这里并没有按数字大小来排序，因为默认使用文字排序
# 与数值大小进行排序
[mrcode@study ~]$ cat /etc/passwd | sort -t ':' -k 3 -n
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin

# 范例 3：利用 last ，将输出的数据仅取账户，并排序
[mrcode@study ~]$ last | cut -d ' ' -f 1 | sort

mrcode
mrcode


```

### uniq

```bash
uniq [-ic]
- i：忽略大小写
- c：进行计数

```

实践练习

```bash
# 范例 1： 使用 last 将账户列出，仅取出账户，排序后去重
[mrcode@study ~]$ last | cut -d ' ' -f 1 | sort | uniq

mrcode
reboot
root
wtmp

# 范例 2：以上题，统计每个账户登录的总次数
[mrcode@study ~]$ last | cut -d ' ' -f 1 | sort | uniq -c
      1
    136 mrcode
     19 reboot
      2 root
      1 wtmp

# 第一行和 wtmp 是 last 的默认字符，可以忽略
```

### wc

wc 可以计算输出的信息。比如：/etc/man_db.conf 这个文件里面有多少字？多少行？

```bash
wc [-lwm]

-l：仅列出行
-w：仅列出多少字（英文单字）
-m：多少字符
    
```

```bash
# 范例 1：/etc/man_db.conf 这个文件里面有多少字
[mrcode@study ~]$ cat  /etc/man_db.conf | wc
	   行     字数	   字符数
    131     723    5171

# 范例 2：last 可以输出登陆者，但是 last 最后两行并非账户内容，那么该如何以一行指令取得登录系统的总人次？
last | grep [a-zA-Z] | grep -v 'wtmp' | grep -v 'reboot' | grep -v 'unknown' | wc -l
138
# grep 正则匹配，排除了非英文字符的账户
# grep -v 反向选择，相当于排除了指定的账户
# 最后使用 wc 统计行数

 
```

### 双向重导向：tee

前一节讲解到 `>` 会将数据流整个栓送给文件或装置，因此除非去读取该文件或装置，那么如果想要将整个暑假流的处理过程中将某段信息存下来该怎么做？就可以使用 **tree**

```bash
Standard input   ------> tee --------> Screen
												  ↓
												 file
# 流程如上
```

**tee** 会同时将数据流分送到文件与屏幕，而输出到屏幕的其实就是 **stdout**，那么就可以让指令继续处理

```bash
tee [-a] file
- a：以累加（append）的方式，将数据加入 file 中

```

```bash
# 将 last 内容输出到 last.list 文件中，并继续处理
[mrcode@study ~]$ last | tee last.list | cut -d " " -f 1

# 将 ls 数据存一分到 ~/homefile 同时屏幕也输出信息
[mrcode@study ~]$ ls -l /home/ | tee ~/hoefile | more

```

### 字符转换命令：tr、col、join、paste、expand

在 **vim** 程序编辑器中提到过 **DOS** 换行符与 **Unix** 不一样，并且可以使用 **dos2unix** 与 **unix2dos** 来完成转换。

那么思考下，是否还有其他的字符转换命令，比如：将大写改成小写、将数据中的 **tab** 转成空格、如何将两篇信息整合成一篇？

#### tr：正则替换或删除字符

**tr** 可以用来删除一段信息中的文字，或则是进行文字信息的替换

```bash
tr [-ds] SET1 ...

-d：删除信息当中的 SET1 这个字符串
-s：替换重复的字符

```

```bash
# 范例 1：将 last 输出的信息中，原有的小写变成大写字符
[mrcode@study ~]$ last | tr '[a-z]' '[A-Z]'
MRCODE   PTS/1        192.168.0.105    MON DEC  2 07:00   STILL LOGGED IN

# 范例 2：将 /etc/passwd 输出的信息中，将冒号 : 删除
[mrcode@study ~]$ cat /etc/passwd | tr -d ':'
rootx00root/root/bin/bash

# 范例 3：将 /etc/passwd 转成 dos 换行到 ~/passwd 中，再将 ^M 符号删除
# 由于我这里没有安装 unix2dos 这里无法实际演示
cp /etc/passwd ~/passwd && unix2dos ~/passwd
file /etc/passwd ~/passwd
cat ~/passwd | tr -d '\r' > ~/passwd.linux
# \r 是 dos 的换行符
ll /etc/passwd ~/passwd*
# 就会发现处理之后和源文件一样大小了
# 本例子是：将 unix 转成 dos，/n 转成了 /r/n ，然后使用 tr 命令将 /r 删除了，相当于又还原了

#那么经过上面的分析之后，其实转换程序就是转换了换行符，那么可以利用 tr 手动来完成转换
[mrcode@study ~]$ cp /etc/passwd ~/passwd
[mrcode@study ~]$ file /etc/passwd ~/passwd
/etc/passwd:         ASCII text
/home/mrcode/passwd: ASCII text
# 将 unix 换行符 \n 替换成 dos 换行符 \r\n
[mrcode@study ~]$ cat passwd | tr '\n' '\r\n' > passwd.dos
[mrcode@study ~]$ file passwd*
passwd:     ASCII text
passwd.dos: ASCII text, with CR line terminators		# 可以看到已经变了
# 再将 \r 删掉
[mrcode@study ~]$ cat passwd | tr -d '\r' > passwd.linux
[mrcode@study ~]$ file passwd*
passwd:       ASCII text
passwd.dos:   ASCII text, with CR line terminators
passwd.linux: ASCII text


```

该指令也可以写在正规表示法里面，因为他也是由正规表示法的方式来取代数据的，比如上面使用 `[]` 来设置字符，通常用来取代文件中的怪异符号。

#### col：将 tab 转换成对等的空格

```bash
col [-xb]

-x:将 tab 键转换成对等的空格键

```

```bash
# 范例 ： 利用 cat -A 显示出所有的特殊按键，最后以 col 将 tab 转成空白
[mrcode@study ~]$ cat -A /etc/man_db.conf
MANDATORY_MANPATH^I^I^Imanpath_element$		# ^I 的符号就是 tab

[mrcode@study ~]$ cat /etc/man_db.conf | col -x | cat -A | more
MANDATORY_MANPATH                      /usr/src/pvm3/man$
    
```

虽然 **col** 有特殊的用途，但是很多时候可以用来简单的将 **tab** 取代为空格键，并且可以取代会对等宽度的空格

#### join：合并两个文件中相同行的数据

```bash
join [-ti12] file1 file2
    
```

选项与参数：

- **t**：**join** 默认以空格符分割数据，并且比对「第一个字段」的数据，如果两个文件相同，则将两笔数据连城一行，且第一个字段放在第一个
- **i**：忽略大小写
- **1**：数值 **1**，代表「第一个文件要用哪个字段来分析」
- **2**：数值 **2**，代表「第二个文件要用哪个字段来分析」

```bash
# 范例 1：用 root 身份，将 /etc/passwd 与 /etc/shadow 相关数据整合成一栏
[root@study ~]# head -n 3 /etc/passwd /etc/shadow
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

==> /etc/shadow <==
root:$6$oTg/fYGfv9/GIl6h$UEcmYlRZacV757rHtXlvmu5xH5TWGfqd3eDOEotB3CAc5mcW5UEoMTSg0pDICd/sYGrEScsHQY9tYZY0FGkKS1::0:99999:7:::
bin:*:17834:0:99999:7:::
daemon:*:17834:0:99999:7:::
# 输出的信息来看，最左边的的账户有相同的账户，且以 : 分割

[root@study ~]# join -t ':' /etc/passwd /etc/shadow | head -n 3
# 看到了吗，作用就是将某个字段的数据合并成一段
root:x:0:0:root:/root:/bin/bash:$6$oTg/fYGfv9/GIl6h$UEcmYlRZacV757rHtXlvmu5xH5TWGfqd3eDOEotB3CAc5mcW5UEoMTSg0pDICd/sYGrEScsHQY9tYZY0FGkKS1::0:99999:7:::
bin:x:1:1:bin:/bin:/sbin/nologin:*:17834:0:99999:7:::
daemon:x:2:2:daemon:/sbin:/sbin/nologin:*:17834:0:99999:7:::

# 范例 2：/etc/passwd 第四个字段是 GID，/etc.group 的第三个字段是 GID ,那么如何将两个文件合并？
[root@study ~]# head -n 3 /etc/passwd /etc/group
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin

==> /etc/group <==
root:x:0:
bin:x:1:
daemon:x:2:

# 下面两种写法一致
join -t ':' -1 4 -2 3 /etc/passwd /etc/group | head -n 3
join -t ':' -1 4 /etc/passwd -2 3 /etc/group | head -n
# 报错了，提示没有排序过，所以在使用时要先对内容排序，这样才能合并两行数据
join: /etc/passwd:6: is not sorted: sync:x:5:0:sync:/sbin:/bin/sync
join: /etc/group:11: is not sorted: wheel:x:10:mrcode
# 看下面被整合的内容
0:root:x:0:root:/root:/bin/bash:root:x:
1:bin:x:1:bin:/bin:/sbin/nologin:bin:x:
2:daemon:x:2:daemon:/sbin:/sbin/nologin:daemon:x:


```

#### paste：将两行贴在一起

将两行贴在一起，且中间以 tab 隔开

```bash
paste [-d] file1 file2

-d：后面可以接分割符。默认以 tab 来分割
- ：如果 file 部分写成 -，表示来自 standard input
    
```

```bash
# 范例 1：用 root 身份，将 /etc/passwd 与 /etc/shadow 同一行贴在一起
[root@study ~]# paste /etc/passwd /etc/shadow | head -n 3
root:x:0:0:root:/root:/bin/bash root:$6$oTg/fYGfv9/GIl6h$UEcmYlRZacV757rHtXlvmu5xH5TWGfqd3eDOEotB3CAc5mcW5UEoMTSg0pDICd/sYGrEScsHQY9tYZY0FGkKS1::0:99999:7:::
bin:x:1:1:bin:/bin:/sbin/nologin        bin:*:17834:0:99999:7:::
daemon:x:2:2:daemon:/sbin:/sbin/nologin daemon:*:17834:0:99999:7:::

# 范例 2：先将 /etc/group 用 cat 读出，然后与范例 1 贴在一起，且仅取出前三行
# paset 文件部分可以是多个，这里最后一个文件使用了 -，也就是 cat /cat/etc/group
[root@study ~]# cat /etc/group | paste /etc/passwd /etc/shadow - | head -n 3
root:x:0:0:root:/root:/bin/bash root:$6$oTg/fYGfv9/GIl6h$UEcmYlRZacV757rHtXlvmu5xH5TWGfqd3eDOEotB3CAc5mcW5UEoMTSg0pDICd/sYGrEScsHQY9tYZY0FGkKS1::0:99999:7:::       root:x:0:
bin:x:1:1:bin:/bin:/sbin/nologin        bin:*:17834:0:99999:7:::        bin:x:1:
daemon:x:2:2:daemon:/sbin:/sbin/nologin daemon:*:17834:0:99999:7:::     daemon:x:2:
```

#### expand：将 tab 转成空格

```bash
expand [-t] file

-t：后面可以接数字。一般来说，一个 tab 可以用 8 个空格取代，这里自定义几个空格取代

```

```bash
# 范例 1：将 /etc/man_db.conf 内行首为 MANPATH 的字样取出，仅取前三行
[root@study ~]# grep '^MANPATH' /etc/man_db.conf | head -n 3
MANPATH_MAP     /bin                    /usr/share/man
MANPATH_MAP     /usr/bin                /usr/share/man
MANPATH_MAP     /sbin                   /usr/share/man
# 行首正则为 ^,下接讲解

# 范例 2：承上，将所有的符号都列出来
[root@study ~]# grep '^MANPATH' /etc/man_db.conf | head -n 3 | cat -A
MANPATH_MAP^I/bin^I^I^I/usr/share/man$
MANPATH_MAP^I/usr/bin^I^I/usr/share/man$
MANPATH_MAP^I/sbin^I^I^I/usr/share/man$
# ^I 是 tab

# 范例 3：承上，将 tab 转成 6 个空格
[root@study ~]# grep '^MANPATH' /etc/man_db.conf | head -n 3 | expand -t 6 | cat -A
MANPATH_MAP /bin              /usr/share/man$
MANPATH_MAP /usr/bin          /usr/share/man$
MANPATH_MAP /sbin             /usr/share/man$
# 可以看到 tab 被替换成空格了

```

有一个需要特别注意：**tab** 最大功能就是格式排列整齐，但是换成空格之后，就不一定是排列整齐的了，也可以参考一下 **unexpand** 这个将空白转成 **tab** 的指令

```bash
[root@study ~]# grep '^MANPATH' /etc/man_db.conf | head -n 3 | expand -t 6 | unexpand -t 6 | cat -A
MANPATH_MAP /bin^I^I^I/usr/share/man$
MANPATH_MAP /usr/bin^I^I/usr/share/man$
MANPATH_MAP /sbin^I^I^I/usr/share/man$
# 可以看到，范例 3 的还可以被 unexpand 给转换回来

```

#### 分区命令：split

**split** 可以分割文件，按文件大小或行数来分割

```bash
split [-bl] file PREFIX

-b：后面可接要分区的大小，可加单位，如 b、k、m 等
-l：以行数进行分区

PREFIX：表示分区文件命名前缀
    
```

```bash
# 范例 1：/etc/services 有 600 多 k，若想要分成 300k 一个文件
[mrcode@study ~]$ cd /tmp; split -b 300k /etc/services servers
[mrcode@study tmp]$ ll servers*
-rw-rw-r--. 1 mrcode mrcode 307200 Dec  2 09:53 serversaa
-rw-rw-r--. 1 mrcode mrcode 307200 Dec  2 09:53 serversab
-rw-rw-r--. 1 mrcode mrcode  55893 Dec  2 09:53 serversac

# 范例 2：如何将上面三个文件合成一个文件？
[mrcode@study tmp]$ cat serversa* > servicesback
[mrcode@study tmp]$ ll serv*
-rw-rw-r--. 1 mrcode mrcode 307200 Dec  2 09:53 serversaa
-rw-rw-r--. 1 mrcode mrcode 307200 Dec  2 09:53 serversab
-rw-rw-r--. 1 mrcode mrcode  55893 Dec  2 09:53 serversac
-rw-rw-r--. 1 mrcode mrcode 670293 Dec  2 09:54 servicesback

# 范例 3：使用 ls -al / 输出的信息中，每 10 行记录成一个文件
# 这里文件使用了 - ，表示使用标准输入，前面讲过的
[mrcode@study tmp]$ ls -al / | split -l 10 - lsroot
[mrcode@study tmp]$ ll lsroot*
-rw-rw-r--. 1 mrcode mrcode 456 Dec  2 09:57 lsrootaa
-rw-rw-r--. 1 mrcode mrcode 523 Dec  2 09:57 lsrootab
-rw-rw-r--. 1 mrcode mrcode 192 Dec  2 09:57 lsrootac
[mrcode@study tmp]$ wc -l lsroot*
  10 lsrootaa
  10 lsrootab
   4 lsrootac
  24 total
# - 一般用在，指令 stdout/stdin 时，但偏偏又没有文件，就用 - 来表示 stdout/stdin
```

#### 参数代换：xargs

产生某个指令的参数。**xargs** 可以读入 **stdin** 的数据，并且以空格符或换行符号作为分辨，将 **stdin** 的数据分割成为 **arguments**。

```bash
xargs [-0epn] command

```

- **0**：数值 **0**，如果输入的 stdin 含有特殊字符，例如 `、\、空格等时，可以将他转义为一个普通字符
- **e**：**EOF**（**end of file**）。后面可以接一个字符串，当 **xargs** 分析到这个字符串时，会停止继续工作；注意：`-e'sync'` 选项与后面的 **eof** 字符中间没有空格
- **p**：在执行每个指令的 **argument** 时，都会询问使用者
- **n**：后面接次数，每次 **command** 指令执行时，要使用几个参数

当 **xargs** 后面没有接任何指令时，默认是以 **echo** 来进行输出的

实践练习

```bash
# 范例 1：将 /etc/passwd 内第一栏取出，仅取三行，使用 id 这个指令将每个账户内容秀出来
# id 可以查询用户的 UID/GID 等信息
[mrcode@study tmp]$ id root
uid=0(root) gid=0(root) groups=0(root)

# 通过之前的指令把前三行的第一栏用户名提取出来
[mrcode@study tmp]$ cat /etc/passwd | head -n 3 | cut -d ':' -f 1
root
bin
daemon

# 通过 $(cmd) 可以预先取得参数，但可惜的时候，id 这个指令只能接收一个参数，导致报错了
[mrcode@study tmp]$ id $(cat /etc/passwd | head -n 3 | cut -d ':' -f 1)
id: extra operand ‘bin’
Try 'id --help' for more information.

# 因为 ID 不是管线命令，管线前的输出都没有用，相当于只输出了 id 的内容
[mrcode@study tmp]$ cat /etc/passwd | head -n 3 | cut -d ':' -f 1 | id
uid=1000(mrcode) gid=1000(mrcode) groups=1000(mrcode),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

# xargs 将 3 个账户处理后给 id，一样的会报错
[mrcode@study tmp]$ cat /etc/passwd | head -n 3 | cut -d ':' -f 1 | xargs id
id: extra operand ‘bin’
Try 'id --help' for more information.

# 通过 -n 来指定每次指令命令使用几个参数
# 因为前面输出是三行，在 xargs 中会被当成 3 个参数
[mrcode@study tmp]$ cat /etc/passwd | head -n 3 | cut -d ':' -f 1 | xargs -n 1 id
uid=0(root) gid=0(root) groups=0(root)
uid=1(bin) gid=1(bin) groups=1(bin)
uid=2(daemon) gid=2(daemon) groups=2(daemon)

# 范例 2：同上，但是每次执行 id 时，都要询问使用者是否动作
[mrcode@study tmp]$ cat /etc/passwd | head -n 3 | cut -d ':' -f 1 | xargs -n 1 -p id
id root ?...			# 这里没有输入 y 被判定为不执行了
id bin ?...y
uid=1(bin) gid=1(bin) groups=1(bin)
id daemon ?...

# 范例 3：将所有的 /etc/passwd 内的账户都以 id 查询，但差到 sync 就结束指令串
[mrcode@study tmp]$ cat /etc/passwd | cut -d ':' -f 1 | xargs -e'sync' -n 1 id
uid=0(root) gid=0(root) groups=0(root)
uid=1(bin) gid=1(bin) groups=1(bin)
uid=2(daemon) gid=2(daemon) groups=2(daemon)
uid=3(adm) gid=4(adm) groups=4(adm)
uid=4(lp) gid=7(lp) groups=7(lp)


```

**xargs** 是一个非常好用的指令，一般使用它的原因是，很多指令其实并不支持管线命令，因此可以通过 **xargs** 来提供该指令引用 **standard input** 。如果还不太明白，下面在来看一个例子

```bash
# 范例 4：找出 /usr/sbin 下具有特殊权限的文件名，并使用 ls -l 列出详细属性
# 但是 ls 不是管线命令
[mrcode@study tmp]$ find /usr/bin/ -perm /7000 | ls
# 可以使用 $(cmd) 语法
[mrcode@study tmp]$ ls -l $(find /usr/bin/ -perm /7000)

# 使用 xargs
[mrcode@study tmp]$ find /usr/bin/ -perm /7000 | xargs -n 1 ls -l
-r-xr-sr-x. 1 root tty 15344 Jun 10  2014 /usr/bin/wall
-rwsr-xr-x. 1 root root 32096 Oct 31  2018 /usr/bin/fusermount

```

### 关于减号 `-` 的用途

管线命令在 **bash** 的连续的处理程序中是相当重要的。另外，在 **log file** 的分析中也是很重要的一环。

另外，在管线命令中，常常会使用到前一个指令的 **stdout** 作为这次的 **stdin**，某些指令需要用到文件名（例如 **tar**）来进行处理时，该 **stdin** 与 **stdout** 可以利用减号 `-`来替代

```bash
# 将 /home 里的文件打包，但打包的数据不是记录到文件，而是传送到 stdout
# 经过管线后，将 tar -cvf - /home 传送给后面的 tar -xvf - ，
# 这里的 - 就是取用前一个指令的 stdout
mkdir /tmp/homeback
[mrcode@study tmp]$ tar -cvf - /home/ | tar -xvf - -C /tmp/homeback/
```
