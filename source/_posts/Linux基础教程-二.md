---
title: Linux基础教程(二)
date: 2024-06-18 10:38:44
tags:
- Linux
categories: Linux
---

# 正则表达式与文件格式化处理

正则表达式（**Regular Expression** ）是通过一些特殊字符的排列，用来搜索或替换、删除一列或多列文字字符串

本章需要多练习：因为目前很多套件都使用正则表达式来达成过来、分析的目的，为了未来主机管理的便利性，使用者至少要能看懂正则表达式的含义

## 开始之前：什么是正则表达式

- 什么是正则表达式：

  这个就不解释了，某些指令支持，比如 `grep 'mail' /lib/systemd/system/*` 搜索该目录下的所有文件中包含 **mail** 的文件，但是 **cp ls** 等命令不支持正则表达式，只能使用 **bash** 自己本身的通配符

- 正则表达式对于系统管理员的用途

  对于一般使用者来说，使用不多，对于系统管理员来说，是必学的知识，如 错误信息登录文件（第十八章中）的内容记录了系统产生的所有信息，包含是否被入侵的记录数据，可以通过正则表达式将这些登录信息进行处理，仅取出有问题的信息进行分析

- 正则表达式的广泛用途

  由于正则表达式强大的字符串处理能力，一堆软件都支持

- 正则表达式与 **shell** 在 **Linux** 中的角色定位

  这样说吧，小学的 九九乘法表 有多重要，**shell** 与 正则表达式就有多重要

- 扩展的正则表达式

  正则表达式的字符串表示方式依照不同的严谨程度分为：基础正则表达式、扩展正则表达式。

## 基础正则表达式

既然正则表达式是处理字符串的一种表示方式，那么对字符排序有影响的语系数据就会对正则表达式的结果有影响。此外也需要有支持工具程序来辅助才行。

因此这里先介绍一个最简单的字符串摘取工具程序 **grep**。前面讲解了 **grep** 的相关参数与参数，本章着重讲解进阶的 **grep** 选项说明，介绍完 **grep** 的功能后，就进入正则表达式的特殊字符处理能力

### 语系对正则表达式的影响

为什么语系数据会影响正则表达式的输出结果？在第 0 章计算器概论的文字编码系统里面谈到，文件其实记录的仅有 **0** 与 **1**，我们看到的字符与数值都是通过编码表转换来的。

由于不同语系的编码数据不同，就会造成数据处理结果的差异了，举例说明，假设两种语系输出结果为：

- **LANG=C：0 1 2 3 ... A B C D ..Z a b c d .. z**
- **LANG=zh_TW ：0 1 2 3 ... a A b c C D .. z Z**

两种语系明显不一样，如果想获取大写字符使用 `[A-Z]`时，会发现 **C** 可以获取到正确的大写字符（因为是连续的），**zh_TW** 连同小写也会 b-z 也会获取到，因为就编码的顺序来看，**big5** 语系可以获取到 `A b B c C .. z Z` 这一堆字符。

所以使用正则表达式时，需要留意当前的语系，否则可能发现与别人不同的截取结果

由于一般我们再联系正则表达式时，使用的是兼容于 **POSIX** 的标准，因此就使用 **C** 这个语系，因此下面的练习都是使用 `LANG=C`来练习的。为了避免这样编码所造成的英文与数字截取问题，因此特殊符号需要了解下

- `[:alnum:]`：代表英文大小写字符及数字，即 **0-9**、**A-Z**、**a-z**
- `[:alpha:]`：代表任何英文大小写字符，**A-Z**、**a-z**
- `[:blank:]`：代表空格与 **tab**
- `[:cntrl:]`：代表键盘上面的控制按键，包括 **CR**、**LF**、**TAB**、**Del** 等
- `[:digit:]`：代表数字，**0-9**
- `[:graph:]`：除了空格符（空格键与 **tab** 键）外其他的所有按键
- `[:lower:]`：代表些小字符，**a-z**
- `[:print:]`：代表任何可以被打印出来的字符
- `[:punct:]`：代表标点符号（**punctuation symbol**）
- `[:upper:]`：代表大写字符，**A-Z**
- `[:space:]`：任何会产生空白的字符，包括空格、**tab**、**CR** 等
- `[:xdigit:]`：代表 **16** 进制的数值类型，包括 **0-9**、**A-F**、**a-f** 的数字与字符

尤其是 `[:alnum:]`、`[:alpha:]`、`[:upper:]`、`[:lower:]`、`[:digit:]`一定要知道代表什么意思，因为他们要比 **a-z** 或 **A-Z** 的用途要确定。

### grep 的一些进阶选项

在第十章 **BASH** 中的 **grep** 谈论过一些基础用法，下面列出较进阶的 **grep** 选项与参数

```bash
grep [-A] [-B] [--color='auto'] '关键词' filename

-A：后面可以加数字，为 after 的意思，除了列出该行外，后续的 n 行也列出来
-B：后面可以加数字，为 befer 的意思，处理列出该行外，前面的 n 行也列出来
--colort=auto：可将正确的哪个截取数据列出颜色
```

实践与练习

```bash
# 范例 1：用 dmesg 列出核心信息，再以 grep 找出含有 qx1 那一行
dmesg | grep 'qx1'
# 笔者不知道自己使用的显卡是什么，而且使用的是虚拟机，而作者使用的显卡是 qx1，所以查看显卡信息

# 范例 2：用 --color=auto 显示查找到的关键词高亮,并显示行号
dmesg | grep -n --color=auto ‘qx1’

# 范例 3：在关键词所在行的前两行与后三行也一起显示出来
dmest | grep -n -A2 -B3 --color=auto 'qx1'


```

**grep** 是一个很常见也很常用的指令，最重要的功能就是进行字符串的比对，然后将符合用户需求的字符串打印出来。需要注意的是：**grep** 是已整行为单位来进行数据截取的

### 基础正则表达式练习

要了解正则表达式最简单的方法就是由实际练习去感受，所以在汇总特殊符号前，先以下面这个文件的内容来进行正则表达式的练习，练习前提为：

- 语系已经使用 `export LANG=C；export LC_ALL=C`
- **grep** 已经使用 **alias** 设置为 `grep --color=auto`

本机默认为 `LANG=en_US.UTF-8;LC_ALL=`

文件为 **regular——express.txt** ，该文件内容是在 **windows** 系统下编辑的，所以包含 **dos** 的换行符；

```bash
"Open Source" is a good mechanism to develop programs.
apple is my favorite food.
Football game is not use feet only.
this dress doesn't fit me.
However, this dress is about $ 3183 dollars.
GNU is free air not free beer.
Her hair is very beauty.
I can't finish the test.
Oh! The soup taste good.
motorcycle is cheap than car.
This window is clear.
the symbol '*' is represented as start.
Oh!	My god!
The gd software is a library for drafting programs.
You are the best is mean you are the no. 1.
The world <Happy> is the same with "glad".
I like dog.
google is the best tools for search keyword.
goooooogle yes!
go! go! Let's go.
# I am VBird


```

**范例 1：搜索特定字符**

从文件中取得 **the** 这个特定字符串，最简单的方式如下

```bash
[mrcode@study tmp]$ grep -n 'the' regular_express.txt
8:I can't finish the test.
12:the symbol '*' is represented as start.
15:You are the best is mean you are the no. 1.
16:The world <Happy> is the same with "glad".
18:google is the best tools for search keyword.
```

反向选择，可以看到输出结果少了上面的 **8、12、15、16、18** 行

```bash
[mrcode@study tmp]$ grep -vn 'the' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
4:this dress doesn't fit me.
5:However, this dress is about $ 3183 dollars.
6:GNU is free air not free beer.
7:Her hair is very beauty.
9:Oh! The soup taste good.
10:motorcycle is cheap than car.
11:This window is clear.
13:Oh!  My god!
14:The gd software is a library for drafting programs.
17:I like dog.
19:goooooogle yes!
20:go! go! Let's go.
21:# I am VBird
22:

```

忽略大小写 ，多出来几行

```bash
[mrcode@study tmp]$ grep -in 'the' regular_express.txt
8:I can't finish the test.
9:Oh! The soup taste good.
12:the symbol '*' is represented as start.
14:The gd software is a library for drafting programs.
15:You are the best is mean you are the no. 1.
16:The world <Happy> is the same with "glad".
18:google is the best tools for search keyword.
```

**范例 2：利用中括号`[]`来搜索集合字符**

如果要搜索 test 或 taste 这两个单词时，可以发现他们其实有共同的 `t?st` 存在

```text
[mrcode@study tmp]$ grep -n 't[ae]st' regular_express.txt
8:I can't finish the test.
9:Oh! The soup taste good.
```

中括号中，无论几个字符都表示任意一个字符。如果想要搜索到所有 **oo** 字符时

```bash
[mrcode@study tmp]$ grep -n 'oo' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
9:Oh! The soup taste good.
18:google is the best tools for search keyword.
19:goooooogle yes!
```

如果不想要 **oo** 前面的 **g** 呢？

```bash
[mrcode@study tmp]$ grep -n '[^g]oo' regular_express.txt
2:apple is my favorite food.
3:Football game is not use feet only.
18:google is the best tools for search keyword.
19:goooooogle yes!
```

会发现可能会有一部分是正确的，一部分是错误的，比如 **1**、**9** 行少了，但是 **google** 和 **goooooogle** 还是出来了，是怎么回事？第 **18** 行，出现了 **tools** 所以也符合 `[^g]oo`，而 **19** 行，中间有那么多的 **oo**，也符合

继续，不想要 **oo** 前面是小写字符的

```bash
# 由于小写字符的 ASCII 编码顺序是连续的，所以可以简化为，否则就需要把 a-z 都写出来
[mrcode@study tmp]$ grep -n '[^a-z]oo' regular_express.txt
3:Football game is not use feet only.

# 取得有数字那一行
[mrcode@study tmp]$ grep -n '[0-9]' regular_express.txt
5:However, this dress is about $ 3183 dollars.
15:You are the best is mean you are the no. 1.

```

由于考虑到语系对于编码顺序的影响，因此除了连续编码使用减号 `-`，还可以使用如下的方法来取得前面两个测试的结果

```bash
[mrcode@study tmp]$ grep -n '[^[:lower:]]oo' regular_express.txt
3:Football game is not use feet only.

[mrcode@study tmp]$ grep -n '[[:digit:]]' regular_express.txt
5:However, this dress is about $ 3183 dollars.
15:You are the best is mean you are the no. 1.
```

**范例 3：行首与行尾字符** `^、$`

```bash
# 只要行首是 the 的
[mrcode@study tmp]$ grep -n '^the' regular_express.txt 
12:the symbol '*' is represented as start.

# 想要行首是小写字符开头的
[mrcode@study tmp]$ grep -n '^[a-z]' regular_express.txt 
2:apple is my favorite food.
4:this dress doesn't fit me.
10:motorcycle is cheap than car.
12:the symbol '*' is represented as start.
18:google is the best tools for search keyword.
19:goooooogle yes!
20:go! go! Let's go.
# 下面的等效
# [mrcode@study tmp]$ grep -n '^[[:lower:]]' regular_express.txt 

# 不要英文字母开头的
# ^ 在中括号内表示反选，在外表示定位首航
[mrcode@study tmp]$ grep -n '^[^a-zA-Z]' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
21:# I am VBird

```

行尾练习

```bash
# 找出行尾为 . 符号的数据
# 使用 \ 对 小数点转义
[mrcode@study tmp]$ grep -n '\.$' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
4:this dress doesn't fit me.
5:However, this dress is about $ 3183 dollars.
6:GNU is free air not free beer.
7:Her hair is very beauty.
8:I can't finish the test.
9:Oh! The soup taste good.
10:motorcycle is cheap than car.
11:This window is clear.
12:the symbol '*' is represented as start.
14:The gd software is a library for drafting programs.
15:You are the best is mean you are the no. 1.
16:The world <Happy> is the same with "glad".
17:I like dog.
18:google is the best tools for search keyword.
20:go! go! Let's go.
```

这里需要说一句，原本的文件 **5-9** 行默认是 `.^M$` 结尾的，也就是 `\r\n`，由于没有网络，无法下载文件，所以复制粘贴丢失了这些换行符，和书上结果不一样。

也就是说上面的示例 **5-9** 不应该出来的，使用命令查看特殊字符应该如下

```bash
[mrcode@study tmp]$ cat -An regular_express.txt | head -n 10 | tail -n 6
     5  However, this dress is about $ 3183 dollars.^M$
     6  GNU is free air not free beer.^M$
     7  Her hair is very beauty.^M$
     8  I can't finish the test.^M$
     9  Oh! The soup taste good.^M$
    10  motorcycle is cheap than car.$		# 但实际上 ^M 被丢失了
```

找出空白行

```bash
[mrcode@study tmp]$ grep -n '^$' regular_express.txt 
22:
# 只有行首和行尾的表示法，中间没有任何字符，所以是 ^$
```

假设你已经知道 **shell script** 或则是配置文件中，空白行与开头为 **#** 的那一行是批注，想要将这些数据忽略掉，该怎么做？

```bash
[mrcode@study tmp]$ cat -n /etc/rsyslog.conf 
# 在 centOS 7 中可以看到有 91 行，有大量的空行与批注信息

# 第一种写法：-v '^$' 是反选，也就是排除空行的，-v ‘^#’ 排除开头是 # 号的
# 但是这里的行号与源文件对不上了，后面的行号是针对前面排除空行后的行号
[mrcode@study tmp]$ grep -v '^$' /etc/rsyslog.conf | grep -vn '^#'
6:$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
7:$ModLoad imjournal # provides access to the systemd journal
18:$WorkDirectory /var/lib/rsyslog
20:$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
25:$IncludeConfig /etc/rsyslog.d/*.conf
28:$OmitLocalLogging on
30:$IMJournalStateFile imjournal.state
37:*.info;mail.none;authpriv.none;cron.none                /var/log/messages
39:authpriv.*                                              /var/log/secure
41:mail.*                                                  -/var/log/maillog
43:cron.*                                                  /var/log/cron
45:*.emerg                                                 :omusrmsg:*
47:uucp,news.crit                                          /var/log/spooler
49:local7.*                                                /var/log/boot.log

# 第二种实现：直接匹配行首非 # 开头的
# 因为使用了中括号表示需要有一个字符存在，所以空行的不会被匹配
[mrcode@study tmp]$ grep -n '^[^#]' /etc/rsyslog.conf 
9:$ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
10:$ModLoad imjournal # provides access to the systemd journal
26:$WorkDirectory /var/lib/rsyslog
29:$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
36:$IncludeConfig /etc/rsyslog.d/*.conf
40:$OmitLocalLogging on
43:$IMJournalStateFile imjournal.state
54:*.info;mail.none;authpriv.none;cron.none                /var/log/messages
57:authpriv.*                                              /var/log/secure
60:mail.*                                                  -/var/log/maillog
64:cron.*                                                  /var/log/cron
67:*.emerg                                                 :omusrmsg:*
70:uucp,news.crit                                          /var/log/spooler
73:local7.*                                                /var/log/boot.log
```

这里要注意的是批注可以出现在任意处，所以匹配行首的是最安全的做法

### 范例 4：任意一个字符 `.` 与重复字符 `*`

在第十章 **bash** 中，通配符 `*`表示任意（0 或 多个）字符，但是正则表达式中并不是这样，他们含义如下：

- `.`：一定有一个任意字符
- `*`：重复前一个字符，0 到任意次，为组合形态

```bash
# 找出 g??d 的字符串，也就是 g 开头 d 结尾的 4 字符的字符串
[mrcode@study tmp]$ grep -n 'g..d' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
9:Oh! The soup taste good.
16:The world <Happy> is the same with "glad".

# 找出 oo、ooo、ooo 等数据，至少含有 2 个 o
# 注意，这里不能写 oo* 因为，*是作用于第二个 o 的，表示 0 到任意个
# 也就是说如果是 oo* 有可能匹配到一个 o 
[mrcode@study tmp]$ grep -n 'ooo*' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
9:Oh! The soup taste good.
18:google is the best tools for search keyword.
19:goooooogle yes!

# 找出 开头与结尾都是 g ，并且中间至少含有一个 o 的数据
# 也就是 gog、goog 之类的数据
[mrcode@study tmp]$ grep -n 'goo*g' regular_express.txt 
18:google is the best tools for search keyword.
19:goooooogle yes!

# 找出 开头与结尾都是 g，中间有无字符均可
[mrcode@study tmp]$ grep -n 'g*g' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
3:Football game is not use feet only.
9:Oh! The soup taste good.
13:Oh!  My god!
14:The gd software is a library for drafting programs.
16:The world <Happy> is the same with "glad".
17:I like dog.
18:google is the best tools for search keyword.
19:goooooogle yes!
20:go! go! Let's go.
# 使用 g*g 发现第一行的数据就不匹配，这个还是需要再终端看，因为可以开启高亮，方便查看哈
# 原因是 * 作用于 g，g* 代表空字符或一个以上的 g，因此应该匹配 g、gg、ggg 等

# 正确的应该这样实现
[mrcode@study tmp]$ grep -n 'g.*g' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
14:The gd software is a library for drafting programs.
18:google is the best tools for search keyword.
19:goooooogle yes!
20:go! go! Let's go.

# 找出包含任意数字的数据
# 同上，[0-9]* 只作用于一个中括号
[mrcode@study tmp]$ grep -n '[0-9][0-9]*' regular_express.txt 
5:However, this dress is about $ 3183 dollars.
15:You are the best is mean you are the no. 1.
# 直接使用 grep -n '[0-9]' regular_express.txt 也可以得到相同结果哈
```

### 范例 5：限定连续 正则字符范围 `{}`

找出 **2** 个到 **5** 个 **o** 的连续字符串

```bash
# 华括弧在 shell 中是特殊符号，需要转义
[mrcode@study tmp]$ grep -n 'o\{2\}' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
9:Oh! The soup taste good.
18:google is the best tools for search keyword.
19:goooooogle yes!
# 上述结果是至少是 2 个 oo 的出来了

# 单词开头结尾都是 g，中间 o，至少 2 个，最多 5 个
[mrcode@study tmp]$ grep -n 'go\{2,5\}g' regular_express.txt 
18:google is the best tools for search keyword.

# 承上，只是中间的 o 至少 2 个
[mrcode@study tmp]$ grep -n 'go\{2,\}g' regular_express.txt 
18:google is the best tools for search keyword.
19:goooooogle yes!
```

## 基础正则表示法字符汇总

- `^word`：搜索的关键词 **word** 在行首

  范例：搜索行首为 **#** 的，并列出行号 `grep -n '^#' file`

- `word$`：搜索的关键词 **word** 在行尾

  范例：搜索以 ！结尾的，`grep -n '!$' file`

- `.`：一定有一个任意字符

  范例：搜索字符串可以是 **eve**、**eae**、**eee**、**e e**；`grep -n 'e.e' file`

- `\`：转义字符

  范例：搜索含有单引号数据。`grep -n '\’' file`

- `*`：重复另个到无穷多个前一个字符

  范例：找出含有 **es**、**ess**、**esss** 等字符串；`grep -n 'es*' file`

- `[list]`：里面列出想要截取的字符合集

  范例：找出含有 **g1** 或 **gd** 的数据；`grep -n 'g[1d]' file`

- `[n1-n2]`：字符合集范围

  范例：找出含有任意大写字母的数据；`grep -n '[A-Z]' file`

- `[^list]`：不要包含该集合中的字符或该范围的字符

  范例：找出 **ooa**、**oog** 但是不包含 **oot** 的数据; `grep -n 'oo[^t]'`

- `\{n,m\}`：连续 **n** 到 **m** 个前一个字符

- `\{n\}`：连续 **n** 个前一个字符

- `\{n,\}`：至少 **n** 个以上的前一个字符；咋效果上感觉和 `\{n\}` 是一样的

最后再强调，通配符和正则表达式不一样，比如在 **ls** 命令中找出以 **a** 开头的文件

- 通配符：`ls -l a*`
- 正则表达式：`ls | grep -n '^a'` 或则 `ls | grep -n '^a.*'`

```bash
# 范例：以 ls -l 配合 grep 找出 /etc/ 下文件类型为链接文件属性的文件名
# 符号链接文件的特点是权限前面一位是 l，根据 ls 的输出，只要找到行首为 l 的即可
[mrcode@study tmp]$ ls -l /etc | grep '^l'
lrwxrwxrwx.  1 root root       56 Oct  4 18:22 favicon.png -> /usr/share/icons/hicolor/16x16/apps/fedora-logo-icon.png
lrwxrwxrwx.  1 root root       22 Oct  4 18:23 grub2.cfg -> ../boot/grub2/grub.cfg
```

### sed 工具

了解了一些正则基础使用后，可以来玩一玩 **sed** 和 **awk** ；作者就利用他们两个实现了一个小工具：**logfile.sh** 分析登录文件（第十八章会讲解）。里面绝大部分关键词的提取、统计等都是通过他们来完成的

**sed**：本身是一个管线命令，可以分析 **standard input** 的数据，还可以将数据进行替换、新增、截取特定行等功能

```bash
sed [-nefr] [动作]
    
```

选项与参数：

- **n**：使用安静（**silent**）模式

  在一般 **sed** 的用法中，所有来自 **STDIN** 的数据一般都会列出到屏幕上，加上 **-n** 之后，只有经过 **sed** 特殊处理的那一行（或则动作）才会被打印出来

- **e**：直接在指令模式上进行 **sed** 的动作编辑

- **f**：直接将 **sed** 的动作写在一个文件内，`- f filename` 则可以执行 **filename** 内的 **sed** 动作

- **r**：**sed** 的动作支持是延伸类型正则表达式的语法（预设是基础正则表达式语法）

- **i**：直接修改读取的文件内容，而不是由屏幕输出

动作说明：`[n1[,n2]]function`

`n1,n2`：不见得会存在，一般代表「选择进行动作的行数」，比如：如果我的动作是需要再 **10** 到 **20** 行之间进行的，则「10,20[动作行为]」

**function** 有如下：

- **a**：新增，**a** 后面可以接字符串，这些字符串会在新的一行出现（当前的下一行）
- **c**：替换，**c** 后面可以接字符串，这些字符串替换 **n1,n2** 之间的行
- **d**：删除，后面不接任何字符串
- **i**：插入，**i** 的后面可以接字符串，而这些字符串会在新的一行出现（当前的上一行）
- **p**：打印，将某个选择的数据打印。通常 **p** 会参与 **sed -n** 一起运作
- **s**：替换，可以直接进行替换工作。通常这个 **s** 的动作可以搭配正则表达式，例如：`1,20s/old/new/g`

#### **以行为单位的新增/删除功能**

```bash
# 范例1：将 /etc/passwd 的内容列出并且打印行号，同时将第 2~5 行删除
[mrcode@study ~]$ nl /etc/passwd | sed '2,5d'   	# 注意写法和结果
     1  root:x:0:0:root:/root:/bin/bash
     6  sync:x:5:0:sync:/sbin:/bin/sync
     7  shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
# 另外这里，应该带上 sed -e '2,5d' 才标准，不过不带也可以，但是需要使用单引号括起来
# 实测不用单引号也可以实现

# 范例2：只删除第二行
[mrcode@study ~]$ nl /etc/passwd | sed '2d'

# 范例3：删除第三行到最后一行
[mrcode@study ~]$ nl /etc/passwd | sed '3,$d'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin

# 范例4：在第二行后（也就是加载第三行）加上「drink tea？」字样
[mrcode@study ~]$ nl /etc/passwd | sed '2a drink tea?'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin
drink tea?
     3  daemon:x:2:2:daemon:/sbin:/sbin/nologin

# 范例5：在第二行后面加入两行字
# 注意：不要一开始就写好所有的单引号，因为需要使用 \ + 回车触发换行
[mrcode@study ~]$ nl /etc/passwd | sed '2a drink tea \
> drink beer?'
     1  root:x:0:0:root:/root:/bin/bash
     2  bin:x:1:1:bin:/bin:/sbin/nologin
drink tea 
drink beer?
```

#### 以行为单位的取代显示功能

```bash
# 范例1：将第 2-5 行的内容替换为 no 2-5 nuber
[mrcode@study ~]$ nl /etc/passwd | sed '2,5c no 2-5 number'
     1  root:x:0:0:root:/root:/bin/bash
no 2-5 number

# 范例2：取出第 11-20 行
# 通过之前的知识来达成需要这样写
[mrcode@study ~]$ nl /etc/passwd | head -n 20 | tail -n 10
    11  games:x:12:100:games:/usr/games:/sbin/nologin
    12  ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
    13  nobody:x:99:99:Nobody:/:/sbin/nologin
    14  systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
    15  dbus:x:81:81:System message bus:/:/sbin/nologin
    16  polkitd:x:999:998:User for polkitd:/:/sbin/nologin
    17  colord:x:998:997:User for colord:/var/lib/colord:/sbin/nologin
    18  libstoragemgmt:x:997:995:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
    19  rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
    20  saslauth:x:996:76:Saslauthd user:/run/saslauthd:/sbin/nologin
# 注意需要使用 -n 只输出 sed 处理过的数据
[mrcode@study ~]$ nl /etc/passwd | sed -n '11,20p'
```

#### 部分数据的搜索并替换功能

除了整行的处理模式外，还可以用行为单位进行部分数据的搜索并替换的功能，基本上 **sed** 的搜索与替换与 **vi** 类似

```bash
sed 's/要被替换的字符串/新的字符串/g'

```

```bash
# 范例1：先观察原始信息，利用 /sbin/ifconfig 查询 IP
[mrcode@study ~]$ /sbin/ifconfig 
enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.128  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::deb9:3a1b:fd0f:f6c2  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:a0:49:8f  txqueuelen 1000  (Ethernet)
        RX packets 2436261  bytes 219827411 (209.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2011081  bytes 319310584 (304.5 MiB)
# 还未讲解到 IP,这里先关注第二行的 IP

# 利用关键词配合 grep 截取出关键的一行数据
[mrcode@study ~]$ /sbin/ifconfig enp0s3 | grep 'inet '
        inet 192.168.0.128  netmask 255.255.255.0  broadcast 192.168.0.255

# 将 ip 前面的信息删除，也就是 inet 
[mrcode@study ~]$ /sbin/ifconfig enp0s3 | grep 'inet ' | sed 's/inet //g'
        192.168.0.128  netmask 255.255.255.0  broadcast 192.168.0.255
# 需要使用通配符，不然会留下前面的空白符号：任意字符开头另个或多个
[mrcode@study ~]$ /sbin/ifconfig enp0s3 | grep 'inet ' | sed 's/^.*inet //g'
192.168.0.128  netmask 255.255.255.0  broadcast 192.168.0.255

# 再删除后续部分，只剩下 192.168.0.128
# 注意这里需要使用：空格任意个，来匹配前面多个空格
[mrcode@study ~]$ /sbin/ifconfig enp0s3 | grep 'inet ' | sed 's/^.*inet //g' | sed 's/ *netmask.*$//g'
192.168.0.128
```

上面例子建议一步一步的来做，下面继续研究 **sed** 与正则表示法配合练习

```bash
# 范例2：只要 MAN 存在的那几行数据，但是含有 # 在内的批注和空白行不要
# 步骤1：先使用 grep 将关键词 MAN 所在行取出来
[mrcode@study ~]$ cat /etc/man_db.conf | grep 'MAN'
# MANDATORY_MANPATH                     manpath_element
# MANPATH_MAP           path_element    manpath_element
# MANDB_MAP             global_manpath  [relative_catpath]
# every automatically generated MANPATH includes these fields
#MANDATORY_MANPATH                      /usr/src/pvm3/man
MANDATORY_MANPATH                       /usr/man
MANDATORY_MANPATH                       /usr/share/man
...省略...
# 步骤2：删除掉批注数据行
[mrcode@study ~]$ cat /etc/man_db.conf | grep 'MAN' | sed 's/^#.*$//g'





MANDATORY_MANPATH                       /usr/man
MANDATORY_MANPATH                       /usr/share/man
MANDATORY_MANPATH                       /usr/local/share/man
# 步骤3：删除空白行
# 注意这里使用了动作里面的 d 命令，前面是正则匹配？
[mrcode@study ~]$ cat /etc/man_db.conf | grep 'MAN' | sed 's/^#.*$//g' | sed '/^$/d'
MANDATORY_MANPATH                       /usr/man
MANDATORY_MANPATH                       /usr/share/man
MANDATORY_MANPATH                       /usr/local/share/man
```

#### 直接修改文件内容（危险动作）

```bash
# 范例1：利用 sed 将 /tmp/regular_express.txt 内每一行结尾若为 . 则换成 ！
# 下面还是使用了动作 s 替换，后面的是转义 . 和 !
# 这样可以直接修改文件内容
[mrcode@study tmp]$ sed -i 's/\./\!/g' regular_express.txt 

# 范例2：利用 sed 直接在 /tmp/regular_express.txt 最后一行加入 # This is a test
# $ 表示最后一行
[mrcode@study tmp]$ sed -i '$a # This is a test ' regular_express.txt 
# 想要删除最后一行就简单了
[mrcode@study tmp]$ sed -i '$d' regular_express.txt
```

## 延伸正则表示法

一般来说，只要了解了基础正则表示法大概就已经相当足够了，所谓技多不压身；还可以了解使用范围更广的延伸正则表示法。举个例子：前面讲解到要去除空白行与行首为 `#` 的行，使用的是

```bash
grep -v '^$' regular_express.txt | grep -v '^#'
```

需要使用到管线命令来搜寻两次，使用延伸的正则表示法则如下

```bash
egrep -v '^$|^#' regular_express.txt
```

此外，**grep** 预设仅支持基础的正则表示法，可以使用 `-E` 参数开启，不过建议用别名 **egrep**

下面是延伸正则表示法的符号（RE 字符）说明：

`+`：重复「一个或一个以上」的前一个 RE 字符

范例：搜索 `(god)(good)(goood)...`等字符串。 可以使用

```bash
[mrcode@study tmp]$ egrep -n 'go+d' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs!
9:Oh! The soup taste good!
13:Oh!  My god!
```

`?`：「**0** 个或 **1** 个」的前一个 **RE** 字符

范例：搜索 **gd**、**god**

```bash
[mrcode@study tmp]$ egrep -n 'go?d' regular_express.txt 
13:Oh!  My god!
14:The gd software is a library for drafting programs!
```

`|`：用或（or）的方式找出数个字符串

范例：搜索 **gd** 或 **good**

```bash
[mrcode@study tmp]$ egrep -n 'gd|good' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs!
9:Oh! The soup taste good!
14:The gd software is a library for drafting programs!
```

`()`：找出「群组」字符串

范例：搜索 **glad** 或 **good**

```bash
# 当然，这里使用上面完整的或来匹配两个固定单词也是可以的
[mrcode@study tmp]$ egrep -n 'g(la)|(oo)d' regular_express.txt 
1:"Open Source" is a good mechanism to develop programs!
2:apple is my favorite food!
9:Oh! The soup taste good!
16:The world <Happy> is the same with "glad"!
```

`()+`：多个重复群组的判别

范例：将「**AxyzxyzxyzxyzC**」用 **echo** 叫出，然后再使用如下的方法搜索

```bash
[mrcode@study tmp]$ echo 'AxyzxyzxyzxyzC' | egrep 'A(xyz)'
Axyz xyzxyzxyzC # 在命令行中是有红色高亮的，这个只能高亮到 Axyz
[mrcode@study tmp]$ echo 'AxyzxyzxyzxyzC' | egrep 'A(xyz)+'
Axyzxyzxyzxyz C # C 不会高亮
[mrcode@study tmp]$ echo 'AxyzxyzxyzxyzC' | egrep 'A(xyz)+C'
AxyzxyzxyzxyzC # 完全匹配
```

> **TIP**
>
> 要特别注意：`grep -n '[!>]' xx.txt` 的含义并不是除了 > 字符之外的字符，因为 `!` 不是一个特殊符号
>
> 想要表示非，需要这样写 `grep -n '[^a-z]' xx.txt`

## 文件的格式化与相关处理

不需要通过 **vim** 去编辑，而是通过数据流重导向配置 **printf** 功能以及 **awk** 指令，可以对文字信息进行排版显示

### 格式化打印：printf

比如将考试分数输出，姓名与科目及分数之间，稍微做个比较漂亮的版面，比如输出下面这样的表格

```text
Name		Chinese		Enlish		Math		Average
DmTsai		80				60				92			77.33
VBird			75				55				80			70.00
Ken				60				90				70			73.33
```

上表数据主要分成 **5** 个字段，每个字段之间可以使用 **tab** 或空格进行分割。将上表存储到 **printf.txt** 文件中，后续会使用到这个文件进行练习。

由于每个字段的长度并不一样，所以要达到上表效果，就需要打印格式管理员 **printf** 来帮忙了

```bash
printf '打印格式' 实际类容
选项与参数：
 关于格式方面的几个特殊样式：
 		\a	警告剩余输出
 		\b	退格键（backspace）
 		\f	清楚屏幕（form feed）
 		\n 	输出新的一行
 		\r	Enter 按键，换行
 		\t	水平的 tab 按键
 		\v	垂直的 tab 按键
 		\xNN	NN 为两位数的数字，可以转换数字称为字符
 关于 C 程序语言内，常见的变量格式：
 		%ns		n 数字，s 表示 string，也就是多少个字符
 		%ni		n 数字，i 表示 integer，多少整数数字
 		%N.nf 	n 与 N 都是数字，f 表示 floating（浮点），如果有小数，比如共 10 个位数，小数点 2 位数，则写成 %10.2f
```

下面进行练习

```bash
# 范例 1：将上面存储的 printf.txt 内容仅列出姓名与成绩，并且用 tab 分割
# 文件存储时，字段之间全部用 tab 隔开的，复制进去就变成下面展示这样了
 [mrcode@study tmp]$ cat printf.txt 
Name            Chinese         Enlish          Math            Average
DmTsai          80                              60                              92                      77.33
VBird                   75                              55                              80                      70.00
Ken                             60                              90                              70                      73.33
# 由于 printf 不是管线命令，需要通过 cat 先提取出来内容
# %s 表示不固定长度的字符串，后面跟了一个空格，并使用横向制表符 \t 来格式化
[mrcode@study tmp]$ printf '%s \t %s \t %s \t %s \t %s \t \n ' $(cat printf.txt)
Name     Chinese         Enlish          Math    Average         
 DmTsai          80      60      92      77.33   
 VBird   75      55      80      70.00   
 Ken     60      90      70      73.33 
```

可以看到上述的效果虽然好多了，但是还是没有对齐。可能是由于 **Chinese** 比其他的长度要长，导致对不齐，那么下面来固定长度

```bash
# 范例 2：将上述第二行以后，分别以字符串、整数、小数点来显示
# grep -v Name 排除包含 Name 字符的行
[mrcode@study tmp]$ printf '%10s %5i %5i %5i %8.2f \n' $(cat printf.txt | grep -v Name)
    DmTsai    80    60    92    77.33 
     VBird    75    55    80    70.00 
       Ken    60    90    70    73.33 
# 由于这里是格式化数字，所以第一行无法使用这里的表达式，如果使用将得到数字 0 的展示
# 展示效果好了很多
 %10s：这一个字段永远显示 10 个字符宽度，不足的用空格补位
 %8.2f：表示 00000.00
```

**printf** 除了可以格式化处理之外，还可以根据 ASCII 的数字与图形对应来显示数据，如下

```bash
# 范例 3： 列出 16 进制 45 代表的字符是什么
[mrcode@study tmp]$ printf '\x45\n'
E
# 可以将数值转换为字符，如果你会写 script 的话
# 可以测试下，20~80 之间的数值表示的字符是什么
```

**printf** 使用相当广泛，包括后面提到的 **awk** 以及在 **c** 程序语言中使用的屏幕输出，都是利用 **printf**。

**printf** 使用场景就是格式化输出，如果你要写自己的软件，把信息漂亮的输出到屏幕的话，可是很有用的

### awk：好用的数据处理工具

- **sed**：常常用于一整行的处理
- **awk**：倾向于将一行分成数个字段来处理

因此，**awk** 适合处理小型的数据处理。

```bash
awk '条件类型1{动作1} 条件类型2{动作2} ...' filename

awk 后可以跟文件，也可以接受前个指令的 standard output
awk 主要处理每一行的字段内的数据，他默认的分隔符为「空格键」或「tab 键」
```

```bash
# 范例：使用 last 将登录者数据取出来
[mrcode@study tmp]$ last -n 5		# 取出前 5 行
mrcode   pts/1        192.168.0.105    Wed Jan 15 22:20   still logged in   
mrcode   pts/0        192.168.0.105    Wed Jan 15 22:20   still logged in   
reboot   system boot  3.10.0-1062.el7. Wed Jan 15 22:19 - 23:05  (00:45)    
mrcode   pts/1        192.168.0.105    Mon Jan 13 22:51 - 23:13  (00:22)    
mrcode   pts/0        192.168.0.105    Mon Jan 13 22:51 - 23:13  (00:22) 

# 若要取出账户与登录 IP ，且账户与 IP 之间以 tab 隔开，可以这样写
[mrcode@study tmp]$ last -n 5 | awk '{print $1 "\t" $3}'
mrcode  192.168.0.105
mrcode  192.168.0.105
reboot  boot
mrcode  192.168.0.105
mrcode  192.168.0.105
        
wtmp    Fri
# 由于每一行数据都需要处理，所以不需要有条件类型
# 通过 print 功能将数据列出来
# 第 3 行数据被误判了，第二个字段中包含了空格
# 那么 $1 开始的变量表示哪一个字段，要注意的是：$0 表示整行数据

```

对于上面示例，**awk** 的处理流程是：

1. 读入第一行，并将第一行的内容填入 `$0、$1...` 变量中
2. 依据 条件类型 的限制，判断是否需要进行后面的 动作
3. 做完所有的动作与条件类型
4. 若还有后续的「行」数据，则重复上面 **1~3** 步骤，直到所有数据都处理完为止

**awk** 是「以行为一次处理的单位」而「以字段为最小的处理单位」，那么 **awk** 中还提供了以下变量信息

| 变量名称 |               含义                |
| :------: | :-------------------------------: |
|    NF    |   每一行（`$0`）拥有的字段总数    |
|    NR    | 目前 awk 所处理的是「第几行」数据 |
|    FS    |    目前的分割字符，默认是空格     |

继续上面 **last -n 5** 的例子来做说明

```bash
# 想要列出每一行的账户：就是 $1
# 列出目前处理的行数：NR 变量
# 该行有多少字段：NF 变量
# 注意：在 awk 的格式内使用 print 打印时，非变量部分需要用双引号引用起来，因为 awk 动作是以单引号的
[mrcode@study ~]$ last -n 5 | awk '{print $1 "\t lines:" NR "\t columns:" NF}'
mrcode   lines:1         columns:10
mrcode   lines:2         columns:10
reboot   lines:3         columns:11
mrcode   lines:4         columns:10
mrcode   lines:5         columns:10
         lines:6         columns:0
wtmp     lines:7         columns:7
# 注意 NF 等变量不需要有 $ 并且需要大写

```

### awk 的逻辑运算字符

既然有「条件」，那么就有逻辑运算符号

| 运算单元 |  代表意义  |
| :------: | :--------: |
|   `>`    |    大于    |
|   `<`    |    小于    |
|   `>=`   | 大于或等于 |
|   `>=`   | 小于或等于 |
|   `==`   |  **等于**  |
|   `!=`   |   不等于   |

范例：在 **/etc/passwd** 中是以冒号「:」来分割字段的，第一个字段为账户，第三字段则是 **UID**

```bash
# 查阅 第三栏小于 10 以下的数据，并且仅列出账户与第三栏
# FS 是字段分隔符
[mrcode@study ~]$ cat /etc/passwd | awk '{FS=":"} $3 < 10 {print $1 "\t" $3}'
root:x:0:0:root:/root:/bin/bash 
bin     1
daemon  2
adm     3
lp      4
sync    5
shutdown        6
halt    7
mail    8

```

第一行，没有生效是为啥呢？在 **awk** 中，在上述定义中，**FS** 仅能在第二行开始，

```bash
# 需要使用关键字 BEGIN，对应的还有 END
[mrcode@study ~]$ cat /etc/passwd | awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t" $3}'
root    0
bin     1
daemon  2

```

使用 **awk** 的计算功能，比如有如下的数据 **pay.txt**

```bash
Name		1st		2nd		3th
Mrcode	2300	2400	2500
DMTsai	2100	2000	2300
Mrcode2	4300	4200	4100

```

```bash
# 计算每个人的总额，而且还要格式化输出
 - 第一行是说明，不需要计算，所以需要使用条件 NR=1 时再处理
 - 第二行才开始计算，NR >=2 才处理

[mrcode@study tmp]$ cat pay.txt | 
> awk 'NR==1 {printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" }
> NR>=2 {total = $2 + $3 + $4 ; printf "%10s %10d %10d %10d %10.2f\n",$1,$2,$3,$4,total}'
      Name        1st        2nd        3th      Total
    Mrcode       2300       2400       2500    7200.00
    DMTsai       2100       2000       2300    6400.00
   Mrcode2       4300       4200       4100   12600.00

为了方便复制，这里粘贴上完整的一行命令：cat pay.txt |  awk 'NR==1 {printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" } NR>=2 {total = $2 + $3 + $4 ; printf "%10s %10d %10d %10d %10.2f\n",$1,$2,$3,$4,total}'

# 现在来分解上面指令
# 1. 在 awk 中，非变量需要使用双引号引用起来
# 2. 使用 printf 时，需要加上 \n 才能换行
# 下面的含义是，当是第一行的时候，执行打印个格式化，前面是格式化表达式
# 后面用逗号分割，给出对应内容，这里给出了 1~4 个字段，并新增了一个 total 字段
[mrcode@study tmp]$ cat pay.txt | awk 'NR==1 {printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"total"}'
      Name        1st        2nd        3th      total

# 对于计算的讲解
# 1. 在{} 动作中可以设置变量，进行运算；这里设置了一个 total 变量，并把 1~3 个字段相加
# 2. 由于这里有多个指令，所以需要使用冒号 「;」 进行分割
# 3. 使用 printf 常规打印，第 5 个字段引用了动作内设置的变量 total，记住 awk 中引用变量不需要使用 % 符号
[mrcode@study tmp]$ cat pay.txt | awk 'NR>=2 {total=$1+$2+$3 ; printf "%10s %10d %10d %10d %10.2f\n",$1,$2,$3,$4,total}'
    Mrcode       2300       2400       2500    4700.00
    DMTsai       2100       2000       2300    4100.00
   Mrcode2       4300       4200       4100    8500.00

# 那么上面两条是针对各自条件进行处理的，相当于 if 语句；多个条件动作之间使用空格分割；链接起来就完成了

```

利用 **awk** 可以帮助我们处理很多日常工作了，在 **awk** 的输出格式中，常常会以 **printf** 来辅助。另外在 **{}** 动作内，也支持 **if**(条件) 语句。那么上面的指令可以使用 **if** 来做，如下

```bash
cat pay.txt | awk '{if(NR==1) printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" } NR>=2 {total = $2 + $3 + $4 ; printf "%10s %10d %10d %10d %10.2f\n",$1,$2,$3,$4,total}'

 
```

笔者没有感觉这个 **if** 有多方便啊？

另外，**awk** 还可以进行循环计算，不过这个属于比较进阶的单独课程了

## 文件比对工具

通常会在同一个软件包的不同版本之间，比较配置文件与原始文件的差异的时候，就会用到文件对比。

很多时候所谓的对比，通常是用在 **ASCII** 纯文本的比对。常见的指令有 **diff**，还可以使用 **cmp** 来对比非纯文本。同时也可以使用 **diff** 建立分析文档，以处理补丁 **patch** 功能的文件

### diff

**diff** 用在比对两个文件之间的差异，以行为单位来比对的。一般是用在 **ASCII** 纯文本文件的比对上。

比如：将 **/etc/passwd** 删除第 4 行，第 6 行则替换为「**no six line**」，新文件放置到 **/tmp/test** 里，该如何做？

```bash
# 创建测试目录
[mrcode@study tmp]$ mkdir -p /tmp/testpw
[mrcode@study tmp]$ cd /tmp/testpw/
[mrcode@study testpw]$ cp /etc/passwd passwd.old 
# sed -e 直接在命令行模式上修改；d 是删除，c是替换；前面 sed 中有讲到过的
# 这里把修改后的内容存到了 passwd.new 文件中
# sed 中有超过两个以上的动作时需要加 -e
[mrcode@study testpw]$ cat /etc/passwd | sed -e '4d' -e '6c no six line' > passwd.new
```

```bash
diff [-bBi] from-file to-file
选项与参数：

from-file：文件名，原始对比文件
to-file：文件名，目的比较文件
注意：两个文件，都可以使用 - 表示，- 代表 standard input

-b：忽略一行当中，仅有多个空白的差异；例如：“about me“ 与 “about         me” 视为相同
-B：忽略空白行的差异
-i：忽略大小写的不同

```

```bash
# 范例 1：比对 passwd.old passwd.new 文件
[mrcode@study testpw]$ diff passwd.old passwd.new 
4d3						# 左边第 4 行被删除(d)掉了，基准是右边第 3 行
< adm:x:3:4:adm:/var/adm:/sbin/nologin				# 列出了左边被删除的那一行内容
6c5						# 左边第 6 行，被替换（c）成右边文件的第 5 行
< sync:x:5:0:sync:/sbin:/bin/sync			# 左边文件第 6 行内容
---	
> no six line								# 右边文件第 5 行内容
# 注意这里的，左边第 4 行被删除意思是：左边文件是完整的，右边是修改之后的，右边与左边对比，原来的第 4 行被删除了

```

如果用 **diff** 去对比两个完全不相干的文件，是对比不出来什么的；另外 **diff** 还可以对比整个目录下的差异

```bash
# 范例：了解一下不同的开机执行等级（runlevel）内容有啥不同？假设你已经知道执行等级 0 与 5的启动脚本分别放置到 /etc/rc0.d 及 /etc/rc5.d 则可以对比下
[mrcode@study testpw]$ diff /etc/rc0.d/ /etc/rc5.d/
只在 /etc/rc0.d/ 存在：K90network
只在 /etc/rc5.d/ 存在：S10network
```

### cmp

**cmp** 主要也是对比两个文件，主要利用字节单位去对比

```bash
cmp [-l] file1 file2
-i：将所有的不同点的字节处都列出来。因为 cmp 预设仅会输出第一个发现的不同点

    
```

```bash
# 范例 1：用 cmp 比较 passwd.old 与 passwd.new
[mrcode@study testpw]$ cmp passwd.old passwd.new 
passwd.old passwd.new 不同：第 106 字节，第 4 行
```

### patch

**patch** 与 **diff** 可配合使用，**diff** 比较出不同，而 **patch** 则可以将「旧文件升级为新的文件」。

1. 先比较新旧版本的差异
2. 将差异制作成补丁文件
3. 再由补丁文件更新旧文件

```bash
# 范例 1：以 /tmp/testpw 内的 passwd.old 与 passwd.new 制作补丁文件
[mrcode@study testpw]$ diff -Naur passwd.old passwd.new > passwd.patch
[mrcode@study testpw]$ cat passwd.patch 
--- passwd.old	2020-01-17 15:58:55.405462402 +0800			# 新旧文件的信息
+++ passwd.new	2020-01-17 16:01:03.115462402 +0800
@@ -1,9 +1,8 @@		# 新旧文件要修改数据的界定范围，旧文件在 1-0 行，新文件在 1-8 行
 root:x:0:0:root:/root:/bin/bash
 bin:x:1:1:bin:/bin:/sbin/nologin
 daemon:x:2:2:daemon:/sbin:/sbin/nologin
-adm:x:3:4:adm:/var/adm:/sbin/nologin			# 左侧文件删除
 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
-sync:x:5:0:sync:/sbin:/bin/sync				# 左侧文件删除
+no six line									# 右侧新加入
 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
 halt:x:7:0:halt:/sbin:/sbin/halt
 mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
 
 # 这里怎么理解？ 可以理解为 old 文件是基准文件
 # 根据这里的基准文件，看到 - 就剪掉，看到 + 就增加；执行完成后，则会得到 new 这个文件；
 # 并且补丁中限制了行数。
```

将 **passwd.old** 同步为 **passwd.new** 相同的内容，

```bash
# 由于系统未预装 patch 软件，需要将之前的 iso 镜像文件挂载
# 在虚拟机上找到顺序为 0 的控制器位置，选择 iso 文件，设备就能被 linux 找到了
[root@study ~]# mount /dev/sr0 /mnt/
mount: /dev/sr0 写保护，将以只读方式挂载
[root@study ~]# rpm -ivh /mnt/Packages/patch-2.*
警告：/mnt/Packages/patch-2.7.1-11.el7.x86_64.rpm: 头V3 RSA/SHA256 Signature, 密钥 ID f4a80eb5: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:patch-2.7.1-11.el7               ################################# [100%]
[root@study ~]# umount /mnt/
[root@study ~]# exit
# 透过上述方式安装所需软件
```

语法

```bash
patch -pN < patch_file  # 更新
patch -R -pN < patch_file  # 还原

选项与参数：
-p：后面可以接 取消几层目录 的意思
-R：代表还原，将新的文件还原成原来的旧文件
```

```bash
# 范例 2：将刚刚制作出来的 patch file 用来更新旧版本数据
[mrcode@study testpw]$ patch -p0 < passwd.patch 
patching file passwd.old
[mrcode@study testpw]$ ll passwd.*
-rw-rw-r--. 1 mrcode mrcode 2266 1月  17 16:01 passwd.new
-rw-r--r--. 1 mrcode mrcode 2266 1月  17 16:50 passwd.old	# 文件大小和new文件一样了
-rw-rw-r--. 1 mrcode mrcode  480 1月  17 16:38 passwd.patch

# 范例 3：恢复旧文件内容
[mrcode@study testpw]$ patch -R -p0 < passwd.patch 
patching file passwd.old
[mrcode@study testpw]$ ll passwd.*
-rw-rw-r--. 1 mrcode mrcode 2266 1月  17 16:01 passwd.new
-rw-r--r--. 1 mrcode mrcode 2323 1月  17 16:52 passwd.old

```

这里为什么会使用 `-p0` ？因为两个文件在同一个目录下，因此不需要减去目录。如果是整体目录比对（**diff** 旧目录 新目录）时，就要依据建立 **patch** 文件所在目录来进行目录删减

更详细的 **patch** 用法在后续的第二十章「原始码编译」

### 文件打印准备：pr

在图形界面中的文字处理软件，打印时可以选择每一页的标头和页码，在文字界面下，可以使用 **pr** 来实现，由于 **pr** 参数实在太多了，这里使用最简单的方式来处理

```bash
# 打印 /etc/man_db.conf
[mrcode@study testpw]$ pr /etc/man_db.conf 


2018-10-31 04:26                /etc/man_db.conf                 第 1 页


# 
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath(5) man page.
```

最上面的一行就是 **pr** 处理之后的效果。依次是：文件时间、文件名、页码

# 学习 Shell Scripts

基本上 **shell script** 类似早期的批处理文件，将一些指令汇总起来一次执行，但是 **shell script** 拥有更强大的功能，可以进行类似程序的编写，并且不需要经过编译就可以执行。

我们可以通过 **shell script** 来简化我们日常的管理工作，而且整个 **Linux** 环境中，一些服务（**services**）的启动都是透过 **shell script** 的。

所以 **shell scripts** 是很重要的课程

## 什么是 Shell Scripts

Shell Scripts ：程序化脚本；

```
- shell ：在前面第十章中讲过的 BASH，是一个文字接口让我们与系统沟通的一个工具接口。
- script：脚本
    
```

那么就是针对 **shell** 写的脚本

**shell script** 可以简单的看成是批处理文件，也可以称为一种程序语言，该语言是利用 **shell** 与相关工具指令，所以不需要编译即可执行，且有不错的 **debug** 工具，所以，它可以帮助系统管理员快速的管理好主机

## 为什么要学习 shell scripts?

简单说：想要玩清楚 **Linux** 的来龙去脉，**shell script** 是必须的知识，因为：

- 自动化管理的重要依据

  管理一部主机每天要进行的任务就有：

  - 查询登录文件
  - 追踪流量
  - 监控用户使用主机状态
  - 主机各项硬件设备状态
  - 主机软件更新查询

  等等，这里白不包括有其他使用者突然的要求了。这些工作进行又可以分为：

  1. 自行手动处理
  2. 写个简单的程序来帮你每日「自动处理分析」

- 追踪与管理系统的重要工作

   在 **CentOS 6.x** 以前的版本中，系统的服务（**services**）启动的接口是在 `/etc/init.d` 目录下，所有文件都是 **scripts**；另外，包括开机（**booting**）过程也是利用 **shell script** 来帮忙搜索系统的相关设置数据，再代入各个服务的设置参数。

   比如：想要重新启动系统注册表，可以使用 `/etc/init.d/rsyslogd restart` **rsyslogd** 文件就是 **script** 了

   另外，比如 **Mysql** 数据库服务启动时，有可能就在 **script** 中主动以「空密码」尝试登陆 **Mysql**，为了安全性，那么你就可以修改这个 **script** 文件。

   虽然 `/etc/init.d/*` 这个脚本目前的启动方式（**systemV**）已经被新一代的 **systemd** 所代替了（从 CentOS 7 开始），但是很多的个别服务在管理他们的服务启动方面，还是使用 **shell script** 的机制

- 简单入侵检测功能

  当系统有异常状态时，大多会讲这些记录在「系统注册表」中（系统记录器），那么就可以在固定的几分钟内主动的去分析注册表文件，若察觉有问题，就立刻通知管理员，或者是立刻加强防火墙的规则，如此一来，主机就能过达到自我保护的聪明学习功能了。

  比如：可以通过 **shell script** 分析「当该封包尝试几次还是联机失败之后，就抵挡住该 **IP**」之类的动作

- 连续指令单一化

  简单说，**script** 最简单的功能就是，将一批指令写入 **script** 中，达到执行一个文件就能下达一批指令的目的。

  比如：防火墙连续规则（**iptables**）、开机加载程序的项目（<B>/etc/rc.d/rc.local</B>） 等等

- 简易的数据处理

  前面几章讲解的如 **awk** 等指令就可以用来处理简单的数据。配合各种指令来达到处理数据的目的

- 跨平台支持与学习历程较短

  几乎所有的 **Unix Like** 上都可以运行 **shell script**，连 **MS Windows** 系列也有相关的 **script** 仿真器可以用

虽然 **shell script** 号称是程序，实际上，**shell script** 处理数据的速度上还是不够快，因为用的是外部的指令与 **bash shell** 的一些默认工具，所以常常去调用外部的函数库，因此指令周期上面比不上传统的程序语言

所以，**shell script** 用在系统管理上是很好的一项工具，但是用在处理大量数值运算上，就不行了，速度较慢，使用 **CPU** 资源较多，造成主机资源的分配不良。我们通常利用 **shell script** 来处理服务器的侦测就比较合适

## 第一支 script 的编写与执行

**shell script** 是纯文本文件，可以在里面一次性执行多个指令，或者是利用一些运算与逻辑判断来帮助我们达成某些功能。所以需要具备 **bash** 指令下达相关知识（第四章中开始下达指令中讲过），除此之外，还有以下知识需要了解：

1. 指令的执行是从上而下、从左而右的分析与执行
2. 指令的下达：指令、选项与参数间的多个空白都会被忽略掉
3. 空白行也将被忽略，并且「**tab**」按键锁推开的空白行同样视为空格
4. 如果读取到一个 **enter** 符号（**CR**），就尝试开始执行改行（或该串）命令
5. 如果一行内容太多，则可以使用「`\[Enter]`」来延伸至下一行
6. `#`可作为批注。任何加在 `#`后面的文字将被视为批注文字而被忽略

假设现在存在一个 **script** 是 `/home/mrcode/shell.sh`，有如下的方式执行这个文件

- 直接指令下达：**shell.sh** 文件必须有可读与执行权限（rx）
  - 绝对路径：使用 `/home/mrcode/shell.sh`执行
  - 相对路径：假设工作目录在 `/home/mnrcode`，就使用 `./shell.sh`执行
  - 变量「**PATH**」功能：将 **shell.sh** 放在 **PATH** 指定目录内，例如 ~`/bin/`
- 以 **bash** 程序来执行：`bash shell.sh` 或 `sh shell.sh` 执行

至于那个相对路径 `./shell.sh` 为什么需要这样，是因为 [路径与指令搜索顺序](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/04.html) 的关系；

`sh shell.sh` 为啥可以执行？

```bash
[mrcode@study ~]$ type -a sh
sh is /usr/bin/sh
[mrcode@study ~]$ ll /usr/bin/sh
lrwxrwxrwx. 1 root root 4 Jan 17 14:32 /usr/bin/sh -> bash

```

可以看到 **sh** 是 **bash** 的链接文件，同时还可以使用参数 `-n 和 -x` 来检查与追踪 **shell.sh** 的语法是否正确

### Hello World

先来一个 **Hello World** 脚本，再来逐步说明

```bash
[mrcode@study ~]$ pwd
/home/mrcode
[mrcode@study ~]$ mkdir bin; cd bin
[mrcode@study bin]$ vi hello.sh

#!/bin/bash
# Program:
#       This program shows "Hello World" in your screen.
# HIstory:
# 2020/02/19    mrcode  first release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo -e "Hello World! \a \n"
exit 0
```

在本章中，请将所有编写的 **script** 放置到你家目录的 `~/bin` 目录内，未来比较好管理，针对如上脚本的写法，分段说明：

1. `#!/bin/bash`：宣告这个 **script** 使用的 **shell** 名称

   因为我们使用的是 **bash**，所以必须以 `#!/bin/bash` 来声明该文件内的语法使用 **bash** 语法。

   当这个程序被执行时，能够加载 **bash** 的相关环境配置文件（一般来说是 **non-login shell** 的 `~/.bashrc`），并且执行 **bash** 来使指令能够执行。很多情况下导致无法执行可能就是因为这一行的原因，系统无法判断该 **sh** 文件使用什么 **shell** 来执行

2. 程序内容说明

   整个文件中，除了第一行的 `#!` 是用来声明 **shell** 之外，其他的 `#`都是批注信息。一般来说，建议一定要养成说明该 **script** 的：

   1. 内容与功能
   2. 版本信息
   3. 作者与联络方式
   4. 建档日期
   5. 历史记录

   等等，这将有助于未来程序的改写与 **debug**

3. 主要环境变量的声明

   建议务必将一些重要的环境变量设置好，**PATH** 与 **LANG**（输出相关信息时） 是当中最重要的，如此一来就可以直接下达外部指令，而不用写绝对路径，比较方便

4. 主要程序部分

   在本例中，就是 **echo** 那一行

5. 执行结果告知（定义回传值）

   [指令回传值](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/05.html#指令回传值-与-和) 中讲解到，可以使用 `$?` 来观察，那么可以利用 **exit** 这个指令来让程序中断，并且回传一个数值给系统。

   本例中使用的是 `exit 0`，表示离开 **script** 并且回传一个 **0** 给系统，所以执行完这个 **shell.sh** 之后，下达 `echo $?` 则可以得到 **0** 的值。

   利用这个 **exit n**（**n** 是数字）的功能，还可以自定义错误信息，让这支程序变得更加智能

执行与观察结果

```bash
# 观察权限，目前没有 x 执行权限
[mrcode@study bin]$ ll
total 4
-rw-rw-r--. 1 mrcode mrcode 239 Jan 19 11:25 hello.sh
# 尝试执行，报错无权限
[mrcode@study bin]$ ./hello.sh
-bash: ./hello.sh: Permission denied
# 添加执行权限
[mrcode@study bin]$ chmod u+x hello.sh 
[mrcode@study bin]$ ll
total 4
-rwxrw-r--. 1 mrcode mrcode 239 Jan 19 11:25 hello.sh
# 执行脚本
[mrcode@study bin]$ ./hello.sh 
Hello World!  

# 查看回传值
[mrcode@study bin]$ echo $?
0
```

## 编写 shell script 的良好习惯建立

一个良好习惯的养成至关重要，往往最开始时最容易忽视这部分的，觉得程序只要写出来就可以了，但是随着时间的拉长，不断的维护和修改。后续维护就可能出现问题

比如：作者管理很多计算机，由于太懒，经常同一个程序在不同的主机上进行修改，最最后也不知道哪一个程序是最新的，其中做了什么修改，又为什么做那样的修改。

所以，在写程序时，需要仔细的将程序的设计过程记录下来，而且还会记录一些历史记录，这样会导致维护成本降低

另外，在一些环节设置上面，毕竟每个人的环境都不相同，为了取得较佳的执行环境，一般都会预先定义一些一定会被用到的环境变量，例如上面的 **PATH**。因此养成良好的 **script** 编写习惯，建议在每个 **script** 文件头记录如下信息：

- 功能描述
- 版本信息
- 作者与联系方式
- 版权信息
- 历史记录（**History**）
- **script** 内较为特殊的指令，使用「绝对路径」方式来下达
- **script** 运作时需要的环境变量预先声明与设置

除了这些信息之外，在关键和难理解的代码部分添加批注信息。另外推荐代码编排格式使用 「巢状方式」，使用 **tab** 来缩进。编写 **script** 的工具是 **vim** 而不是 **vi**，因为 **vim** 有额外的语法校验机制

## 简单的 Shell Script 练习

本章 范例中，实现的方式很多，建议先自行编写，再参考例子，才能加深概念

### 简单范例

本小节范例在很多脚本程序中都会用到，而且简单

### 对谈式脚本：变量类容由用户决定

在很多场景中，需要用户输入一些内容，让程序可以顺利运行。比如，安装软件时，让用户选择安装目录；

[BASH 中的变量读取指令 read](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/02.mdl#read) ，那么以 **read** 指令的用途实现：

1. 用户输入 **first name**
2. 用户输入 **last name**
3. 最后在屏幕上显示：**You full name is：xxx**

```bash
[mrcode@study bin]$ vim read.sh
#!/bin/bash
read -p 'first name: ' firstName
read -p 'last name: ' lastName
echo "You full name: ${firstName}${lastName}"
exit 0
```

```bash
# 增加执行权限
[mrcode@study bin]$ chmod a+x read.sh
# 执行
[mrcode@study bin]$ ./read.sh 
first name: zhu
last name: mrcode
You full name: zhumrcode

```

下面是书上的程序

```bash
vim showname.sh
#!/bin/bash
# Program：
#	用户输入姓名，程序显示出输入的姓名
# History：
#	2020/01/19	mrcode	first release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "Please input you first name: " firstname		# 提示使用者输入
read -p "Please input you last name: " lastname			# 提示使用者输入
# -e 开启反斜杠转移的特殊字符显示，比如下面的 \n 换行显示
echo -e “\n Your full name is: ${firstname}${lastname}”	# 结果由屏幕输出	
```

```bash
# 执行结果
[mrcode@study bin]$ ./showname.sh 
Please input you first name: zhu
Please input you last name: mrcode

 Your full name is: zhumrcode
```

笔者小结：可以看到上面这个脚本，增加了一个良好的习惯，就是脚本说明等信息

### 随日期变化：利用 date 进行文件的建立

考虑一个场景，每天备份 **MySql** 的数据文件，备份文件名以当天日期命名，如 `backup.2020-01-19.data`.

重点是 `2020-01-19` 是怎么来的？范例需求如下：

1. 用户输入一个文件名前缀
2. 创建出以日期为名的三个空文件（通过 **touch** 指令），生成 前天、昨天、今天 日期，及格式为：**filename_2020-01-19**

```bash
vim create_3_filename.sh
#!/bin/bash
# Program: 
#       用户输入文件名前缀，生成前天、昨天、今天的三个空文件
# History：
#       2020/01/19      mrcode          first release
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "将使用 ‘touch’ 命令创建 3 个文件"
read -p "请输入文件名：" fileuser

# 容错，使用变量功能判定与赋值默认值
filename=${fileuser:-"filename"}

# date 命令的使用
date1=$(date --date='2 days ago' +%Y-%m-%d)		# 两天前的日期，并格式化显示
date2=$(date --date='1 days ago' +%Y-%m-%d)	
date3=$(date +%Y-%m-%d)

file1="${filename}_${date1}"
file2="${filename}_${date2}"
file3="${filename}_${date3}"

# 在这里其实可以直接拼接文件名
touch "${file1}"
touch "${file2}"
touch "${file3}"
```

这里使用了变量的赋值相关功能，详参考：[变量功能](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/02.html#变量的测试与内容替换)

运行测试

```bash
[mrcode@study bin]$ ./create_3_filename.sh 
将使用 ‘touch’ 命令创建 3 个文件
请输入文件名：mrcode
[mrcode@study bin]$ ll
总用量 16
-rwxrwxr-x. 1 mrcode mrcode 677 1月  19 14:15 create_3_filename.sh
-rwxrwxr-x. 1 mrcode mrcode 239 1月  19 11:25 hello.sh
-rw-rw-r--. 1 mrcode mrcode   0 1月  19 14:15 mrcode_2020-01-17
-rw-rw-r--. 1 mrcode mrcode   0 1月  19 14:15 mrcode_2020-01-18
-rw-rw-r--. 1 mrcode mrcode   0 1月  19 14:15 mrcode_2020-01-19
# 一次正常输入文件名，一次直接按 enter 按键完成输入，查看是否达到默认赋值等功能
```

### 数值运算：简单的加减乘除

在变量功能课程中讲解到，需要使用 [declare](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/02.html#declare-、-typeset) 来定义变量为正数才能进行计算，此外，也可以利用 `$((计算表达式))` 来进行数值运算，可惜的是，**bashe shell** 预设仅支持整数数据。

范例需求：

1. 要求用户输入两个变量
2. 将两个变量相乘后输出到屏幕

下面是笔者自己写的

```bash
#!/bin/bash
# Program:
#       用户输入 2 个整数；输出相乘后的结果
# History：
#       2020/01/19      mrcode          first release
read -p '请输入第一个整数：' intUser1
read -p '请输入第二个整数：' intUser2
declare -i int1=${intUser1}
declare -i int2=${intUser2}

echo -e  "\n ${int1} x ${int2} = $((int1*int2))"

```

测试输出

```bash
[mrcode@study bin]$ ./multiplying.sh 
请输入第一个整数：2
请输入第二个整数：3

 2 x 3 = 6
```

其实用下面这样的方式来定义和输出

```bash
read -p '请输入第一个整数：' intUser1
read -p '请输入第二个整数：' intUser2
total=$((${intUser1}*${intUser2}))		# 使用 $((运算内容)) 方式计算
# declare -i total=${intUser1}*${intUser2}		# 还可以使用此种方式
echo -e  "\n ${intUser1} x ${intUser2} = ${total}"
 
```

建议用 `var = $((计算内容))` 方式来计算，此种方式简单。比如

```bash
# 取余数
[mrcode@study bin]$ echo $((3 % 2))
1

# 对于小数，可以使用 bc 指令来协助
[mrcode@study bin]$ echo $((3 / 2)); echo "3/2" | bc -l
1
1.50000000000000000000
```

### 数值运算：通过 bc 计算 pi

bc 提供了一个计算 pi 的公式： `pi=$(echo "scale=10; 4*a(1)" | bc -l)`，此计算公式可以通过 `man bc | grep 'pi'` 定位到相关文档。这里的 **scale** 是计算 **pi** 的精度，越高则利用到的 **cpu** 资源越多，计算时间越长。

好了，了解到怎么启用 **pi** 计算，这里要求用户输入 **scale** 进行计算 **pi** 值，并输出显示

```bash
vim cal_pi.sh
#!/bin/bash
# Program：
#	用户输入 scale 的值，程序计算出 scale 精度的 pi 值，并显示
# History：
#	2020/01/19		mrcode		first release
# PATH 常规赋值
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p '请输入 scale 的值（10~10000）？' checking
num=${checking:-"10"}

echo -e '\n开始计算 pi 的值'
time echo "scale=${num}; 4*a(1)" | bc -l
```

测试输出

```bash
[mrcode@study bin]$ ./cal_pi.sh 
请输入 scale 的值（10~10000）？20

开始计算 pi 的值
3.14159265358979323844

real	0m0.002s
user	0m0.000s
sys	0m0.001s
```

## script 的执行方式差异（source、sh script、./script）

不同的方式执行执行会造成不一样的结果，尤其影响 **bash** 的环境很大。

### 利用直接执行的方式来执行 script：在子程序中执行

[直接指令下达](https://zq99299.github.io/linux-tutorial/tutorial-basis/12/01.html#第一支-script-的编写与执行) 或者是利用 **bash**（**sh**）来运行脚本时，都会使用一个新的 **bash** 环境来执行脚本的指令。也就是说这种方式执行是在子程序的 **bash** 内执行的。在第十章 **BASH** 内谈到 [`export` 自定义变量转成环境变量](https://zq99299.github.io/linux-tutorial/tutorial-basis/10/02.html) 的功能时，重点在于：当子程序完成后，子程序内的各项变量或动作将会结束儿不会传回到父程序中。

```bash
# 运行上面范例的姓名打印
[mrcode@study bin]$ ./showname.sh 
Please input you first name: m
Please input you last name: q

 Your full name is: mq		#  echo -e "\n Your full name is: ${firstname}${lastname}" 打印出来了信息
[mrcode@study bin]$ echo ${fristname}${lastname}		# 但是在父程序中却没有信息
```

### 利用 source 来执行脚本：在父程序中执行

同样的测试代码，使用 **source** 就不一样了

```bash
[mrcode@study bin]$ source showname.sh 
Please input you first name: m
Please input you last name: q

 Your full name is: mq
[mrcode@study bin]$ echo ${firstname}${lastname}
mq			# 在父程序中还能获取到
```

## 善用判断

在 上一章中，提到过 `$?` 这个变量所代表的含义，以及通过 `&& 和 ||` 来判定前一个指令执行回传值对于后一个指令是否要进行的依据。

在上一章中，判定一个目录是否存在，使用了 `ll 目录 && 执行指令` 的方式来判定 **xx** 目录是否存在，从而决定后续指令是否执行，但是有更简单的方式进行条件判断，就是通过 **test** 指令

### 使用 test 指令的测试功能

**test** 指令主要用于检测文件或相关属性时的指令和比较值，比如检查 **/mrcode** 是否存在时

```bash
# -e 是检测文件是否存在的选项
[root@cloud-08 script]# test -e /mrcode
```

上面命令没有任何输出值

```bash
[root@cloud-08 script]# test -e /mrcode && echo "exist" || "not exist"
exist
# 通过与 && 或 || 可以知道是存在还是不存在了
```

要善用 man 查看该指令的信息，下面是整理翻译出来的其他选项

关于某个文件名的 **文件类型** 判断。如 `test -e filename` 标识是否存在

| 测试的标志 | 含义                                               |
| :--------: | :------------------------------------------------- |
|   **-e**   | 文件是否存在；**常用**                             |
|   **-f**   | 该文件是否存在且为文件（**file**）？**常用**       |
|   **-d**   | 该文件是否存在且为目录（**directory**）？**常用**  |
|   **-b**   | 该文件是否存在且为一个 **block device** 装置？     |
|   **-c**   | 该文件是否存在且为一个 **character device** 装置？ |
|   **-S**   | 该文件是否存在且为一个 **Socket** 文件？           |
|   **-p**   | 该文件是否存在且为一个 **FIFO**（**pipe**）文件？  |
|   **-L**   | 该文件是否存在且为一个连接文件？                   |

关于文件的 **权限** 判定。如 `test -r filename` 标识是否可读？（但 **root** 权限常有例外）

| 测试的标志 | 含义                                       |
| :--------: | :----------------------------------------- |
|   **-r**   | 该文件是否存在且具有可读权限？             |
|   **-w**   | 该文件是否存在且具有可写权限？             |
|   **-x**   | 该文件是否存在且具有可执行权限？           |
|   **-u**   | 该文件是否存在且具有 **SUID** 属性？       |
|   **-g**   | 该文件是否存在且具有 **SGID** 属性？       |
|   **-k**   | 该文件是否存在且具有 **Sticky bit** 属性？ |
|   **-s**   | 该文件是否存在且为「非空白文件」？         |

两个文件之间的比较。如 `test file1 -nt file2`

| 测试的标志 | 含义                                                         |
| :--------: | :----------------------------------------------------------- |
|  **-nt**   | （**newer than**）判断 **file1** 是否比 **file2** 新         |
|  **-ot**   | （**older than**）判断 **file1** 是否比 **file2** 旧         |
|  **-ef**   | 判断 **file1** 与 **file2** 是否是同一文件，可用在判断 **hard link** 的判定上。主要意义在判定两个文件是否均指向同一个 **inode** |

两个整数之间的判定。`test nl -eq n2`

| 测试的标志 | 含义                                  |
| :--------: | :------------------------------------ |
|  **-eq**   | 两数值相等（**equal**）               |
|  **-ne**   | 不相等（**not equal**）               |
|  **-gt**   | 大于（**greater than**）              |
|  **-lt**   | 小于（**less than**）                 |
|  **-ge**   | 大于等于（**greater than or equal**） |
|  **-le**   | 小于等于（**less than or equal**）    |

判定字符串的数据

|      测试的标志       | 含义                                                         |
| :-------------------: | :----------------------------------------------------------- |
|  **test -z string**   | 判定字符串是否为 **0**？若为空串，则为 **true**              |
|  **test -n string**   | 判定字符串是否不为 **0**？若为空串，则为 **false**；注意：**\-n** 可省略 |
| **test str1 == str2** | 是否相等，相等则为 **true**                                  |
| **test str1 != str2** | 是否不相等，相等则为 **false**                               |

多重条件判断。比如 `test -r filename -a -x filename`

| 测试的标志 | 含义                                                         |
| :--------: | :----------------------------------------------------------- |
|   **-a**   | （**and**）两状况同时成立；如：`test -r filename -a -x filename`，则 **file** 同时具有 **r** 与 **x** 权限时才为 **true** |
|   **-o**   | （**or**）任意一个成立。如：`test -r filename -o -x filename`，则 **file** 具有 **r** 或 **x** 权限时就为 **true** |
|   **!**    | 反向状态。                                                   |

总结完这么多的判定，就可以来写几个简单的例子。让用户输入一个文件名，我们判断：

1. 该文件是否存在，若不存在则给予一个「**Filename does not exist**」 提示，并中断程序
2. 若该文件存在，则判断是文件还是目录：文件输出「**Filename is regular file**」，目录输出 「**Filename is directory**」
3. 判断执行者的身份对这个文件或目录所拥有的权限，并输出权限数据

下面是笔者写的思路，代码组织方面有点糟糕。还有指令使用不太熟悉

```bash
#!/bin/bash
# Program
# 
# History
#       2020/01/19              mrcode          first relese
read -p "请输入一个文件名：" filename
# 判断是否输入了字符串
test -z ${filename} && echo "请输入一个有效的文件名！" && exit -1

# 判断该文件是否存在: 不存在输出提示信息并退出
# 特别是这里的多条指令的执行，使用 || 会很难处理，只能转成 true
test ! -e ${filename} && echo "${filename} does not exist" && exit -1

# 提示是文件还是目录
test -f ${filename} && echo "${filename} is regular file" || echo "${filename} is directory"

# 判断执行者的身份对这个文件拥有的权限，并输出
test -r ${filename} && echo "${filename} 可读"
test -w ${filename} && echo "${filename} 可写"
test -x ${filename} && echo "${filename} 可执行"
```

测试输出如下

```bash
[mrcode@study bin]$ ./file_perm.sh 
请输入一个文件名：ss
ss does not exist
[mrcode@study bin]$ ./file_perm.sh 
请输入一个文件名：/etc
/etc is directory
/etc 可读
/etc 可执行
```

书上代码如下

```bash
vim file_perm.sh
#!/bin/bash
# Program
#   User input a filename,program will check the flowing:
#	1.) exist?
#	2.) file/directory?
#	3.) file permissions
# History
#       2020/01/19              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo -e "Please input a filename,I will check the filename's type and permission. \n\n"
read -p "Input a filename ：" filename
# 判断是否输入了字符串
test -z ${filename} && echo "You MUST input a filename. " && exit 0

# 判断该文件是否存在: 不存在输出提示信息并退出
test ! -e ${filename} && echo "The filename ${filename} does not exist" && exit 0

# 开始判断文件类型与属性
test -f ${filename} && filetype="regulare file"
test -d ${filename} && filetype="directory"
test -r ${filename} && perm="readable"
test -w ${filename} && perm="${perm} writable"
test -x ${filename} && perm="${perm} executable"

# 信息输出
echo "The filename: ${filename} is a ${filetype}"
echo "And the permissions for you are : ${perm}"
```

测试输出如下

```bash
[mrcode@study bin]$ ./file_perm.sh 
Please input a filename,I will check the filename's type and permission. 


Input a filename ：ss
The filename ss does not exist
[mrcode@study bin]$ ./file_perm.sh 
Please input a filename,I will check the filename's type and permission. 


Input a filename ：/etc
The filename: /etc is a directory
And the permissions for you are : readable executable
```

自己写的脚本组织来看，除了不熟悉指令用法之外，对于程序结构的抽象不够好，对比书上的，发觉这个代码组织的不错

另外，该脚本检查权限的指令是针对运行该脚本的用户所反馈的，所以当使用 **root** 的时候，常常会发现与 **ls -l** 观察到的结果并不相同

### 利用判断符号 `[]`

除了 **test** 外，还可以使用中括号 `[]` 来判定

```bash
# 判断 ${HOME} 这个变量是否为空
[mrcode@study bin]$ [ -z "${HOME}" ]; echo $?
1
```

使用该种方式需要特别注意，因为中括号在很多地方都代表特殊符号，在 **bash** 的语法中作为 **shell** 判断时，必须要注意 **中括号的两端需要有空格符来分隔**

- 在中括号内的每个组件都需要有空格来分隔
- 在中括号内的变量，最好都以双引号括起来
- 在中括号内的常量，都好都以单或双引号括起来

看一个例子，设置一个 **name** 变量，再用中括号方式判断

```bash
[mrcode@study bin]$ name="Mrcode Tset"
[mrcode@study bin]$ [ ${name} == "Mrcode" ]
-bash: [: 参数太多
# 是因为，如果 ${name} 没有使用双引号括起来就会变成  [ Mrcode Test  == "Mrcode" ]
# 中括号内的变量是以空格来分隔的，那么这里就出现了 Mrcode Test “Mrcode” 三个比较对象了
# 那么使用 [ “${name}” == "Mrcode" ] 就变成了 [ “Mrcode Test”  == "Mrcode" ]

```

除了以上注意之外，中括号使用方式与 **test** 几乎一模一样，只是中括号比较常用在 **条件判断 if...then..fi** 的情况中。

实践范例需求如下：

1. 当执行一个程序的时候，要求用户选择 **Y** 或 **N**
2. 如果用户输入 **Y** 或 **y** 时，就显示「**Ok，continue**」
3. 如果用户输入 **N** 或 n 时，就显示「**Oh，interrupt！**」
4. 如果不是以上规定字符，则显示「**I don't know what your choice is**」

利用中括号、`&&`、`||` 来达成

```bash
vi ans_yn.sh
#!/bin/bash
# Program:
#	This program shows the user's choice
# History:
#	2020/01/20		mrcode		first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "请输入 Y/N：" yn
[ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "Ok，continue" && exit 0
[ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh，interrupt！" && exit 0
echo "I don't know what your choice is" && exit 0

```

输出测试

```bash
# 这里报错时因为 [ "${yn}" == "Y" || "${yn}" == "y" ]  中使用了 || 来达成条件判定
[mrcode@study bin]$ ./ans_yn.sh 
请输入 Y/N：n
./ans_yn.sh: 第 10 行:[: 缺少 `]'
./ans_yn.sh:行10: n: 未找到命令
./ans_yn.sh: 第 11 行:[: 缺少 `]'
./ans_yn.sh:行11: n: 未找到命令
I don't know what your choice is

# [ "${yn}" == "Y" -o "${yn}" == "y" ] 使用了 test 中的参数， -o 只要任意一个成立都算 true
# 程序正常
[mrcode@study bin]$ vim ans_yn.sh 
[mrcode@study bin]$ ./ans_yn.sh 
请输入 Y/N：n
Oh，interrupt！
[mrcode@study bin]$ ./ans_yn.sh 
请输入 Y/N：y
Ok，continue
[mrcode@study bin]$ ./ans_yn.sh 
请输入 Y/N：
I don't know what your choice is
```

### shell script 的默认变量 `$0,$1...`

指令可以带有选项与参数，如 `ls -la` 可以查看包含隐藏文件的所有属性。那么 **script** 也可以携带参数。

```bash
# 重新启动系统的网络
[mrcode@study bin]$ file /etc/init.d/network 
/etc/init.d/network: Bourne-Again shell script, ASCII text executable
# 利用 file 指令查询该文件，显示是个可执行的 shell script 文件

# 这里携带 restart 参数，如果替换成 stop 参数就是关闭该服务了
[mrcode@study bin]$ /etc/init.d/network restart 
```

**read** 是使用过程中需要手动输入，而参数是可以跟随在执行命令后的，这样就比较方便

**script** 针对参数已经设置好一些变量名称了，对应如下

```text
/path/to/scriptname		opt1	opt2	opt3	opt4
 		&0				 &1		 &2		 &3		 &4
```

除了这些数字的变量参数外，还有一些较为特殊的变量可以使用

- `$#`：代表后接的参数「个数」，以上表为例这里显示「**4**」
- `$@`：代表 「`"&1" "&2" "&3" "&4"`」 的意思，每个变量是独立的（用双引号括起来）
- `$*`：代表「`"&1c&2c&3c&4"`」，其中 **c** 为分隔符，默认为空格，所以本例中代表「`"&1 &2 &3 &4"`」

`$@ 与 $*` 基本上还是有所不同，一般使用 `$@` 较多。

范例需求：输出如下数据

- 程序的文件名
- 共有几个参数
- 若参数小于 **2** ，则告知使用者参数数量太少
- 全部的参数内容
- 第一个参数
- 第二个参数

```bash
vi prit_info.sh
#!/bin/bash
# Program:
#       输出脚本文件名，与相关参数信息
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo $0
echo $#
# 这样写，语法是错误的，要记得这里是使用 test 里面的语法
# 并且，不能用 ${变量} 的方式来写
[ "${$#}" < "2" ] && echo "参数数量太少，比如大于等于 2 个" && exit 0
echo $@
echo $1
echo $2

```

输出

```bash
[mrcode@study bin]$ ./print_info.sh 
./print_info.sh
0
./print_info.sh:行11: 2: 没有那个文件或目录



[mrcode@study bin]$ ./print_info.sh a b
./print_info.sh
2
./print_info.sh:行11: 2: 没有那个文件或目录
a b
a
b
```

以下是书上的写法

```bash
vi how_paras.sh
#!/bin/bash
# Program:
#       输出脚本文件名，与相关参数信息
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "The script name is			==> $0"
echo "Total parameter number is		==> $#"
[ "$#" -lt 2 ] && echo "参数数量太少，比如大于等于 2 个" && exit 0
echo "Your whole parameter is		==> '$@'"
echo "The 1st parameter 			==> $1"
echo "The 2nd parameter 			==> $2"
```

输出测试

```bash
[mrcode@study bin]$ ./how_paras.sh 
The script name is			==> ./how_paras.sh
Total parameter number is		==> 0
参数数量太少，比如大于等于 2 个

[mrcode@study bin]$ ./how_paras.sh a b
The script name is			==> ./how_paras.sh
Total parameter number is		==> 2
Your whole parameter is		==> 'a b'
The 1st parameter 			==> a
The 2nd parameter 			==> b
```

### shift：造成参数变量位置偏移

先修改下上面的范例，`how_paras.sh` 先来看看效果什么是偏移

```bash
vi how_paras.sh
#!/bin/bash
# Program:
#       Program shows the effect of shift function
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "Total parameter number is		==> $#"
echo -e "Your whole parameter is		==> '$@' \n"

shift	# 进行第一次 一个变量的 shift
echo "Total parameter number is		==> $#"
echo -e "Your whole parameter is		==> '$@' \n"

shift 3	# 进行第二次 三个变量的 shift
echo "Total parameter number is		==> $#"
echo "Your whole parameter is		==> '$@'"
```

输出如下

```bash
[mrcode@study bin]$ ./how_paras.sh a b c d e f
Total parameter number is		==> 6		# 位偏移的参数数量，是 6 个
Your whole parameter is		==> 'a b c d e f'

Total parameter number is		==> 5		# 偏移一次后，只剩下 5 个，并且第一个参数 a 不见了
Your whole parameter is		==> 'b c d e f'

Total parameter number is		==> 2		# 第二次偏移掉 3 个后，b c d 不见了
Your whole parameter is		==> 'e f'

# 再来看看如果参数不够偏移会出现什么情况
[mrcode@study bin]$ ./how_paras.sh a b c	# 给 3 个参数
Total parameter number is		==> 3
Your whole parameter is		==> 'a b c' 

Total parameter number is		==> 2		# 第一次偏移 1 个，只生效 2 个了
Your whole parameter is		==> 'b c' 

Total parameter number is		==> 2		# 第二次偏移 3 个，发现没有生效，不够偏移
Your whole parameter is		==> 'b c'


[mrcode@study bin]$ ./how_paras.sh a b c d		# 给 4 个参数
Total parameter number is		==> 4
Your whole parameter is		==> 'a b c d' 

Total parameter number is		==> 3		# 第一次偏移 1 个，还剩下 3 个
Your whole parameter is		==> 'b c d' 

Total parameter number is		==> 0		# 第二次偏移 3 个，剩下 0 个
Your whole parameter is		==> ''
```

总结如下：

- **shift** 可以忽略掉 **n** 个参数
- **shif** 中的 **n** 必须要有足够的参数才会生效，否则不会偏移

## 条件判断

在程序中，没有条件判断 `if then` 方式的话，在执行多条指令的时候，就会很麻烦。

### 利用 `if...then`

**单层、简单条件判断**

```bash
if [ 表达式 ]; then
	当条件成立时，可以进行的指令工作内容
fi
```

至于表达式的编码，与上一章的 **test** 一致，但是有一个特别的是，可以使用 `&&` 与 `||` 来连接多个中括号，在这里他们的含义就是表示 并且 和 或者 的意思

所以在使用中括号的时候， `&&` 和 `||` 与指令状态下的含义不同。比如：

```bash
[ "${yn}" == "Y" -o "${yn}" == "y" ]
可以替换为下面的方式
[ "${yn}" == "Y" ] || [ "${yn}" == "y" ]
```

这样就很方便维护了，一个中括号一个表达式。那么将这个 **script** 修改为 `if...then` 的形式如下

```bash
[mrcode@study bin]$ vi ans_yn.sh

#!/bin/bash
# Program:
#       This program shows the user's choice
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "请输入 Y/N：" yn
# [ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "Ok，continue" && exit 0
if [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]; then
        echo "Ok，continue"
        exit 0
fi

# [ "${yn}" == "N" -o "${yn}" == "n" ] && echo "Oh，interrupt！" && exit 0
if [ "${yn}" == "N" ] || [ "${yn}" == "n" ]; then
        echo "Oh，interrupt！"
        exit 0
fi
echo "I don't know what your choice is" && exit 0
```

此方式只是在代码组织上更偏向于笔者所学的 JAVA 语言了，对于变量的判定还可以使用如下的多重判断来达到效果

**多重、复杂条件判断**

简单说，上述实例对于变量 `${yn}` 使用了两次 **if**，那么可以使用如下方式简化

```bash
if [ 条件表达式 ]; then
	做点啥
else
	做点啥
fi
```

更复杂的情况，增加 **elseif** ，如下

```bash
if [ 条件表达式 ]; then
	做点啥
elif [ 条件表达式 ]; then
	做点啥
else
	做点啥
fi
```

改写 `ans_yn.sh` 脚本如下

```bash
#!/bin/bash
# Program:
#       This program shows the user's choice
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "请输入 Y/N：" yn
# [ "${yn}" == "Y" -o "${yn}" == "y" ] && echo "Ok，continue" && exit 0
if [ "${yn}" == "Y" ] || [ "${yn}" == "y" ]; then
        echo "Ok，continue"
        exit 0
else
        echo "Oh，interrupt！"
        exit 0
fi
echo "I don't know what your choice is" && exit 0
```

另一个范例知识，上一节提到参数功能（**$1**），让用户在下达指令的时候将参数带进去，让用户输入 **hello** 关键词，利用参数的方法可以如下设计：

1. 判断 `$1` 是否为 **hello** ，如果是，则显示「**Hello, how ary you?**」
2. 如果无参数，则提示使用者必须要使用的参数下达方法
3. 如果参数不是 **hello**，则提示使用者仅能使用 **hello** 为参数

```bash
#!/bin/bash
# Program:
#       直接携带参数提示
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

if [ "$1" == "hello" ]; then
	echo "Hello, how ary you?"
elif [ -z "$1" ]; then
	echo "请携带参数"
else
	echo "只能携带参数 hello"
fi

```

测试输出

```bash
[mrcode@study bin]$ ./hello-2.sh 
请携带参数
[mrcode@study bin]$ ./hello-2.sh hello
Hello, how ary you?
[mrcode@study bin]$ ./hello-2.sh hellox
只能携带参数 hello
```

书上例子如下：

```bash
#!/bin/bash
# Program:
#       Chek $1 is equal to "hello"
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

if [ "$1" == "hello" ]; then
	echo "Hello, how ary you?"
elif [ "$1" == "" ]; then
	echo "You MUST input parameters, ex> {${0} someword}"
else
	echo "The only parameter is 'hello', ex> {${0} hello}"
fi
```

信息输出如下

```bash
[mrcode@study bin]$ ./hello-2.sh 
You MUST input parameters, ex> {./hello-2.sh someword}
[mrcode@study bin]$ ./hello-2.sh hell
The only parameter is 'hello', ex> {./hello-2.sh hello}
[mrcode@study bin]$ ./hello-2.sh hello
Hello, how ary you?
```

那么深入练习。

在第十章学习了 grep 指令，现在多了解一个 netstat 指令，可以查询到目前主机有开启的网络服务端口（service ports），相关功能会在 **服务器架设篇** 继续介绍；这里只需要知道 `netstat -tuln`可以取得目前主机有启动的服务，而且取得的信息类似下面这样

```bash
[mrcode@study bin]$ netstat -tuln
Active Internet connections (only servers)
# 封包格式			 本地 IP:端口			  远程 IP:端口				是否监听
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:6011          0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN     
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp6       0      0 ::1:631                 :::*                    LISTEN     
tcp6       0      0 ::1:25                  :::*                    LISTEN     
tcp6       0      0 ::1:6010                :::*                    LISTEN     
tcp6       0      0 ::1:6011                :::*                    LISTEN     
tcp6       0      0 :::111                  :::*                    LISTEN     
tcp6       0      0 :::22                   :::*                    LISTEN     
udp        0      0 0.0.0.0:48829           0.0.0.0:*                          
udp        0      0 192.168.122.1:53        0.0.0.0:*                          
udp        0      0 0.0.0.0:67              0.0.0.0:*                          
udp        0      0 0.0.0.0:111             0.0.0.0:*                          
udp        0      0 127.0.0.1:323           0.0.0.0:*                          
udp        0      0 0.0.0.0:672             0.0.0.0:*                          
udp        0      0 0.0.0.0:5353            0.0.0.0:*                          
udp6       0      0 :::111                  :::*                               
udp6       0      0 ::1:323                 :::*                               
udp6       0      0 :::672                  :::*  
```

重点关注 **Local Address** 字段（本地主机 **IP** 与端口对应），代表本机所启动的网络服务，**127.0.0.1** 则是针对本机开放，若是 **0.0.0.0 或 :::** 则代表对整个 **Internet** 开放。每个端口 **port** 都有其特定的网络服务，几个常见的 **port** 与网络服务的关系是：

- **80**：**www**
- **22**：**ssh**
- **21**：**ftp**
- **25**：**mail**
- **111**：**RPC**
- **631**：**CUPS**（打印服务功能）

假设我要检测常见端口 **port 21、22、25、80** 时，可以通过 **netstat** 检测主机是否有开启这四个主要的网络服务端口，由于每个服务的关键词都是接在冒号「**:**」后面，所以可以截取类似「**:80**」来检测。那么程序如下

下面是笔者写的脚本

```bash
vim netstat.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "现在开始检测当前主机上的服务"
echo -e "www、ftp、mail、www 服务将被检测 \n"
# 将 local Address 字段截取出来
datas=$(netstat -tuln | awk '{print $4}')
testing=$(grep ":80" ${datas}) 
if [ ! -z "${testing}" ]; then
	echo "www"
fi
testing=$(grep ":22" ${datas}) 
if [ ! -z "${testing}" ]; then
	echo "ssh"
fi
testing=$(grep ":21" ${datas}) 
if [ ! -z "${testing}" ]; then
	echo "ftp"
fi
testing=$(grep ":25" ${datas}) 
if [ ! -z "${testing}" ]; then
	echo "mail"
fi
```

不过很遗憾，**grep** 后只能跟一个文件路径。那么正确的做法如下

```bash
#!/bin/bash
# Program:
#       Using netstat and grep to detect www⽀~Assh⽀~Aftp and mail services
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1. 佅~H佑~J潟¥彎¥䷾K彝¥襾A佁~Z亾@举H
echo "潎°作¨廾@妾K梾@派K弾S佉~M主彜º䷾J潚~D彜~M佊¡"
echo -e "www⽀~Aftp⽀~Amail⽀~Awww 彜~M佊¡対F被梾@派K \n"

# 2. 达[蠾L佈¤孾Z佒~L信彁¯轾S佇º 
# 対F local Address 嬾W段彈ª住~V佇º彝¥﻾L并潔~_彈~P彖~G件
testfile=/dev/shm/netstat_checking.txt
netstat -tuln | awk '{print $4}' > ${testfile}

testing=$(grep ":80" ${testfile})
if [ "${testing}" != "" ]; then
        echo "www is running in you system. "
fi

testing=$(grep ":22" ${testfile})
if [ ! -z "${testing}" ]; then
        echo "ssh  is running in you system. "
fi

testing=$(grep ":21" ${testfile})
if [ ! -z "${testing}" ]; then
        echo "ftp  is running in you system. "
fi
testing=$(grep ":25" ${testfile})
if [ ! -z "${testing}" ]; then
        echo "mail  is running in you system. "
fi
```

输出信息如下

```bash
[mrcode@study bin]$ ./netstat.sh 
现在开始检测当前主机上的服务
www、ftp、mail、www 服务将被检测 

ssh  is running in you system. 
mail  is running in you system.
```

条件判断还可以更复杂，比如：在台湾当兵是国民应尽的义务，不过，在当兵的时候总是很想退伍，那么写个脚本程序来实现：让用户输入他的退伍日期，计算出还有多少天才退伍？的功能

那么思路如下：

1. 用户输入自己的退伍日期
2. 由现在的日期对比退伍日期
3. 由两个日期的比较来显示「还需要几天」才能够退伍的字样

温馨提示：日期可以使用 `date --date="YYYYMMDD" +%s` 来取得指定日期的秒数，再利用秒数相减，再计算到天

笔者从现在开始，就不再贴出自己写的代码了，先自己写，然后对照书上的，最后部分修改成书上的展示

```bash
vim cal_retired.sh
  
#!/bin/bash
# Program:
#       You input you demobilization date,I calculate how many days before you demobilize.
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 1. 告知用户程序的用途，并且告知应该如何输入日期格式
# 这个程序将尝试计算出，您的退伍日期还有多少天
echo "This program will try to calculate :"
echo "How many days before your demobilization date..."
read -p "Please input your demobilization date (YYYYMMDD ex>20200112)：" date2

# 2. 测试判定，输入内容是否正确，使用正则表达式
date_d=$(echo ${date2} | grep '[0-9]\{8\}')             # 匹配 8 位数的字符串
if [ -z "${date_d}" ]; then
        # 您输入了错误的日期格式
        echo "You input the wrong date format..."
        exit 1
fi

# 3. 开始计算日期
declare -i date_dem=$(date --date="${date_d}" +%s)              # 退伍日期秒数
declare -i date_now=$(date +%s)                                 # 当前日期秒数
declare -i date_total_s=$((${date_dem}-${date_now}))    		# 剩余秒数
# 需要注意的是：这种嵌套执行的时候，括号一定要嵌套对位置
declare -i date_d=$((${date_total_s}/60/60/24))                 # 转换为日
# 中括号里面不能直接使用 < 这种符号
if [ "${date_total_s}" -lt 0 ]; then
		# 这里是用 -1 乘，得到是正数，标识已经退伍多少天了
        echo "You had been demobilization before:  $((-1*${date_d}))  ago"
else
        # 这里使用 总秒数 - 转换为日的变量（这里只是转换为了天），剩余数据转成小时
        # 则计算到 n 天 n 小时
        declare -i date_h=$(($((${date_total_s}-${date_d}*60*60*24))/60/60))
        echo "You will demobilize after ${date_d} days and ${date_h} hours."
fi


```

测试输出

```bash
[mrcode@study bin]$ ./cal_retired.sh 
This program will try to calculate :
How many days before your demobilization date...
Please input your demobilization date (YYYYMMDD ex>20200112)：20200120	# 输入当天
You had been demobilization before:  0  ago

[mrcode@study bin]$ ./cal_retired.sh 
This program will try to calculate :
How many days before your demobilization date...
Please input your demobilization date (YYYYMMDD ex>20200112)：20200119	# 输入前一天
You had been demobilization before:  1  ago

[mrcode@study bin]$ ./cal_retired.sh 
This program will try to calculate :
How many days before your demobilization date...
Please input your demobilization date (YYYYMMDD ex>20200112)：20200121	# 输入明天
You will demobilize after 0 days and 8 hours.

[mrcode@study bin]$ ./cal_retired.sh 
This program will try to calculate :
How many days before your demobilization date...
Please input your demobilization date (YYYYMMDD ex>20200112)：2020^H^H3	# 输入错误的格式
You input the wrong date format...

[mrcode@study bin]$ ./cal_retired.sh 
This program will try to calculate :
How many days before your demobilization date...
Please input your demobilization date (YYYYMMDD ex>20200112)：20300120	# 输入10 年后
You will demobilize after 3652 days and 8 hours.
```

笔者总结：

- 本例结合了 **grep** 查找符合条件的参数，如果完全不符合，则为空白返回了
- 结合了 **declare -i** 定义整数变量
- 使用了 `$(($(())))` 嵌套指令执行语法
- 该范例还是有难度的，难点在于 用正则检查输入参数 和 计算 天 并计算小时

### 利用 `case...esac` 判断

作为 **JAVA** 程序员，这个不用多解释，直接看语法

```bash
case $变量名称 in		# 关键词为 case 还有 变量前的 $ 符号
	“变量内容 1”)		# 每个变量内容建议用双引号括起来，关键词则为小括号
	 	程序段
	 ;;				  # 使用两个连续的分号来结尾
	“变量内容 2”)
	 	程序段
	 ;;
	*)			 	  # 最后一个变量内容需要用 * 来代表所有其他值
		程序段
	 ;;
esac				  # 最终的 case 结尾，就是反过来拼写的字符 esac
```

将上面 `./hello-2.sh` 的例子使用该语法修改

```bash
./hello-3.sh 

#!/bin/bash
# Program:
#       直接携带参数提示
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

case $1 in
	"hello")
		echo "Hello, how ary you?"
	;;
	"")
		echo "请携带参数"
	;;
	*)
		echo "只能携带参数 hello"
	;;
esac
```

此种判定方式，针对于判定字符串时会更加的方便，**CentOS6.x** 以前系统很多服务的启动都是使用使用这种写法写的。虽然 **CentOS7** 已经使用 **systemd** ，不过任然有数个服务时放在 `/etc/init.d` 目录下的、比如有个名为 `netconsole` 的服务在该目录下

```bash
# 重新启动该服务
# 注意该服务需要使用 root 身份才行，一般账户可以执行，但是不会成功
/etc/init.d/netconsole restart
```

查阅该文件，找到文件末尾为发现以下的内容，这里就判定了输入的参数，使用的就是 case 语法

```bash
case "$1" in
    stop) stop ;;
    status) status ;;
    start|restart|reload|force-reload) restart ;;
    condrestart) condrestart ;;
    *) usage ;;
esac
```

所以对于脚本的编写，可以参考这些已经有的，看看人家是怎么写的

一般来说，使用「`case $变量 in`」语法，那个变量大致有两种取得方式：

- 直接下达：利用 `script.sh variable` 方式直接给 **$1** 变量，这也是在 `/etc/init.d` 目录下大多数程序的设计方式
- 交互式：通过 **read** 指令让用户输入变量内容

下面来演示下：

- 用户输入 `one、two、three` 并显示在屏幕上
- 如果不是以上变量，那么提示用户只有这三种选择

```bash
vim show123.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 如需要让用户交互性输入，那么可以用这两行代替：case "$1" in
# read -p "请输入您的选择：" choice
# case "${choice}" in

case "$1" in
	"one") echo $1 ;;
	"two") echo $1 ;;
	"three") echo $1 ;;
	*) echo "只能输入 one、two、three" ;;
esac
```

测试输出

```bash
[mrcode@study bin]$ ./show123.sh 
只能输入 one、two、three
[mrcode@study bin]$ ./show123.sh one
one
[mrcode@study bin]$ ./show123.sh two
two
[mrcode@study bin]$ ./show123.sh three
three
[mrcode@study bin]$ ./show123.sh three111
只能输入 one、two、three
```

### 利用 function 功能

函数功能，不用多说，可以被复用，优化程序结构，语法如下

```bash
function fname(){
	程序段
}
```

{% note info %}

**TIP**

由于 **shell script** 执行方式是由上而下，由左而右，因此 **function** 的代码一定要在程序的最前面

{% endnote %}

下面将 **show123.sh** 改写成使用 **function** 方式

```bash
vim show123-2.sh
#!/bin/bash
# Program:
#       
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 如需要让用户交互性输入，那么可以用这两行代替：case "$1" in
# read -p "请输入您的选择：" choice
# case "${choice}" in

function printit(){
	echo -n "Your choice is " # -n 可以不断行连续在同一行显示
}

case "$1" in
	"one") printit; echo $1 ;;
	"two") printit; echo $1 | tr 'a-z' 'A-z' ;;  # 转换为大写
	"three") printit; echo $1 ;;
	*) echo "只能输入 one、two、three" ;;
esac
```

输出信息

```bash
[mrcode@study bin]$ ./show123-2.sh one
Your choice is one
[mrcode@study bin]$ vim show123-2.sh
[mrcode@study bin]$ ./show123-2.sh tow
只能输入 one、two、three
[mrcode@study bin]$ ./show123-2.sh two
Your choice is TWO
```

上述代码，做了一个打印部分重复信息的功能，这个例子比较简单，当在程序中有大量重复，和大量逻辑的时候，就会体现出来了

同样，**function** 也可以有参数变量，改写成有参数调用函数

```bash
vim show123-3.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/01/20              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

function printit(){
        echo "Your choice is ${1}" # 在函数域中，的参数变量，与外部的不一致
}

case "$1" in
        "one") printit 1 ;;
        "two") printit 2 ;;
        "three") printit $1 ;;
        *) echo "只能输入 one、two、three" ;;
esac
```

测试如下

```bash
[mrcode@study bin]$ ./show123-3.sh one
Your choice is 1			
[mrcode@study bin]$ ./show123-3.sh two
Your choice is 2 	# 可以看到，这里给定参数 1，那么在里面获取 ${1}，的时候就获取到了

[mrcode@study bin]$ ./show123-3.sh three		
Your choice is three	# 在外部给定的是脚本中的变量 $1, 在内部也能获取到变量的具体内容
[mrcode@study bin]$ ./show123-3.sh threex
只能输入 one、two、three
```

## 循环（loop）

循环可以不断执行某个程序段楼，直到用户设定的条件达成为止。

### while do done、until do done（不定循环）

当条件成立时，执行循环体

```bash
while [ condition ]   # 中括号中条件判断
do    # 循环开始
  程序段落
done  # 循环结束
```

还有一种不定循环的方式，当条件成立时退出循环体

```bash
until [ condition ]
do
    程序段落
done
```

范例：让使用者输入 **yes** 或则是 **YES** 才结束程序的执行，否则就一直告知用户输入字符串

```bash
vim yes_to_stop.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

while [ "${yn}" != 'yes' -a "${yn}" != 'YES' ]
# 使用 until 则是如下
# until [ "${yn}" == 'yes' -o "${yn}" == 'YES' ]
do
  read -p '请输入 yes 或 YES 退出程序' yn
done

echo "你输入了正确答案"
```

测试如下

```bash
[mrcode@study bin]$ ./yes_to_stop.sh
请输入 yes 或 YES 退出程序j
请输入 yes 或 YES 退出程序jj
请输入 yes 或 YES 退出程序yes
你输入了正确答案
```

如果想要计算 `1+2+3+..100`则如下写

```bash
vim cal_1_100.sh

#!/bin/bash
# Program:
#       计算 1+2+3+..100 的结果
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

total=0	# 计算结果
i=0			# 当前数值

while [ "${i}" != 100 ]
do
	i=$(($i+1))		# 每次增加 1
	total=$(($total+$i))
done
echo "1+2+3+..100 = ${total}"
```

### `for...do...done` 固定循环

```bash
for var in con1 con2 con3 ...
do
	循环体
done

```

范例：假设有三种动物，分别是 **dog**、**cat**、**elephant** 三种，输出三行信息，如 `There are dogs...` 之类的信息

```bash
vim show_animal.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

for animal in dog cat elephant
do
	echo "There are ${animal}s..."
done
```

**/etc/passwd** 中第一个字段存放了用户名，使用循环打印出每个用户名的 **id** 信息；可使用 **cut** 截取第一字段，使用 **id**指令获取用户名的信息（标识符与特殊参数）

```bash
vim userid.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

users=$(cut -d ':' -f1 /etc/passwd)		# 获取到所有的用户名
for user in ${users}
do
	id ${user}
done

```

当然还可以使用数字来做循环项，比如需要执行 `ping 192.168.1.1~192.168.1.100` 也就是从 **1 ping** 到 **100**，但是不可能需要我们手动输入 **100** 个数字吧

```bash
vim pingip.sh


#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

network="192.168.0"			# 先定义一个网域的前部分
for sitenu in $(seq 1 100)	# seq 为 sequence 连续的意思
do
	# ping -c 1 -w 1 192.168.0.101 &> /dev/null && echo "1" || echo "0"
	# 不显示执行结果，并获取命令是否执行成功
	ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=0 || result=1
	if [ "${result}" == 0 ]; then
		echo "${network}.${sitenu} is up"
	else
		echo "${network}.${sitenu} is down"
	fi
done
```

测试结果

```bash
[mrcode@study bin]$ ./pingip.sh
192.168.0.1 is up
192.168.0.2 is down
192.168.0.3 is down
..
```

对于 `$(seq 1 100)` 来说，还可以使用 **bash** 的内建机制 `{1..100}` 来代替，中间两个点表示连续的意思，比如想要输出 **a~g** 则可以使用 `a..g`

最后一个范例：

1. 让用户输入一个目录
2. 如果目录不存在，则提示并退出程序
3. 如果目录存在，则获取该目录下第一级文件是否可读、可写、可执行

```bash
vim dir_perm.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "请输入一个目录，将会检测该目录是否可读、可写、可执行：" dir
# 判定输入不为空，并且目录存在
if [ "${dir}" == '' -o ! -d "${dir}" ]; then
        echo "The ${dir} is NOT exist in your system"
        exit 1
fi

# 获取该目录下的文件权限信息
filelist=$(ls ${dir})
for file in ${filelist}
do
        perm=""
        test -r "${dir}/${file}" && perm="${perm} readable"
        test -w "${dir}/${file}" && perm="${perm} writable"
        test -x "${dir}/${file}" && perm="${perm} executable"
        echo "The file ${dir}/${file}'s permission is ${perm}"
done

```

使用这种方式，可以很轻易的来处理一些文件的特性

### `for...do...done` 数值处理

```bash
for (( 初始值; 限制值; 执行步阶))
do
	循环体
done
```

- 初始值：某个变量在循环中的起始值，可以以 **i=1** 设置好初始值
- 限制值：当变量值在这个限制值范围内，则继续循环。例如 **i<=100**
- 执行步阶：每执行一次循环时，变量的变化量。例如 **i=i+1**，如果是自增则可以使用 **i++** 来替代

范例：计算从 **1** 累加到指定数值的结果

```bash
vim cal_1_100-2.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

read -p "请输入一个数值，将计算出从 1 累加到该数值的计算结果" nu

total=0

for (( i=1; i<=${nu}; i++))
do
	total=$((${total}+${i}))
done

echo "1+..+${nu} = ${total}"
```

测试输出如下

```bash
[mrcode@study bin]$ ./cal_1_100-2.sh
请输入一个数值，将计算出从 1 累加到该数值的计算结果2
1+..+2 = 3
[mrcode@study bin]$ ./cal_1_100-2.sh
请输入一个数值，将计算出从 1 累加到该数值的计算结果100
1+..+100 = 5050
```

### 搭配随机数与数组的实验

现在大概已经能够掌握 **shell script** 了。

现在来做个有趣的小东西，今天中午吃啥？要完成这个脚本，首先需要将全部的店家输入到一组数组中，再通过随机数的处理，获得可能的值

```bash
vim what_to_eat.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 定义你搜集到的店家信息
eat[1]="卖当当汉堡"
eat[2]="肯爷爷炸鸡"
eat[3]="彩虹日式便当"
eat[4]="越油越好吃打呀"
eat[5]="想不出吃什么"
eat[6]="太师傅便当"
eat[7]="池上便当"
eat[8]="怀恋火车便当"
eat[9]="一起吃泡面"
eat[10]="太上皇"
eatnum=10		# 一共有几家可用的店铺

check=$((${RANDOM} * ${eatnum} / 32767 + 1))
echo "your may eat ${eat[${check}]}"
```

测试输出

```bash
[mrcode@study bin]$ ./what_to_eat.sh
your may eat 太上皇
[mrcode@study bin]$ ./what_to_eat.sh
your may eat 越油越好吃打呀
[mrcode@study bin]$ ./what_to_eat.sh
your may eat 想不出吃什么
[mrcode@study bin]$ ./what_to_eat.sh
```

继续深入，一次性输出 3 个选择，并且不能重复

```bash
vim what_to_eat-2.sh

#!/bin/bash
# Program:
#       
# History:
#       2020/02/12              mrcode          first relese
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

# 定义你搜集到的店家信息
eat[1]="卖当当汉堡"
eat[2]="肯爷爷炸鸡"
eat[3]="彩虹日式便当"
eat[4]="越油越好吃打呀"
eat[5]="想不出吃什么"
eat[6]="太师傅便当"
eat[7]="池上便当"
eat[8]="怀恋火车便当"
eat[9]="一起吃泡面"
eat[10]="太上皇"
eatnum=10		# 一共有几家可用的店铺

# 其实就是需要轮询出来 3 个不同的索引结果

eated=0	# 已选中数量

while [ "${eated}" -lt 3 ];
do
	check=$((${RANDOM} * ${eatnum} / 32767 + 1))
	mycheck=0		# 当为 0 时，表示不重复
	# 去重检查
	if [ ${eated} -gt 0 ]; then			# 当已选中至少一个店铺的时候，才执行
    for i in $(seq 1 ${eated})
    do
      if [ "${eatedcon[$i]}" == $check ]; then
        mycheck=1
      fi
    done
  fi
	if [ ${mycheck} == 0 ]; then
		echo "your may eat ${eat[${check}]}"
		eated=$(( ${eated} + 1 ))
		eatedcon[${eated}]=${check}		# 将已选中结果存储起来
	fi
done
```

## Shell Script 的追踪与 debug

**scripts** 在执行前，最怕出现语法错误问题了，可以通过 **bash** 相关参数来检测

```bash
sh [-nvx] scripts.sh

选项与参数：
-n：不执行 script，仅检查语法问题
-v：执行 script 前，先将 scripts 内容输出到屏幕上
-x：将执行到的 script 内容显示到屏幕上，相当于 debug 了
```

```bash
# 范例 1：测试 dir_perm.sh 有无语法问题？
sh -n dir_perm.sh
# 如果没有语法问题，则不会显示任何信息
# 笔者实测，貌似语法检测效果并不强大

```

```bash
# 范例 2：将 show_animal.sh 的执行过程全部列出来
[mrcode@study bin]$ sh -x show_animal.sh 
+ PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/home/mrcode/bin
+ export PATH
+ for animal in dog cat elephant
+ echo 'There are dogs...'
There are dogs...
+ for animal in dog cat elephant
+ echo 'There are cats...'
There are cats...
+ for animal in dog cat elephant
+ echo 'There are elephants...'
There are elephants...

# 下面是原始脚本，方便对比
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

for animal in dog cat elephant
do
        echo "There are ${animal}s..."
done
# 可以看到，每次循环执行，都打印出来了关键信息，
# 该功能非常有用，可以当成 debug 来使用

```

熟悉 **sh** 的用法，可以使你在管理 **Linux** 的过程中得心应手，至于在 **shell script** 的学习方法上，需要多看、多模仿、并加以修改成自己的代码，是最快的学习手段了。网络有上相当多的有用的 **scripts**，你可以将其拿来进行修改成自己的代码

另外，**Linux** 系统本来就有很多的服务启动脚本，如果想要知道每个 **script** 所代表的功能是什么，直接 **vim** 进入该 **script** 查阅下，通常就知道了。比如说之前提到的 `/etc/init.d/netconsole` 是做什么的？直接查看他的前几行

```bash
vim /etc/init.d/netconsole 


#!/bin/bash
# 
# netconsole    This loads the netconsole module with the configured parameters.
#
# chkconfig: - 50 50
# description: Initializes network console logging
# config: /etc/sysconfig/netconsole
#
### BEGIN INIT INFO
# Provides:          netconsole
# Required-Start:    $network
# Short-Description: Initializes network console logging
# Description:       Initializes network console logging of kernel messages.
### END INIT INFO

# Copyright 2002 Red Hat, Inc.
#
# Based in part on a shell script by
# Andreas Dilger <adilger@turbolinux.com>  Sep 26, 2001
```

-------

# 进程管理与 SELinux 初探

一个程序被加载到内存中运行，那么在内存中的那个数据就称为进程（**process**）。

所有系统上跑的数据都会以进程的形态存在，进程有哪些状态？不同的状态会如何影响听的运行？进程之间是否可以互相控管？等等，这些是我们所必须要知道的项目

与进程有关的还有 **SELinux** 加强文件存取安全性的东西

## 什么是进程（process）

在 Linux 系统中：**触发任何一个事件事，系统都会将它定义成一个进程，并且给予这个进程一个 ID，称为 PID，同时依据启发这个进程的用户与相关属性关系，给予这个 PID 一组有效的权限设置**。

### 进程与程序 （process & program）

执行一个程序或指令，就可以触发一个事件而取得一个 **PID**

不同的用户运行程序，程序所拥有的权限也是不同的，概念如下图

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/Linux-pid.png" style="zoom: 67%;" />

系统通过这个 **PID** 来判断该 **process** 是否具有权限进行工作的。

比如我们登陆的 **bash tty**，它是一个程序，登陆之后，系统会根据登陆者的 **UID/GID（/etc/paswd）**来分配一个 **PID**，比如执行了一个 **touch** 的执行，那么由这个进程 **衍生出来的其他进程在一般状态下，也会沿用这个进程的相关权限**

程序与进程总结：

- 程序 **program**：通常为 **binary program**，实体文件的形态存在
- 进程 **process**：程序被触发后，执行者的权限与属性、程序的程序代码与所需数据等都会被加载到内存中，操作系统并给予这个内存单元一个标识符（**PID**），可以说，进程就是一个正在运行的程序

### 子进程与父进程

上面提到 **衍生出来的进程**，我们登陆到 **bash**，该 **bash** 是一个程序，并有一个 **PID**，在这个 **bash** 上执行指令，触发了相关指令的程序运行，从而得到该程序的 **PID**，这个 **PID** 就是一个子进程，原本的 **bash** 就是一个父进程

下面以一个小练习，来了解什么是子进程/父进程

```bash
# 在目前的 bash 环境下，再触发一次 bash，并以 ps -l 指令管擦进程相关的输出信息
# 直接执行 bash 指令，会进入到子进程的环境中
[root@study ~]# bash
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0  5713  1923  0  80   0 - 32064 do_wai pts/0    00:00:00 su
4 S     0  5862  5713  0  80   0 - 29218 do_wai pts/0    00:00:00 bash
4 S     0 10917  5862  0  80   0 -  3184 do_wai pts/0    00:00:00 bash
0 R     0 11193 10917  0  80   0 - 12407 -      pts/0    00:00:00 ps
# 注意 PID 与 PPID，第 1 行的 PID 与第 2 行的 PPID 是一样的
# 第 2 行的 CMD 是 bash，就是从第一行中执行 bash 产生出来的

```

如果你发现，你杀掉了一个进程，不多久又出现了，这样的情况，如果不是 **crontab** 的定时任务产生的，那么就是有一个父进程在产生新的子进程

### fork and exec：进程呼叫的流程

进程与父进程的关系最为复杂的在于进程相互间的呼叫。

在 **Linux** 的进程呼叫通常称为 **fork-and-exec** 的流程，进程都会借由父进程以复制（**fork**）的方式产生一个一模一样的子进程，然后被复制出来的子进程再以 **exec** 的方式来执行时机要进行的程序，最终就成为一个子进程的存在。整个流程类似下图：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-fork-exec.png" style="zoom:67%;" />

### 系统或网络服务：常驻在内存的进程

常驻就是一直在运行的程序，比如 **crond** 程序，每分钟都会扫描 `/etc/crontab` 以及相关的配置文件，将它启动在背景当中一直持续不断的运行，这就是常驻在内存中的进程

这些进程通常都是负责一些系统所提供的功能以服务用户各项任务，因此这些常驻程序称为 **服务（daemon）**。系统的服务非常多，主要大致分为系统本身所需要的服务，如 **crond**、**atd**、**rsyslogd** 等。还有一些则负责网络联机服务，例如 **Apache**、**named**、**postfix**、**vsftpd** 等，这些网络服务程序被执行后，会启动一个可以负责网络监听的端口 port，以提供外部客户端的联机要求

{% note info %}

**TIP**

**cron** 和 **at** 是他们主要执行的程序名称，但是后面加了一个 **d**，成为 **crond**、**atd** ，是因为这样可以简单的判定为该程序是一个服务 **daemon**，所以一般 **daemon** 类型的程序都会加上 **d** 的文件名，比如服务器篇中会看到的 **httpd**、**vsftpd** 等

{% endnote %}

### Linux 的多人多任务环境

了解了进程的知识点后，你可以简单的认为进程的出现，让我们多用户在 **Linux** 都能拥有自己的环境了。

- 多人环境

  **Linux** 最棒的地方就在于它的多人多任务环境。什么是多人多任务？每个账户都有其特殊的权限，除了 **root** 之外，其他人都必须受一些限制，而每个人进入 **Linux** 的环境设置都可以自定义，所以每个人登录后取得的 **shell PI**D 不同，是因为不在同一个进程程序中

- 多任务行为

  **CPU** 多在各个进程之间进行切换工作，因此当多人同时登录系统时，你的感觉就像整部主机好像就是你一个人的一样

- 多重登录环境的七个基本终端窗口

  在 **Linux** 中，默认提供了 6 个文字界面登录窗口和一个图形界面，可以使用 `alt+F{1-7}`来切换不同的终端机界面，每个终端机界面的登录者可以是不同用户

  这也是多任务环境下所产生的一个情况，**Linux** 默认会启动 **6** 个终端机登录环境的程序，所以才可以切换，在后续第 19 章开机管理流程中会仔细介绍的。

- 特殊的进程管理行为

  对于宕机情况，在 **Linux** 上，几乎不会出现，因为他可以在任何时候，将某个困住的进程杀掉，然后在重新执行该进程而不用重新启动。

  比如在 **Linux** 下以文字界面登录，在屏幕中显示错误信息后就挂了，不能动了。这个时候你可以切换到另外的 6 个终端机接口，以 `ps -aux` 找出刚刚的错误进程，**kiil** 掉它，再回到刚刚的终端机界面就好了（笔者疑问？**kill** 后会自动重启？有点不太理解）

- **bash** 环境下的工作管理

  当我们登录 **bash** 后，取到了一个 **PID**，那么在这个环境下执行的其他指令，几乎是子进程了，在这个单一的 **bash** 接口中，可以如下进行多个工作，并且是同时进行

  ```bash
  cp file1 file2 &
  
  ```

  上述指令串中，`&` 表示把 **file1** 复制为 **file2**，并放到背景中执行。也就是说，下达完这一串指令后，马上就可以下达其他的指令串了，当着一个指令执行完成后，系统将会在你的终端接口显示完成的消息

- 多人多任务的系统资源分配问题考虑

  当人多的时候，由于是共用计算机资源，所以有可能会导致资源不够用的情况发生，这个时候就需要升级机器了

-----

## 工作管理（job control）

是在 **bash** 环境下的概念，当我们登录系统取得 **bash shell** 后，在单一终端机下同时进行多个工作的行为管理。

### 什么是工作管理？

进行工作管理的行为中，其实每个工作都是目前 bash 的子进程，彼此之间是有相关性的。我们**无法以 job control 的方式由 tty1 的环境去管理 tty2 的 bash**

为什么会有工作管理？系统有多个 tty 使用，这样切换很麻烦，还有之前讲解的 `/etc/security/limits.conf(第 13 章)`可以设置同时登录的联机数量，假设只允许一个呢？

假设我们只有一个终端机接口，因此在可以出现提示字符让你操作的环境称为 **前景 foreground**，其他工作可以放入 **背景 background** 去暂停或运行。要注意的是：放入背景的工作在运行时，不能与使用者互动。比如 **vim** 不能再背景里面执行（**running**）的，因为你没有输入数据它就不会运行。**而且放入背景的工作是不可以使用 ctrl+c 来终止的**

进行 **bash** 的 **job control** 必须要注意的限制是：

- 这些工作所触发的进程必须来自于你的 **shell** 的子进程（只管理自己的 **bash**）
- 前景 **foreground**：你可以控制与下达指令的环境
- 背景：可以自动运行的工作，你无法使用 **ctrl + c** 终止它，可以使用 **bg**、**fg** 呼叫该工作
- 背景中**执行**的进程不能等待 **terminal/shell** 的输入（**input**）

### job control 的管理

#### 直接将指令丢到背景中 *执行* 的 `&`

```bash
# 使用 & 将 /etc/ 整个备份为 /tmp/etc/tar.gz 工作丢到背景中执行
# 原因就是，压缩费时，不想一直就在当前界面看着他完成
[root@study ~]# tar -zpcf /tmp/etc.tar.gz /etc &
[1] 19763			# job number 与 PID
[root@study ~]# tar: Removing leading `/' from member names
# PID 与 bash 的控制有关，后续出现的数据信息是 tar 执行的数据流
# 由于没有加上数据流重导向，所以会影响画面，不过不会影响前景的操作

# 那他什么时候完成呢？当你输入几个指令后，发现出现了这一行
# 那么久表示在背景中的工作已经完成了
[1]+  Done                    tar -zpcf /tmp/etc.tar.gz /etc

```

`[1]+` 表示这个工作已经完成（**Done**），后面是具体的指令串。如果有有信息出现，那么你的前景会出现干扰，只需要按下 **enter** 键就会出现提示字符，更下下指令

```bash
[root@study ~]# tar -zpcvf /tmp/etc.tar.gz /etc &

```

由于输出了信息，**stdout** 和 **stderr** 都会输出到屏幕上，这样就会影响前景终端，所以一般都利用数据流重导向，将输出数据传送至某个文件中，比如

```bash
[root@study ~]# tar -zpcvf /tmp/etc.tar.gz /etc  > /tmp/log.txt 2>&1 &
[1] 16592
[root@study ~]# 

```

#### 将 *目前* 的工作丢到背景中_暂停_：ctrl+z

考虑这个场景，我正在使用 **vim**，却发现某个文件的路径不记得了，需要到 **bash** 环境下进程搜索，此时不需要结束 **vim**，可以把它丢到背景中等待

```bash
[root@study ~]# vim ~/.bashrc 
# 在 vim 环境下按 ctrl + z 组合键
[2]+  Stopped                 vim ~/.bashrc
[root@study ~]# 		# 这就取得了前景
[root@study ~]# find / -print
# 会大量输出信息，我们把这个工作也丢到背景中执行
[3]+  Stopped                 find / -print
[root@study ~]# 

```

`[2]+` 表示这个是加入到背景中的第二个工作，**Stopped** 是状态，预设情况下，使用 **ctrl+z** 丢到背景中的工作都是暂停状态

#### 观察目前的背景工作状态：jobs

```bash
jobs [-lrs]

选项与参数：
	-l：除了列出 job number 与指令之外，同时列出 PID 的号码
	-r：仅列出正在背景 run 的工作
	-s：仅列出正在背景中暂停 stop 的工作

```

```bash
# 范例 1：观察目前的 bash 中，所有工作与队友的 PID
[root@study ~]# jobs -l
[2]- 26476 Stopped                 vim ~/.bashrc
[3]+  2207 Stopped                 find / -print

```

仔细看上面有减号和加号：

- `+`：表示最近被放到背景的工作；如果只输入 **fg** 指令，那么 `[3]` 会被拿到前景中来处理
- `-`：表示最近最后第二个被放置到背景中的工作。如果超过最后第三个以后的工作，就不会有 `-、+` 符号了

#### 将背景工作拿到前景来处理：fg

```bash
fg %jobnumber

$jobnumber: jobnumber 是工作号码（数字），哪个 % 是可有可无的

```

```bash
# 范例 1：先以 jobs 管擦工作，再将工作取出
[root@study ~]# jobs -l
[2]- 26476 Stopped                 vim ~/.bashrc
[3]+  2207 Stopped                 find / -print
# 取出 + 号的工作，注意会刷屏，马上按下 ctrl + z ，再次放入到背景中
[root@study ~]# fg
# 直接取出 2 的工作，在放到背景中
[root@study ~]# fg %2
vim ~/.bashrc

[2]+  Stopped                 vim ~/.bashrc
[root@study ~]# jobs -l
[2]+ 26476 Stopped                 vim ~/.bashrc
[3]-  2207 Stopped                 find / -print

# 可以看到， 2 的工作被标记为了 + 号，表示是最近放进去的

```

#### 让工作在背景下的状态变成运行中：bg

```bash
# 范例 1： 执行 find / -perm /7000 > /tmp/text.txt，立刻丢到背景去暂停
[root@study ~]# find / -perm /7000 > /tmp/text.txt
find: '/proc/29541/task/29541/fd/5': No such file or directory
find: '/proc/29541/task/29541/fdinfo/5': No such file or directory
find: '/proc/29541/fd/6': No such file or directory
find: '/proc/29541/fdinfo/6': No such file or directory
^Z
[4]+  Stopped                 find / -perm /7000 > /tmp/text.txt

# 范例 2：让该工作在背景下进行，并且观察他
[root@study ~]# jobs ; bg %4; jobs
[2]-  Stopped                 vim ~/.bashrc
[3]   Stopped                 find / -print
[4]+  Stopped                 find / -perm /7000 > /tmp/text.txt

[4]+ find / -perm /7000 > /tmp/text.txt &
[2]+  Stopped                 vim ~/.bashrc
[3]   Stopped                 find / -print
[4]-  Running                 find / -perm /7000 > /tmp/text.txt &
# 第 4 个由 Stopped 变成了 Running 状态

```

#### 管理背景中的工作：kill

通过 **fg** 拿到前景来，可以通过 **kill** 将该工作直接移除

```bash
kill -signal $jobnumber
kill -l

选项与参数：
	-l：L 的小写，列出目前 kill 能够使用的信号（signal）有哪些？
	signal：给予后续工作什么指示，用 man 7 signal 可知：
		-1：重新读取一次参数的配置文件（类似 reload）
		-2：代表与由键盘输入 ctrl+c 同样的动作
		-9：立刻强制删除一个工作
		-15：已正常的进程方式终止一项工作。与  -9 是不一样的

```

```bash
范例 1： 找出目前 bash 环境下的背景工作，并将该工作 强制删除
[root@study ~]# jobs
[2]+  Stopped                 vim ~/.bashrc
[3]   Stopped                 find / -print
[4]-  Exit 1                  find / -perm /7000 > /tmp/text.txt

[root@study ~]# kill -9 %3; jobs
[2]+  Stopped                 vim ~/.bashrc
[3]   Killed                  find / -print
# 过几秒再运行 jobs 会发现 killed 的不见了

# 范例 2：找出目前 bash 环境下的而背景工作，并将该工作 正常 终止
[root@study ~]# kill -SIGTERM %2

[2]+  Stopped                 vim ~/.bashrc
[root@study ~]# jobs
[2]+  Stopped                 vim ~/.bashrc
# -SIGTERM 与 -15 同效果，可以用哪个 kill -l 来查阅
# 在这个案例中，会发现 vim 的工作无法被结束，无法通过 kill 正常终止

```

使用 **vim** 时候，会产生一个 `.filename.swp` 文件，使用 **-15** 这个 **signal** 时，**vim** 会尝试以正常的步骤来结束掉该 **vi** 的工作，使用 `.filename.swp` 会主动的被移除，若是使用 **-9**，那么 **swp** 文件不会被移除调用

**kill** 需要了解 **1**、9、15 的 signal 的含义，可以用 man 7 signal 查询相关资料，还有一个 killall 也是同样的用法。

kill 后面接的数字，默认是 PID，要管理 bash 的工作控制，需要加上 %数字 的方式

### 脱机管理问题

**注意**：前面工作管理中的「背景」是指在终端机模式下可以避免「ctrl+c」中断，可以理解为是这个 bash 的背景，并 **不是放到系统的背景** 中去。所以，工作管理的背景依旧与终端机有关

如果你是以远程方式连接到 Linux 主机，并且将工作以 & 的方式放到背景中去，在工作未结束时，你脱机了，该工作不会继续进行，而是会被中断掉

那么可以使用前一章学习的 at 指令，因为它是将工作放置到系统背景，还可以使用 nohup 指令来达到效果

```bash
nohup [指令与参数]		# 在终端机前景中工作
nohup [指令与参数]		# 在终端机背景中工作

```

> **TIP**
>
> **nohup** 后面的指令不支持 **bash** 内置指令！

```bash
#1. 编辑一个会随眠  500 秒的程序
[root@study ~]# vim sleep500.sh
#!/bin/bash
/bin/sleep 500s
/bin/echo "I have sleep 500 seconds."

# 2. 丢到背景中执行
[root@study ~]# chmod a+x sleep500.sh 
[root@study ~]# nohup ./sleep500.sh &
[3] 14915
[root@study ~]# nohup: ignoring input and appending output to 'nohup.out'
    
```

你登出登录后，再次登录系统，使用 **pstree** （这里没有说是什么）去查询你的进程，会发现它还在执行，还输出了一个信息，**nohup** 与终端机无关了，因此整个信息的输出就会被导向 `~/nohup.out`

## 进程管理

为什么进程管理这么重要？是因为：

- 我们在操作系统时的各项工作都是经过某个 **PID** 来达成的（包括你的 **bash** 环境），因此，能不能进行某项工作，与该进程的权限有关
- 如果你的 **LInux** 是个很忙碌的系统，当整个系统资源要被使用光的时候，你是否能够找出最耗资源的哪个进程，然后删除该进程，让系统恢复正常？
- 由于某个程序写的不好，导致产生一个有问题的进程在内存中，如何找出它，将它移除呢？
- 如果有 5、6 项工作在系统中运行，但其中有一项工作才是最重要的，该如何让那一项重要的工作被最优先执行？

以上几点，在系统使用中都是很重要且常见的问题

### 进程的观察

#### ps：将某个时间点的进程运行情况截取下来

```bash
ps aux		# 观察系统所有的进程数据
ps -l 		# 观察与当前终端机相关的进程
ps -lA 		# 观察系统所有的进程数据（显示内容项同 ps -l 的项一样，只不过是系统所有进程）
ps axjf		# 连同部分进程树状态

选项与参数：
	-A：所有的 process 都显示出来，与 -e 具有同样的效果
	-a：不与 terminal 有关的所有 process
	-u：有效使用者（effective user）相关的 process
	x：通常与 a 一起使用，可列出完整信息
输出格式规划：
	l：较长、较详细的将该 PID 的信息列出
	j：工作的格式（jobs format）
	-f：做一个更为完整的输出
```

**ps** 指令的 **man page** 不太好查阅，不同的 **Unix** 都使用 **ps** 来查阅进程状态，为了符合不同版本的需求，该 **man page** 写的非常庞大，因此建议你有两个选择：

1. 只能查询自己 **bash** 进程的 `ps -l`
2. 可以查询所有系统运行的进程 `ps aux`

#### 仅查看自己的 bash 相关进程：`ps -l`

```bash
# 范例 1： 将目前属于您自己这次登录的 PID 与相关信息列出来（只与自己的 bash 有关）
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 29260 28796  0  80   0 - 57972 do_wai pts/0    00:00:00 su
4 S     0 29473 29260  0  80   0 - 29090 do_wai pts/0    00:00:00 bash
0 R     0 30444 29473  0  80   0 - 12407 -      pts/0    00:00:00 ps
# 前面三项，最初是用了普通账户登录的，使用了 su - 切换到了一个 bash

```

这里列出的只是与你操作环境 **bash** 有关的进程，没有延伸到 **systemd**（后续交代）：

- **F**：进程旗标（**process flags**），说明这个进程的总结权限，常见的号码有：
  - **4**：表示此进程的权限为 **root**
  - **1**：则表示此子进程仅进行 **复制（fork）而没有实际执行（exec）**
- **S**：进程状态（**STAT**），主要状态有：
  - **R**（**Running**）：正在运行中
  - **S**（**Sleep**）：该程序目前正在睡眠状态（**idle**），但可以被唤醒（**signal**）
  - **D**：不可被唤醒的睡眠状态，通常该程序可能在等待 **I/O** 的情况
  - **T**：停止状态（**stop**），可能是在工作控制（背景暂停）或除错（**traced**）状态
  - **Z**（**Zombie**）：僵尸状态，进程已终止但却无法被移除至内存外
- **UUID/PID/PPID**：代表此进程被该 **UID** 所拥有、进程的 **PID** 、此进程的父进程 **PID**
- **C**：代表 **CPU** 使用率，单位为百分比
- **PRI/NI**：**Priority/Nice** 的缩写，代表此进程被 **CPU** 所执行的优先级，数值越小表示该进程越快被 **CPU** 执行。详细的 **PRI** 与 **NI** 将在下一小节讲解
- **ADDR/SZ/WCHAN**：都与内存有关
  - **ADDR**：**kernel function**，该进程在内存的哪个部分，如果是 **running** 的进程，一般会显示 `-`
  - **SZ**：该进程用掉多少内存
  - **WCHAN** 该进程是否运行中，若为 `-` 表示正在运行中
- **TTY**：登陆者的终端机位置，若为远程登录则使用动态终端接口（**pts/n**）
- **TIME**：使用掉的 **CPU** 时间。注意：是此进程实际花费 **CPU** 运行的时间
- **CMD**：**command** 的缩写，此进程的触发程序指令

如上列出的信息表示， **bash** 的程序属于 **UID** 为 0 的使用者，状态是睡眠（**sleep**），他睡眠是因为他触发了 **ps**（状态为 **R**，**run**）的原因，**ps** 的 PID=30444，优先执行顺序为 80，下达 bash 所取得的终端机接口为 pts/0，运行状态为 do_wai

#### 观察系统所有进程：ps aux

```bash
[root@study ~]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.5 128372  6988 ?        Ss   21:14   0:01 /usr/lib/systemd/systemd --switched-root --system --deseriali
root         2  0.0  0.0      0     0 ?        S    21:14   0:00 [kthreadd]
root         4  0.0  0.0      0     0 ?        S<   21:14   0:00 [kworker/0:0H]
...
root     27082  0.0  0.1  51752  1716 pts/2    R+   21:41   0:00 ps aux


```

会发现 `ps -l` 与 `ps aux` 显示的项目也不一样

- **USER**：该 **process** 属于哪个使用者账户
- **PID**：进程标识符
- `%CPU`：该进程使用掉的 **CPU** 资源百分比
- `%MEM`：占用的虚拟内存（**KBytes**）
- **RSS**：占用的固定内存（**KBytes**）
- **TTY**：在哪个终端机上面运行？
  - `?`：与终端机无关
  - `tty1-tty6`：本机上登录的
  - `pts/0`等：是由网络连接进入的进程
- **STAT**：目前的状态，与 `ps -l` 中的状态相同含义
- **START**：该进程被触发启动时间（如果太久不会显示具体时间）
- **TIME**：该进程实际使用 **CPU** 运行的时间
- **COMMAND**：进程执行的指令

一般来说，**ps aux** 会按照 **PID** 的顺序来排序显示。

```bash
# 范例 3：以范例 1 的显示内容，显示出所有的进程
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 25710  1956  0  80   0 - 57972 do_wai pts/2    00:00:00 su
4 S     0 25917 25710  0  80   0 - 29090 do_wai pts/2    00:00:00 bash
0 R     0 32189 25917  0  80   0 - 12407 -      pts/2    00:00:00 ps
[root@study ~]# ps -lA
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0     1     0  0  80   0 - 32093 ep_pol ?        00:00:01 systemd
1 S     0     2     0  0  80   0 -     0 kthrea ?        00:00:00 kthreadd
1 S     0     4     2  0  60 -20 -     0 worker ?        00:00:00 kworker/0:0H
1 S     0     6     2  0  80   0 -     0 smpboo ?        00:00:00 ksoftirqd/0
1 S     0     7     2  0 -40   - -     0 smpboo ?        00:00:00 migration/0
1 S     0     8     2  0  80   0 -     0 rcu_gp ?        00:00:00 rcu_bh
1 R     0     9     2  0  80   0 -     0 -      ?        00:00:01 rcu_sched
# 会发现，与 ps -l 显示类似，不过显示的是系统的所有进程

# 范例 4：列出类似进程树的进程显示
[root@study ~]# ps axjf
 PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
    0     2     0     0 ?           -1 S        0   0:00 [kthreadd]
    2     4     0     0 ?           -1 S<       0   0:00  \_ [kworker/0:0H]
    2     6     0     0 ?           -1 S        0   0:00  \_ [ksoftirqd/0]
    1  1269  1269  1269 ?           -1 Ss       0   0:00 /usr/sbin/sshd -D
 1269  1922  1922  1922 ?           -1 Ss       0   0:01  \_ sshd: mrcode [priv]
 1922  1932  1922  1922 ?           -1 S     1000   0:09  |   \_ sshd: mrcode@pts/0,pts/1
 1932  1934  1934  1934 pts/0     1934 Ss+   1000   0:00  |       \_ -bash
 1932  1939  1939  1939 ?           -1 Ss    1000   0:00  |       \_ /usr/libexec/openssh/sftp-server
 1932  1941  1941  1941 pts/1     2573 Ss    1000   0:00  |       \_ -bash
 1941  2573  2573  1941 pts/1     2573 S+    1000   0:04  |       |   \_ top
 1932  7742  7742  7742 ?           -1 Ss    1000   0:00  |       \_ bash -c export LANG="en_US.UTF-8";export LANGUAGE="en_US.
 7742  7789  7742  7742 ?           -1 S     1000   0:00  |           \_ sleep 1
 1269  1926  1926  1926 ?           -1 Ss       0   0:01  \_ sshd: mrcode [priv]
 1926  1950  1926  1926 ?           -1 S     1000   0:09      \_ sshd: mrcode@pts/2,pts/3
 1950  1956  1956  1956 pts/2     7790 Ss    1000   0:00          \_ -bash
 1956 25710 25710  1956 pts/2     7790 S        0   0:00          |   \_ su -
25710 25917 25917  1956 pts/2     7790 S        0   0:00          |       \_ -bash
25917  7790  7790  1956 pts/2     7790 R+       0   0:00          |           \_ ps axjf
 1950  2009  2009  2009 ?           -1 Ss    1000   0:00          \_ /usr/libexec/openssh/sftp-server
 1950  2012  2012  2012 pts/3     2574 Ss    1000   0:00          \_ -bash


```

看上面 **PPID** 为 **1269** 的那一行开始，我这里使用了 **ssh** 远程链接，用的是 **mrcode** 账户，登录成功后，获得了一个 **bash** 环境，后面我使用了 `su -` 指令切换到了 **root** 的 **bash** 环境，然后执行了刚刚的 **ps axjf** 指令。这样就比较清楚了。

还可以通过 **pstree** 指令来显示进程树，不过貌似没有这么详细

```bash
# 范例 5：找出与 cron 和 rsyslog 这两个服务有关的 PID 号码
[root@study ~]# ps aux | egrep '(cron|rsyslog)'
root      1273  0.0  0.3 215672  3652 ?        Ssl  21:15   0:00 /usr/sbin/rsyslogd -n
root      1285  0.0  0.1 126288  1696 ?        Ss   21:15   0:00 /usr/sbin/crond -n
root      4838  0.0  0.0   9096   932 pts/2    R+   21:58   0:00 grep -E --color=auto (cron|rsyslog)
# 对于上面为什么要使用 egrep，在第 11 章，延伸正则表示法中有介绍。

```

#### 僵尸进程 zombie

僵尸 **zombie**：该进程以及执行完毕或则是因故应该要终止了，但是该进程的父进程却无法完整的将该进程结束掉，而造成哪个进程一直在内存中。

在进程中它的标识是在 **CMD** 后面有 `<defunct>` 标识,例如下面这样

```bash
apache 8683 0.0 0.9 83383 9992 ？Z 14:33 0:00 /usr/sbin/httpd<defunct>

```

当系统不稳定时，容易造成僵尸进程，可能是因为程序有问题，或则是使用者的操作习惯不良等。

发现有僵尸进程时，应该找出来，分析原因，否则有可能一直产生僵尸进程

事实上，通常僵尸进程都已经无法管控，而直接交给 **systemd** 程序来负责了，偏偏 **systemd** 是系统第一个执行的程序，它是所有程序的父程序，无法杀掉该程序（杀掉它，系统就死了），所以，经过一段时间后，系统无法通过核心非经常性的特殊处理来将该进程删除时，那只有重启机器了

### top：动态观察进程的变化

**ps** 可以显示一个时间点的进程状态，而 **top** 则可以持续的侦测进程运行状态

```bash
top [-d 数字] | top [-bnp]

选项与参数：
	-d：后面可以接秒数，整个进程画面更新的秒数，预设是 5 秒更新一次
	-b：以批次的方式执行 top，还有更多的参数可以使用（莫名其妙啊，啥参数？），通常会搭配数据流重导向来将批次的结果输出为文件
	-n：与 -b 搭配，需要进行几次 top 的输出
	-p：指定某些 PID 来进行观察

在 top 执行过程中可以使用的按键指令：
	？：显示在 top 中可以输入的按键指令
	P：以 CPU 的使用资源排序显示
	M：以 Memory 的使用资源排序显示
	N：以 PID 排序
	T：由该进程使用 CPU 时间累积（TIME+）排序
	k：给予某个 PID 一个信号（signal）
	r：给予某个 PID 重新制定一个 nice 值
	q：离开 top 软件的按键
	E：切换单位显示，比如从 KB 切换为 G 显示
	c：切换 COMMAND 的信息，name/完成指令
```

**top** 的功能太多，可用的按键也很多，可以参考 **man top** 的内部文件说明，上面只是列出常用的选项

```bash
# 范例 1：每两秒更新一次 top，观察整体信息
[root@study ~]# top -d 2
top - 22:20:11 up  1:05,  4 users,  load average: 0.52, 0.53, 0.52
Tasks: 186 total,   2 running, 184 sleeping,   0 stopped,   0 zombie
%Cpu(s):  7.7 us,  9.7 sy,  0.0 ni, 82.1 id,  0.0 wa,  0.0 hi,  0.5 si,  0.0 st
KiB Mem :  1190952 total,   428928 free,   402624 used,   359400 buff/cache
KiB Swap:  1048572 total,  1048572 free,        0 used.   632160 avail Mem
# <<< 如果按下 k 或 r 时，有相关的提示在这里出现
  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                    
 1699 gdm       20   0 2947388 136736  61224 S  0.5 11.5   0:04.00 gnome-shell                                                
 1932 mrcode    20   0  161324   3016   1296 S  0.5  0.3   0:17.28 sshd                                                       
 1950 mrcode    20   0  161324   3028   1296 S  0.5  0.3   0:17.41 sshd                                                       
 2573 mrcode    20   0  162820   3068   1576 S  0.5  0.3   0:07.43 top                                                        
    1 root      20   0  128372   6988   4196 S  0.0  0.6   0:01.67 systemd
```

**top** 的信息基本上分为两个区域，上面 **6** 行，和下面的列表

- 第一行信息：**top** -

  - 目前开机时间：**22:20:11** 这个

  - 开机到目前为止所经过的时间：**up 1:05** 这个

  - 已经登录系统的用户人数：**4 users**

  - 系统在 **1、5、15** 分钟的平均工作负载

    在第 **15** 章谈到过 **batch** 工作方式负载小于 **0.8** 就是这里显示的值了。

    表示的是，系统平均要负责运行几个进程，这里是三个值，也就是对应平均 1/5/15 分钟

    越小达标系统越空闲，若高于 **1** ，那么你的系统进程执行太频繁了

- 第二行：**tasks**

  显示的是目前进程的总量与各个状态（**running、sleeping、stopped、zombie**）的进程数量

  如果发现有 **zombie** 进程的话，就需要找下是哪个进程变成了僵尸进程了

- 第三行：`$Cpus`

  **CPU** 整体负载，每个项目可使用 ？ 查询。

  需要特别注意的是 **wa** 项，表示 **I/O wait**，通常系统变慢，都是 **I/O** 产生的问题比较大，需要特别注意该项占用的 **CPU** 资源，如果是多核 **CPU**，可以按下数字键「**1**」来切换成不同 **CPU**

- 第四行和第五行

  目前的物理内存与虚拟内存（**Mem/Swap**）的使用情况。要注意的是 **swap** 的使用量要尽量的少，如果 **swap** 被大量使用，表示系统的物理内存不足

- 第六行：当在 **top** 程序中输入指令时，显示状态的地方

下面的列表部分大部分都见过了，下面再列出含义：

- **PID**：进程 **ID**
- **USER**：进程所属使用者
- **PR**（**priority**）：进程优先执行顺序，越小越早被执行
- **NI**（**nice**）：与 **PR** 有关，越小越早被执行
- `%CPU`：**CPU** 使用率
- `%MEM`：内存使用率
- `TIME+` ：**CPU** 使用时间的累加

**top** 预设使用 **CPU** 使用率 `%CPU`作为排序的重点，如果想要使用内存使用率排序，可以按下 **M** 键，要离开按下 **q** 键

```bash
# 范例 2：将 top 的信息进行 2 次，然后将结果输出到 /tmp/top.txt
[root@study ~]# top -b -n 2 > /tmp/top.txt
# 这里的结果就是，写入了执行 2 次的结果信息。是追加写入的

```

由于只有一屏显示，所以当你要观察的进程没有排序到最前面的时候，还可以单独观察该线程

```bash
# 范例 3：我们自己的 bash PID 可以由 $$ 变量取得，使用 top 持续观察该 PID
[root@study ~]# top -d 2 -p $$
top - 22:53:55 up  1:39,  2 users,  load average: 0.59, 0.28, 0.32
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.1 us,  4.6 sy,  0.0 ni, 92.8 id,  0.0 wa,  0.0 hi,  0.5 si,  0.0 st
KiB Mem :  1190952 total,   435612 free,   392456 used,   362884 buff/cache
KiB Swap:  1048572 total,  1048572 free,        0 used.   642448 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                                                    
 9051 root      20   0  116472   3172   1780 S  0.0  0.3   0:00.04 bash 
```

就只显示着一个程序给你看了，还可以修改 **NI** 值

```bash
# 范例 4：上题的 NI 指是 0，把它修改成 10
# 在上题的 top 画面中按下 r 键出现下面的提示
PID to renice [default pid = 9051] 5501 		# 输入要修改的 PID
Renice PID 9051 to value 10		# 输入要修改的 nice 值
 PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
9051 root      30  10  116472   3172   1780 S  0.0  0.3   0:00.04 bash 

# 会发现 NI 值已经修改了
```

### pstree

```bash
pstree [-AIU] [-up]

选项与参数：
	-A：各进程之间的连接以  ASCII 字符来连接
	-U：各进程之间的连接以万国码的字符来连接。在某些终端机接口下可能会有错误
	-p：并同时列出每个 process 的 PID
	-u：并同时列出每个 process 的所属账户名称
```

```bash
# 范例 1：列出目前系统上所有的进程树的相关性
[root@study ~]# pstree -A
systemd-+-ModemManager---2*[{ModemManager}]				# ModenManager 与其子进程
        |-NetworkManager---2*[{NetworkManager}]
        |-2*[abrt-watch-log]
        |-abrtd
        |-accounts-daemon---2*[{accounts-daemon}]
        ....
        |-sshd---sshd---sshd-+-bash---su---bash---pstree		# 我们指令执行的相依性
        |                    |-bash---top
        |                    |-bash
        |                    `-sftp-server
# 看下这个相依性，差不多就是登陆之后，在 su 切换账户之后，执行的

# 范例 2：同时显示出 PID 与 users
[root@study ~]# pstree -Aup
systemd(1)-+-ModemManager(871)-+-{ModemManager}(881)
           |                   `-{ModemManager}(891)
           |-NetworkManager(935)-+-{NetworkManager}(941)
           |                     `-{NetworkManager}(945)
           |-abrt-watch-log(856)
           |-sshd(1269)---sshd(7771)---sshd(7779,mrcode)-+-bash(3239)---sleep(3263)
           |                                             |-bash(7780)---su(8985,root)---bash(9051)---pstree(3264)
           |                                             |-bash(7835)---top(8102)
           |                                             `-sftp-server(7833)
# 可以看到 sshd 登录的 PID 是 7779 ，用 mrcode 账户登录的。后续用 su 切换到了 root，这个时候新开了一个进程 7780 的 bash

```

用 **pstree** 来找相关性，同时使用 `-A` 来让连线不断开。默认的 **Unicode** 连线有可能出现断线，整体画面显示错位的问题

由 **pstree** 的输出我们可以知道，所有的进程都是依附在 **systemd** 程序下面的，**systemd** 的进程 **ID** 是 1 号，是 **LInux** 核心主动运行的第一个程序

之前讲解遇到僵尸进程为啥要重启，因为 **systemd** 要重启，那么就相当于重启系统了

### 进程的管理

进程相互管理是通过一个信号（**signal**）去告知该进程你要它做什么。信号可以通过 `man 7 signal` 查阅，主要信号代号与名称含义如下：

| 代号   | 名称        | 含义                                                         |
| ------ | ----------- | ------------------------------------------------------------ |
| **1**  | **SIGHUP**  | 启动被终止的进程，可让该 PID 重新读取自己的配置文件，类似重新启动 |
| **2**  | **SIGINT**  | 相当于用键盘输入 ctrl + c 来终端一个进程的运行               |
| **9**  | **SIGKILL** | 强制终端一个进程的运行，如果该进程进行到一半，那么尚未完成的部分可能会有半成品产生，类似 vim 会有 .filename.swp 保留下来 |
| **15** | **SIGTERM** | 以正常的结束进程来终止该进程。由于是正常的终止，所以后续的动作会将他完成。不过，如果该进程已经发生问题，就无法使用正常的方法终止时，输入该 signal 也是没有用的 |
| **19** | **SIGSTOP** | 相当于用键盘输入 ctrl-z 来暂停一个进行的运行                 |

可以使用 **kill** 或 **killall** 把信号传递给进程

### kill -signal PID

**kill** 可以将信号传递给某个工作（**%jobnumber**） 或某个 **PID**

```bash
# 范例 1：以 ps 找出 rsyslogd 这个进程 PID 后，再使用 kill 传递信号，让它可以重新读取配置文件
[root@study ~]# ps aux | grep 'rsyslogd'
root      1273  0.0  0.3 215672  3728 ?        Ssl  21:15   0:00 /usr/sbin/rsyslogd -n
root     18876  0.0  0.0   9096   928 pts/0    RN+  23:30   0:00 grep --color=auto rsyslogd
[root@study ~]# ps aux | grep 'rsyslogd' | grep -v 'grep'
root      1273  0.0  0.3 215672  3728 ?        Ssl  21:15   0:00 /usr/sbin/rsyslogd -n
[root@study ~]# ps aux | grep 'rsyslogd' | grep -v 'grep' | awk '{print $2}'
1273

# 最终的指令是如下的
[root@study ~]# kill -SIGHUP $(ps aux | grep 'rsyslogd' | grep -v 'grep' | awk '{print $2}') 
# 是否重启无法看通过看进程来知道，可以看日志
[root@study ~]# tail -5 /var/log/messages
Mar  9 23:20:01 study systemd: Removed slice User Slice of root.
Mar  9 23:30:01 study systemd: Created slice User Slice of root.
Mar  9 23:30:01 study systemd: Started Session 19 of user root.
Mar  9 23:30:01 study systemd: Removed slice User Slice of root.
Mar  9 23:35:20 study rsyslogd: [origin software="rsyslogd" swVersion="8.24.0-38.el7" x-pid="1273" x-info="http://www.rsyslog.com"] rsyslogd was HUPed
# 看上面，rsyslogd was HUPed 的字样，表示有重新启动
```

还记得可以查询到登录的 **bash** 的进程吗？也可以使用 **kill -9** 来删除，就意味着，该登陆者被踢下线了

### killall -signal 指令名称

由于 **kill** 后面必须要加上 **PID** （或是 **job number**），所以通常需要配合 **ps**、**pstree** 等指令，还可以使用另外一种方法来达到效果

```bash
killall [-iIe] [command name]

选项与参数：
	-i：interactive ，交互式的，若需要删除时，会出现提示字符给用户确认
	-e：exact，后面接的 command name 要一致，但整个完整的指令不能超过 15 个字符
	-I：指令名称（可能含参数）忽略大小写
```

```bash
# 范例 1：给予 rsyslogd 指令启动的 PID 一个 SIGHUP 的信号
[root@study ~]# killall -1 rsyslogd
# 这里 -1  是信号

# 范例 2：强制终止所有以 httpd 启动的进程（其实当前没有该进程启动）
[root@study ~]# killall -9 httpd         
httpd: no process found

# 范例 3：依次询问每个 bash 程序是否需要被终止
[root@study ~]# killall -i -9 bash
Signal bash(7780) ? (y/N) n
Signal bash(7835) ? (y/N) n
Signal bash(9051) ? (y/N) n
bash: no process found

# 这里都选择了 n，所以提示没有进程被找到，按下 y 就杀掉了

```

### 关于程序的执行顺序

**CPU** 是切换着执行进程，那么谁先执行？这个就要看进程的优先级 **priority** 与 **CPU** 排程（每个进程被 **CPU** 运行的演算规则）

#### Priority 与 Nice 值

**CPU** 一秒钟可以运行多达数 **G** 的微指令次数，通过核心的 **CPU** 调度可以让各程序 被 **CPU** 所切换运行， 因此每个程序在一秒钟内或多或少都会被 **CPU** 执行部分的指令码。

如果进程不分优先级顺序的话，那么就是排队执行，如果中间有个进程执行时间很长，其他进程就要等待很长时间

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/linux-queue-priority.jpg)

如上图，有了优先级之后，高优先级的可用被执行两次，低优先级则执行 **1** 次，但是上图仅是示意图，并非高优先级的就会执行两次，**Linux** 给予进程一个优先执行序（**priority PRI**），**PRI** 值越低优先级越高，不过该值是由核心动态调整的，用户无法直接调整 **PRI** 值

```bash
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
0 R     0  7183  9051  0  90  10 - 12406 -      pts/0    00:00:00 ps
4 S     0  8985  7780  0  80   0 - 57972 do_wai pts/0    00:00:00 su
4 S     0  9051  8985  0  90  10 - 29118 do_wai pts/0    00:00:00 bash
```

由于 **PRI** 是动态调整的，用户无法干涉，但是可以通过 Nice 值来达到一定的优先级调整，**Nice** 就是上述中的 **NI** 值，一般来说 **PRI** 与 **NI** 的相关性 `PRI(new)=PRI(old)+nice`，虽然可以调整 **nice** 的值，由于 **PRI** 是动态调整的，所以不包装调整完之后，最终的 **PRI** 就会变低，优先级变高的

此外，必须要注意，**nice** 值范围

- **nice** 值范围是 **-20~19**
- **root** 可随意调整自己或他人进程的 **Nice** 值，且范围为 **-20~19**
- 一般使用者仅可调整自己进程的 **Nice** 值，且范围仅为 **0~19**（避免一般用户抢占系统资源）
- 一般使用者仅可将 **nice** 值越调越高；比如 **nice** 为 **5**，则未来仅能调整到大于 **5**；

那么调整 **nice** 值有两种方式：

- 一开始执行程序就立即给予一个特定的 **nice** 值：用 **nice** 指令
- 调整某个已经存在的 **PID** 的 **nice** 值：用 **renice** 指令

#### nice：新执行的指令给予新的 nice 值

```bash
nice [-n 数字] command

-n：后面接一个数值，数值范围 -20~19
    
```

```bash
# 范例 1： 用 root 给一个 nice 值为 -5，用于执行 vim，并观察该进程
[root@study ~]# nice -n -5 vim &
[2] 30185
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0  8985  7780  0  80   0 - 57972 do_wai pts/0    00:00:00 su
4 S     0  9051  8985  0  90  10 - 29118 do_wai pts/0    00:00:00 bash
4 T     0 30185  9051  0  85   5 - 10791 do_sig pts/0    00:00:00 vim
0 R     0 30652  9051  0  90  10 - 12407 -      pts/0    00:00:00 ps
# 原本的 bash PRI 为 90，所以 vim 预设为 90，这里给予 nice -5，所以最终 PRI 变成了 85
# 要注意：不一定正好变成 85，因为会动态调整的
```

那么通常什么时候需要将 **nice** 值调大呢？比如：系统的背景工作中，某些比较不重要的进程进行时，比如备份工作，由于备份工作相当耗系统资源，这个时候就可以将备份的指令 **nice** 值调大一些，可以使系统的资源分配更公平

#### renice：已存在进程的 nice 重新调整

```bash
renice [number] PID

```

```bash
# 范例 1：找出自己的 bash PID ,并将该 PID 的 nice 调整到 -5
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0  3426  3372  0  80   0 - 58072 do_wai pts/1    00:00:00 su
4 S     0  3443  3426  0  80   0 - 29059 do_wai pts/1    00:00:00 bash
0 R     0  3487  3443  0  80   0 - 12407 -      pts/1    00:00:00 ps
[root@study ~]# renice -5 3443
3443 (process ID) old priority 0, new priority -5
[root@study ~]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0  3426  3372  0  80   0 - 58072 do_wai pts/1    00:00:00 su
4 S     0  3443  3426  0  75  -5 - 29059 do_wai pts/1    00:00:00 bash
0 R     0  3493  3443  0  75  -5 - 12407 -      pts/1    00:00:00 ps
```

### 系统资源的观察

**top** 可以看到很多系统的资源使用情况，还有其他工具

### free：观察内存使用情况

```bash
free [-b|-k|-m|-g|-h] [-t] [-s N -c N]

选项与参数：
	-b：单位参数；默认是用 k，其他单位对应 bytes、Mbytes、Kbytes、Gbytes
	-t: 输出的最终结果，显示物理内存与 swap 的总量
	-s：可以让系统每几秒输出一次，不间断输出；
	-c：与 -s 同时处理，让 free 列出几次

```

```bash
# 范例 1：显示目前系统的内存容量
[root@study ~]# free -m
#			  总内存		已使用		   剩余							  可用
              total        used        free      shared  buff/cache   available
Mem:           7631         713        6374          15         542        6671
Swap:          4095           0        4095
```

`shared buff/cache` 是缓冲区等使用量，`available` 是可用容量，当系统忙碌时，可以被释放掉，给系统使用

由于系统会把空闲内存拿来做缓冲区之用，所以你系统没有那么繁忙的时候，也会显示内存被用的多的原因，这个是正常的，需要注意的是 **swap**，**swap** 最好不要被使用，而且不要使用超过 **20%** 以上，因为 **swap** 被使用，那么很有可能是物理内存不够用了

### uname：查询系统与核心相关信息

```bash
uname [-asrmpi]

选项与参数：
	-a：所有系统相关的，都列出来
	-s：系统核心名称
	-r：核心的版本
	-m：本系统的硬件名称，例如 i686 或 x86_64
	-p：CPU 的类型，与 -m 类似
	-i：硬件的平台（ix86）
```

```bash
# 范例 1：输出系统的基本信息
[root@study ~]# uname -a 
# 核心名称   主机名			核心版本			  核心建立日期 与 硬件平台
Linux study.centos.mrcode 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 
```

### uptime：观察系统启动时间与工作负载

```bash
[root@study ~]# uptime 
 17:31:46 up 43 min,  2 users,  load average: 0.00, 0.01, 0.05
 # 当前时间	 已开机多久  几个用户登录	平均负载：1、5、15 分钟的平均负载
```

### netstat：追踪网络或插槽文件

该指令常被用在网络的监控方面；**netstat** 基本上的输出分为两大部分：网络与系统自己的进程相关性部分

```bash
netstat -[atunlp]

选项与参数：
	-a：将目前系统上所有的联机、监听、Socket 数据都列出来
	-t：列出 tcp 网络封包的数据
	-u：列出 udp 网络封包的数据
	-n：不以进程的服务名称，以端口号来显示
	-l：列出目前正在网络监听的（listen）的服务
	-p：列出该网络服务的进程 PID
```

```bash
# 范例 1：列出目前系统上已经建立的网络连接与 unix socket 状态
[root@study ~]# netstat 
Active Internet connections (w/o servers)		# 与网络相关部分
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0     36 study.centos.mrcode:ssh 192.168.4.170:50821     ESTABLISHED
Active UNIX domain sockets (w/o servers)	# 与本机的进程自己的相关性（非网络）
Proto RefCnt Flags       Type       State         I-Node   Path
unix  2      [ ]         DGRAM                    12644    /run/systemd/shutdownd
unix  3      [ ]         DGRAM                    7618     /run/systemd/notify
unix  2      [ ]         DGRAM                    7620     /run/systemd/cgroups-agent
unix  5      [ ]         DGRAM                    7634     /run/systemd/journal/socket
```

网络联机部分：

- **Proto**：网络封包协议，主要分为 **TCP** 与 **UDP**。
- **Recv-Q**：非由用户程序连接到此 **socket** 的复制和总 **Bytes** 数
- **Send-Q**：非由远程主机传送过来的 **acknowledged** 总 **Bytes** 数
- **Local Address**：本地端的 **Ip:port**
- **Foreign Address**：远程主机的 **IP:port**
- **State**：联机状态，主要有建立（**ESTABLISED**）、监听（**LISTEN**）

上面有一条数据，含义是：**192.168.4.170:50821** 通过 **TCP** 封包联机到本机端的 **study.centos.mrcode:ssh**，状态是 **ESTABLISHED**；至于更多的知识点这里不深入，在服务器篇讲解

除了网络上的联机之外，**Linux** 系统上的进程是可以接收不同进程所发来的信息，通过 **socket file** 可以在两个进程之间通信。比如 **X Window** 这种需要通过网络连接的软件，新版 **distribution** 以 **socket** 来进行窗口接口的联机沟通。上表中 **socket file** 的输出字段含义为：

- **Proto**：一般是 **unix**
- **RefCnt**：连接到此 **socket** 的进程数量
- **Flags**：联机旗标
- **Type**：**socket** 存取的类型。主要有 **STREAM**：确认联机、**DGRAM**：不需确认 两种
- **State**：若为 **CONNECTED** 表示多个进程之间已经联机建立
- **PATH**：连接到此 **socket** 的相关程序路径，或则是相关数据输出的路径

```bash
# 范例 2：找出目前系统上已在监听的网络联机与 PID
[root@study ~]# netstat -tulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1380/cupsd          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1579/master         
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      3765/sshd: mrcode@p 
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd           
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1973/dnsmasq        
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1379/sshd           
tcp6       0      0 ::1:631                 :::*                    LISTEN      1380/cupsd          
tcp6       0      0 ::1:25                  :::*                    LISTEN      1579/master         
tcp6       0      0 ::1:6010                :::*                    LISTEN      3765/sshd: mrcode@p 
tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd           
tcp6       0      0 :::22                   :::*                    LISTEN      1379/sshd           
udp        0      0 192.168.122.1:53        0.0.0.0:*                           1973/dnsmasq        
udp        0      0 0.0.0.0:67              0.0.0.0:*                           1973/dnsmasq        
udp        0      0 0.0.0.0:111             0.0.0.0:*                           1/systemd           
udp        0      0 127.0.0.1:323           0.0.0.0:*                           938/chronyd         
udp        0      0 0.0.0.0:41378           0.0.0.0:*                           953/avahi-daemon: r 
udp        0      0 0.0.0.0:672             0.0.0.0:*                           927/rpcbind         
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           953/avahi-daemon: r 
udp6       0      0 :::111                  :::*                                1/systemd           
udp6       0      0 ::1:323                 :::*                                938/chronyd         
udp6       0      0 :::672                  :::*                                927/rpcbind
# 最后一个字段是 PID 与进程的指令名称
```

```bash
# 范例 3：将上述的 0 0.0.0.0:41378 网络服务关闭
[root@study ~]# kill -9 953
[root@study ~]# killall -9 avahi-daemon
   
```

对于非正常的关闭服务方法就用暴力的 kill -9，正常的关闭方式，下个章节讲解

### dmesg：分析核心产生的信息

系统在开机的时候，核心会去侦测系统的硬件，那么硬件的检测信息由于开机过程中要么一闪而过，要么没有显示在屏幕上，可以使用 **dmesg** 来查看

从系统开机起，核心产生的信息都会记录到内存中，通过 **dmesg** 可以查询到，信息过多时可以通过 **more** 指令查看

```bash
# 范例 1：输出所有的核心开机时的信息
[root@study ~]# dmesg | more
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.10.0-1062.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 
4.8.5-36) (GCC) ) #1 SMP Wed Aug 7 18:08:02 UTC 2019
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto spect
re_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet LANG=zh_CN.UTF-8
[    0.000000] e820: BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
--More--


# 范例 2：找到硬盘相关信息
[root@study ~]# dmesg | grep -i 'sda'
[    2.632630] sd 2:0:0:0: [sda] 85491712 512-byte logical blocks: (43.7 GB/40.7 GiB)
[    2.632651] sd 2:0:0:0: [sda] Write Protect is off
[    2.632653] sd 2:0:0:0: [sda] Mode Sense: 00 3a 00 00
[    2.632662] sd 2:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
[    2.643988]  sda: sda1 sda2 sda3 sda4 sda5 sda6 sda7 sda8
[    2.644394] sd 2:0:0:0: [sda] Attached SCSI disk
[    4.616881] XFS (sda2): Mounting V5 Filesystem
[    4.636376] XFS (sda2): Ending clean mount

```

### vmstat：侦测系统资源变化

**vmstat** 可以侦测 **CPU**、内存、磁盘输入输出状态等信息。比如可以了解一台繁忙的系统到底是哪个环节最耗时间，可以使用 **vmstat** 分析看看，常见选项与参数如下：

```bash
vmstat [-a] [延迟 [总计侦测次数]]		# CPU/内存等信息
vmstat [-fs]										 # 内存相关
vmstat [-S 单位]									# 设置显示数据的单位
vmstat [-d]											 # 与磁盘有关
vmstat [-p 分区槽]								 # 与磁盘有关

选项与参数：
	-a：使用 inactive/active（是否活跃）取代 buffer/cache 的内存输出信息
	-f：开机到目前为止，系统复制（fork）的进程数
	-s：将一些事件（开机到目前为止）导致的内存变化情况列表说明
	-S：后面可以接单位，例如 k、M 等
	-d：列出磁盘的读写总量统计表
	-p：后面列出分区槽，可显示该分区槽的读写总量统计表
```

```bash
# 范例 1：统计目前主机 CPU 状态，每秒一次，总共 3 次
[root@study ~]# vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 450296   2116 346828    0    0   501    36  181  320  2  3 95  0  0
 0  0      0 450156   2116 346860    0    0     0     0  163  223  2  3 95  0  0
 0  0      0 450156   2116 346860    0    0     0     0  273  388  3  5 91  0  0
```

还可以不限制次数，就一直统计字段说明如下：

- **procs**：进程

  - **r**：等待运行中的进程数量
  - **b**：不可被唤醒的进程数量

  **rb** 越多表示系统越繁忙。因为系统太忙，导致很多进程无法被执行或一直在等待而无法被唤醒

- **memory**：内存

  - **swpd**：虚拟内存被使用的容量
  - **free**：未被使用的内容容量
  - **buff**：用于缓冲存储器
  - **cache**：用于高速缓存

  这里的含义与 **free** 指令一致

- **swap**：内存交换空间

  - **si**：由磁盘中将进程取出的量
  - **so**：由于内存不足而将没用到的进程写入到磁盘的 swap 的容量

  如果 **si**、**so** 的数值太大，表示内存的数据常常得在磁盘与主存储器之间传来传去，效率很低

- **io**：磁盘读写

  - **bi**：由磁盘读入的区块数量
  - **bo**：写入到磁盘去的区块数量

  如果这部分数值越高，代表系统的 **I/O** 非常忙碌

- **system**：系统

  - **in**：每秒被中断的进程次数
  - **cs**：每秒钟进行的事件切换次数

  这两个值越大，代表系统与接口设备的沟通非常频繁，接口设备包括磁盘、网卡、时钟等

- **CPU**：

  - **us**：非核心层的 **CPU** 使用状态
  - **sy**：核心层所使用的 **CPU** 状态
  - **id**：闲置的状态
  - **wa**：等待 **I/O** 所耗费的 **CPU** 状态
  - **st**：被虚拟机（**virtual machine**）所盗用的 **CPU** 使用状态（**2.6.11**）

练习机上看不到忙碌的数据，如果有一天，你的系统非常忙碌，可以使用该指令来分析是哪里出现了问题

```bash
# 范例 2：系统上面所有的磁盘读写状态
[root@study ~]# vmstat -d
disk- ------------reads------------ ------------writes----------- -----IO------
       total merged sectors      ms  total merged sectors      ms    cur    sec
sda     7640      1  709893    6377   2486    351   54323    8478      0      5
sdb      116      0    5384      27      0      0       0       0      0      0
sr0        0      0       0       0      0      0       0       0      0      0
dm-0    7072      0  661717    6054   2611      0   45902   10871      0      5
dm-1      88      0    4408      21      0      0       0       0      0      0
dm-2     103      0   10834      58     23      0    4325      56      0      0
```

至于上面的字段含义，可以通过 **man vmstat** 查阅

## 特殊文件与进程

在第 6 章中讲到特殊权限 **SUID**、**SGID**、**SBIT**，那么这些权限对于你的 **进程** 是如何影响的？进程用到的系统资源，比如硬盘资源，使用 **umount** 硬盘时，出现提示 「**device is busy**」的提示是怎么回事？

### 具有 SUID、SGID 权限的指令执行状态

**SUID** 的权限与进程的相关性非常大，**SUID** 的程序是如何被一般用户执行，具有什么特色？

- **SUID** 权限仅对二进制程序（**binary program**）有效
- 执行者对于该进程需要具有 **x** 的可执行权限
- 本权限仅在执行程序的过程中有效（**run-time**）
- 执行者将具有该程序拥有者（**owner**）的权限

所以，整个 **SUID** 的权限会生效是由于**具有该权限的程序被触发**，一个进程表示一个程序的运行，所以执行者可以具有程序拥有者的权限就是在该程序变成进程的时候

比如执行了 **passwd** 后你就具有 **root** 的权限？是因为你再触发 **passwd** 后，会取得一个新的进程与 **PID**，该 **PID** 产生时通过 **SUID** 来给予该 **PID** 特殊的权限设置

下面使用 **mrcode** 登录系统并执行 **passwd** 后，通过工作控制来理解

```bash
[mrcode@study ~]$ export LANG=C
[mrcode@study ~]$ passwd
Changing password for user mrcode.
Changing password for mrcode.
(current) UNIX password: 		# 这里按下 ctrl + z，并按下 enter 键

[1]+  Stopped                 passwd
[mrcode@study ~]$ pstree -uA
systemd-+-ModemManager---2*[{ModemManager}]
        |-sshd-+-sshd---sshd(mrcode)-+-bash---su(root)---bash
        |      |                     |-bash---top
        |      |                     |-bash---sleep
        |      |                     `-sftp-server
        |      `-sshd---sshd(mrcode)-+-bash-+-passwd(root)
        |                            |      `-pstree
        |                            |-bash---top
        |                            |-bash---sleep
        |                            `-sftp-server


```

从上面的进程来看，在执行 **passwd** 前是 **mrcode** 的权限，**passwd** 则是 **root** 权限，**passwd** 是由 **bash** 衍生出来的，但是权限不一样，这样一来就能理解为什么不同程序所产生的权限不同了，是由于 **SUID** 程序运行过程中产生的进程的关系。

```bash
[mrcode@study ~]$ type passwd
passwd is hashed (/usr/bin/passwd)
[mrcode@study ~]$ ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 27856 Aug  9  2019 /usr/bin/passwd
#可以看到，的确该指令也有 s 权限

# 还可以通过以下指令查找 SUID/SGID 的文件
find / -perm /6000

```

### `/proc/*` 代表的意义

进程在内存中，内存中的数据都是写入到 `/proc/*` 目录下的，可以直接查看该目录

```bash
[mrcode@study ~]$ ll /proc/
total 0
dr-xr-xr-x.  9 root           root                         0 Mar 15 20:13 1
dr-xr-xr-x.  9 root           root                         0 Mar 15 20:13 10
dr-xr-xr-x.  9 root           root                         0 Mar 15 20:13 11
...
dr-xr-xr-x.  2 root           root                         0 Mar 15 20:54 sysvipc
-r--r--r--.  1 root           root                         0 Mar 15 20:54 timer_list
-rw-r--r--.  1 root           root                         0 Mar 15 20:54 timer_stats
dr-xr-xr-x.  4 root           root                         0 Mar 15 20:54 tty
-r--r--r--.  1 root           root                         0 Mar 15 20:16 uptime
-r--r--r--.  1 root           root                         0 Mar 15 20:54 version
-r--------.  1 root           root                         0 Mar 15 20:54 vmallocinfo
-r--r--r--.  1 root           root                         0 Mar 15 20:54 vmstat
-r--r--r--.  1 root           root                         0 Mar 15 20:54 zoneinfo
```

基本上，目前主机上面的各个进程的 PID 都是以目录的形态存在该目录中。如第 **1** 行的 **PID** 为 **1**，它是开机执行的第一个程序 **systemd**，该 **PID** 的所有相关信息都写入在 `/proc/1/*` 下面

```bash
[root@study ~]# ll /proc/1
total 0
dr-xr-xr-x. 2 root root 0 Mar 15 20:48 attr
-rw-r--r--. 1 root root 0 Mar 15 20:53 autogroup
-r--------. 1 root root 0 Mar 15 20:53 auxv
-r--r--r--. 1 root root 0 Mar 15 20:13 cgroup
-r--r--r--. 1 root root 0 Mar 15 20:13 cmdline			# 指令串
-r--------. 1 root root 0 Mar 15 20:13 environ			# 一些环节变量
lrwxrwxrwx. 1 root root 0 Mar 15 20:13 exe -> /usr/lib/systemd/systemd
```

里面数据很多，可以查询下 **cmdline** 的内容

```bash
[root@study ~]# cat /proc/1/cmdline
/usr/lib/systemd/systemd--switched-root--system--deserialize22
```

上面指令显示了是以什么参数启动的 **systemd** 指令，这个是针对 **PID** 有关的内容，下面是针对整个 **Linux** 系统相关的参数，对应与 **/proc** 目录下的文件如下

| 文件名              | 文件内容                                                     |
| ------------------- | ------------------------------------------------------------ |
| `/proc/cmdline`     | 加载 kernel 时所下达的相关指令与参数，查询此文件，可了解指令是如何启动的 |
| `/proc/cpuinfo`     | 本机的 CPU 相关信息，包含频率、类型与计算功能等              |
| `/proc/devices`     | 系统各个主要装置的主要装置代号，与 mknod 有关                |
| `/proc/filesystems` | 目前系统已经加载的文件系统                                   |
| `/proc/interrupts`  | 目前系统上 IRQ 分配状态                                      |
| `/proc/ioports`     | 目前系统上各个装置所配置的 I/O 地址                          |
| `/proc/kcore`       | 内存大小，很大？不要读取该文件                               |
| `/proc/loadavg`     | top 以及 uptime 的三个平均数值就是记录在这里的               |
| `/proc/meminfo`     | 使用 free 列出的内存信息，在这里也可以查询到                 |
| `/proc/modules`     | 目前我们 LInux 已经加载的模块列表，可以看成是驱动程序        |
| `/proc/mounts`      | 系统已经挂载的数据，就是用 mount 指令查询出来的数据          |
| `/proc/swaps`       | 系统挂载的内存在哪里？使用掉的 partition 记录在这里          |
| `/proc/partitions`  | 使用 fsidk -l 会出现目前所有的 partition，在该文件中也有记录 |
| `/proc/uptime`      | 使用 uptime 出现的信息                                       |
| `/proc/version`     | 核心的版本，使用 uname -a 显示的信息                         |
| `/proc/bus/*`       | 一些总线的装置，还有 USB 的装置也记录在这里                  |

这些文件内容建议使用 **cat** 去查阅看看，不必深入了解，如果未来你要写某些工具软件，那么这个目录下相关文件可能会对你有点帮助

### 查询已开启文件或已执行进程开启之文件

还有一些与进程相关的指令可以参考与应用

#### fuser：由文件（或文件系统）找出正在使用该文件的进程

```bash
fuser [-umv] [-k [i] [signal]] file/dir

选项与参数：
	-u：除了进程的 PID 之外，同时列出该进程的拥有者
	-m：后面接的文件名会主动的上提到该文件系统的最顶层，对 umount 不成功很有效
	-v：可以列出每个文件与进程还有指令的完整相关性
	-k：找出使用该文件/目录的 PID，并试图以 SIGKILL 这个信号给予该 PID
	-i：必须与 -k 配合使用，在删除 PID 之前会先询问使用者
	-signal：例如 -1 -15 等，若不加的话，预设是 -9：SIGKILL
```

```bash
# 范例 1：找出目前所在目录的使用 PID、所属账户、权限
[root@study ~]# fuser -uv .
                     USER        PID ACCESS COMMAND
/root:               root       2604 ..c.. (root)bash
```

有一个进程属于 **root**，而 **ACCESS** 项则略为复杂一点：

- **c**：此进程在当前的目录下（非次目录）
- **e**：可被触发为执行状态
- **f**：是一个被开启的文件
- **r**：代表顶层目录（**root directory**）
- **F**：该文件被开启了，不过在等待回应中
- **m**：可能为分享的动态函数库

如果想知道某个文件系统下又多少进程正在占用该文件系统时，可以使用 `-m` 选项

下面做几个简单测试，包括实体文件系统挂载与 /proc 虚拟文件系统的内容，看看有多少的进程对这些挂载点或其他目录的使用状态

```bash
# 范例 2：找到所有使用到 /proc 这个文件系统的进程
[root@study ~]# fuser -uv /proc/
                     USER        PID ACCESS COMMAND
/proc:               root     kernel mount (root)/proc
                     rtkit       834 .rc.. (rtkit)rtkit-daemon

[root@study ~]# fuser -muv /proc/
                     USER        PID ACCESS COMMAND
/proc:               root     kernel mount (root)/proc
                     root          1 f.... (root)systemd
                     root        589 f.... (root)systemd-journal
                     rtkit       834 .rc.. (rtkit)rtkit-daemon
                     root        844 f.... (root)udisksd
                     root        929 f.... (root)NetworkManager
                     root       1277 F.... (root)libvirtd
                     root       1638 F.... (root)X
                     gdm        1693 f.... (gdm)gnome-shell
                     root       1759 f.... (root)packagekitd
                     mrcode     2280 f.... (mrcode)top
                     mrcode     7722 f.... (mrcode)top
# 这就能看到有几个程序在对该目录进行存取


# 范例 3：找到所有使用到 /home 这个文件系统的进程
[root@study ~]# echo $$
2604				# 先确定下自己的 bash 的进程 PID
[root@study ~]#  cd /home/
[root@study home]# fuser -muv .
                     USER        PID ACCESS COMMAND
/home:               root     kernel mount (root)/home
                     mrcode     1346 ..c.. (mrcode)bash
                     mrcode     1371 ..c.. (mrcode)bash
                     mrcode     1378 ..c.. (mrcode)sleep
                     mrcode     1399 ..c.. (mrcode)sleep
                     mrcode     1958 ..c.. (mrcode)bash
                     mrcode     1991 ..c.. (mrcode)sftp-server
                     mrcode     1992 ..c.. (mrcode)bash
                     mrcode     2280 ..c.. (mrcode)top
                     root       2604 ..c.. (root)bash			# 看这里，自己的 bash 存在列表中
                     mrcode     7294 ..c.. (mrcode)bash
                     mrcode     7358 ..c.. (mrcode)sftp-server
                     mrcode     7362 ..c.. (mrcode)bash
                     mrcode     7722 ..c.. (mrcode)top
                     root       8884 ..c.. (root)passwd

[root@study home]# cd ~
[root@study ~]# umount /home/
umount: /home: target is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
# 通过 fuser 知道有好几个进程在该目录下运行，可以通过如下的方式一个一个删除
[root@study ~]# fuser -mki /home/
/home:                7294c  7358c  7362c  7722c  8884c 19238c 19289c 19291c 19601c 25650c 25674c 25685c 25746c
Kill process 7294 ? (y/N)
# 以上指令有一个问题，颇为棘手，就是很容易杀到自己 bash 的进程，那么久直接把直接踢掉了
# 不知道这个这么排除掉是出方便的

```

上面可以针对整个文件系统，其实也可以针对单一文件

```bash
# 范例 4：找到 /run 下属于 FIFO 类型的文件，并找出存取该文件的进程
[root@study ~]# find /run -type p
/run/dmeventd-client
/run/dmeventd-server
/run/systemd/inhibit/7.ref
/run/systemd/inhibit/6.ref
/run/systemd/inhibit/5.ref
/run/systemd/inhibit/4.ref
/run/systemd/inhibit/2.ref
/run/systemd/inhibit/1.ref
/run/systemd/sessions/13.ref
/run/systemd/sessions/5.ref
/run/systemd/sessions/c1.ref
/run/systemd/initctl/fifo

# 随便找到文件测试
[root@study ~]# fuser -uv /run/systemd/sessions/c1.ref
                     USER        PID ACCESS COMMAND
/run/systemd/sessions/c1.ref:
                     root        842 f.... (root)systemd-logind
                     root       1649 F.... (root)gdm-session-wor
# 通常系统的 FIFO 文件都会放置到 /run 下，通过该方式来追踪该文件存取的 process
# 同样也能够看到系统有多忙碌（进程多当然就忙碌）

```

**fuser** 的重点是可以让我们了解到某个文件系统或文件目前正在被哪些进程所使用

#### lsof：列出被进程所开启的文件名

fuser 是通过文件或则装置名去找使用它的进程，而 lsof 则是通过某个进程去找它开启或使用的文件与装置

```bash
lsof [-aUu] [+d]

选项与参数：
	-a：多想数据需要同时成立才显示出结果时
	-U：仅列出 Unix like 系统的 socket 文件类型
	-u：后面接 username，列出该使用者相关进程所开启的文件
	+d：后面接目录，找出某个目录下已经被开启的文件


```

```bash
# 范例 1：列出目前系统上所有已经被开启的文件与装置
[root@study ~]# lsof
libvirtd   1277 1318           root  mem       REG              253,0     53848    9645351 /usr/lib64/libavahi-common.so.3.5.3
libvirtd   1277 1318           root  mem       REG              253,0    155784    8569818 /usr/lib64/libselinux.so.1
libvirtd   1277 1318           root  mem       REG              253,0     37056    8655202 /usr/lib64/libacl.so.1.1.0
# 文件很多很多，直接刷屏了


# 范例 2：仅列出关于 root 的所有进程开启的 socket 文件
[root@study ~]# lsof -u root -a -U
COMMAND     PID USER   FD   TYPE             DEVICE SIZE/OFF   NODE NAME
systemd       1 root   12u  unix 0xffff8922437ed800      0t0  12513 /run/systemd/private
systemd       1 root   13u  unix 0xffff892243dc2c00      0t0  25917 /run/systemd/journal/stdout
systemd       1 root   15u  unix 0xffff892243e71800      0t0  25941 /run/systemd/journal/stdout
systemd       1 root   16u  unix 0xffff892243e8fc00      0t0  25942 /run/systemd/journal/stdout
systemd       1 root   17u  unix 0xffff892243e6ec00      0t0  26002 /run/systemd/journal/stdout
systemd       1 root   18u  unix 0xffff892243e6dc00      0t0  26009 /run/systemd/journal/stdout
systemd       1 root   23u  unix 0xffff89224359a800      0t0   7620 /run/systemd/notify
# 注意 -a 参数，分别执行 lsof -u root 及 lsof -U 信息都不同
# -a 取他们的交集结果


# 范例 3：列出目前系统上所有被启动的周边装置
[root@study ~]# lsof +d /dev/
COMMAND     PID           USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
systemd       1           root    0u   CHR                1,3      0t0 5342 /dev/null
systemd       1           root    1u   CHR                1,3      0t0 5342 /dev/null
systemd       1           root    2u   CHR                1,3      0t0 5342 /dev/null
systemd       1           root   27r   CHR             10,235      0t0 7250 /dev/autofs
systemd       1           root   30u  unix 0xffff8922436ce000      0t0 7645 /dev/log
kdevtmpfs    13           root  cwd    DIR                0,5     3340    3 /dev
# 因为都在目录中，所以搜索目录即可

# 范例 4：列出 root 的 bash 程序开启的文件
[root@study ~]# lsof -u root | grep bash
ksmtuned    921 root  txt       REG              253,0    964600     309027 /usr/bin/bash
bash      20030 root  cwd       DIR              253,0      4096   25165889 /root
bash      20030 root  rtd       DIR              253,0       251         64 /
bash      20030 root  txt       REG              253,0    964600     309027 /usr/bin/bash
bash      20030 root  mem       REG              253,0 106075056     309022 /usr/lib/locale/locale-archive
bash      20030 root  mem       REG              253,0     61624    8548289 /usr/lib64/libnss_files-2.17.so
bash      20030 root  mem       REG              253,0   2156160    8532847 /usr/lib64/libc-2.17.so
bash      20030 root  mem       REG              253,0     19288    8532853 /usr/lib64/libdl-2.17.so
bash      20030 root  mem       REG              253,0    174576    8548350 /usr/lib64/libtinfo.so.5.9
bash      20030 root  mem       REG              253,0    163400    8532840 /usr/lib64/ld-2.17.so
bash      20030 root  mem       REG              253,0     26254   16946906 /usr/lib64/gconv/gconv-modules.cache
bash      20030 root    0u      CHR              136,0       0t0          3 /dev/pts/0
bash      20030 root    1u      CHR              136,0       0t0          3 /dev/pts/0
bash      20030 root    2u      CHR              136,0       0t0          3 /dev/pts/0
bash      20030 root  255u      CHR              136,0       0t0          3 /dev/pts/0
```

可以通过范例 4 找出某个进程是否有启用哪些信息

### pidof：找出某个正在运行的程序的 PID

```bash
pidof [-sx] program_name

选项与参数：
	-s：仅列出一个 PID 而不列出所有的 PID
	-x：同时列出该程序可能的 PPID 那个进程的 PID

```

```bash
# 范例 1：列出目前系统上 systemd 以及 rsyslogd 这两个程序的 PID
[root@study ~]# pidof systemd rsyslogd
1 1265
# 结果显示的是两个 PID

```

**pidof** 指令较简单，可配合 **pas aux** 与正则表示法，就可以很轻易的找到你想要的进程内容了。如果要找的是 **bash**，那就 **pidof bash** ，就列出一堆 **PID** 号码了

## SELinux 初探

**CentOS 5.x** 之后，**SELinux** 已经是个非常完备的核心模块了，尤其是 **CentOS** 提供了很多管理 **SELinux** 的指令与机制，因此在整理架构上面是单纯且容易操作管理的，所以在没有自行开发网络服务软件以及使用其他第三方协力软件的情况下，也就是全部使用 **CentOS** 官方提供的软件来使用我们服务器的情况下，建议不要关闭 **SELinux**

### 什么是 SELinux

**Security Enhanced Linux** 的缩写 **SELinux**，字面意思是安全强化的 **LInux**。至于强化的是哪个部分？下面来了解下

### 当初设计的目标：避免资源的误用

**SELinux** 是由美国国家安全局（**NSA**）开发的，需求来源于内部员工资源误用导致系统出现问题；

资源误用：将一个 `/var/www/html/` 目录权限设置成 **777**，那么当启动 **www** 服务器软件，就意味着这个软件触发的进程拥有对该目录写入的权限，只要通过该进程服务器对目录大量写入，就会导致系统硬盘资源被爆破

**SELinux** 是在进行进程、文件等西部权限设置依据的一个核心模块，由于启动网络服务的也是进程，因此刚好也能够控制网络服务是否能存取系统资源的一道关卡

在讲解 **SELinux** 之前，先回顾一下之前讲到的：系统文件权限与用户之间的关系

### 传统的文件权限与账户关系：自主式访问控制 DAC

第 **13** 章中讲到：系统账户主要分为系统管理员（**root**）与一般用户，他们能否使用系统上的文件资源与 **rwx** 权限设置有关。（各种权限设置对 **root** 无效）。当某个进程想要对文件进行存取时，系统会根据该进程的拥有者、群组，并比对文件的权限，若通过权限检查，就可以存取该文件

这种存取文件的方式被称为 **自主式访问控制 Discretionary Access Controller 简称 DAC**，基本上就是依据进程的拥有者与文件资源的 **rwx** 权限来决定有无存取的能力。**DAC** 有如下困扰：

- **root** 具有最高的权限：只要取得属于 **root** 的进程，那么就很危险
- 使用者可以取得进程来变更文件资源的访问权限：如果将某个目录权限不小心设置为 **777**，由于对任何人的权限会变成 **rwx**，因此该目录就会被任何人所任意存取

### 以政策规则规定特定进程读取特定文件：委任式访问控制 MAC

为了避免 **DAC** 的困扰，**SELinux** 导入了委任式访问控制 **Mandatory Access Control** 简称 **MAC**

**MAC** 可以针对特定的进程与特定的文件资源来进行权限的控制。即使你是 **root**，那么在使用不同的进程时，你所能取得的权限并不一定是 **root**，而需要看当时该进程的设置。如此一来针对控制的「主体」变成了「进程」而不是使用者，但是真个系统进程很多、文件也很多，一项一项控制太麻烦，所以 **SELinux** 也提供一些预设的政策 **Policy** ，并在该政策内提供多个规则 **rule**，让你可以选择是否启用该控制规则

在该种模式下，进程能够活动的空变小了。比如：**www** 服务器软件达成进程为 **httpd** 这个程序，默认情况下， **httpd** 仅能在 `/var/www` 目录下存取文件，如果 httpd 进程要去其他目录存储数据时，除了规则设置要开放外，目标目录也要设置成 **httpd** 可读取的模式 **type** 才行，限制非常多，所以，即使 **httpd** 这个进程被黑客取得了控制权限，它也无权限浏览其他的目录文件

简单说，针对 **Apache** 这个 **www** 网络服务使用 **DAC** 或 **MAC** 的结果来说，两者的关系可用下图来说明

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/selinux.png)

传统的进程与文件的 **rwx** 方式，在这中间增加了 **SELinux**  安全性本文 *规则* ，通过了这些规则之后，才和传统的进程与文件的 **rwx** 方式一致。

笔者理解为是通过拦截器的方式，出台了 **SELinux** ，前面通过 **SElinux** 拦截细化权限，符合要求的再去到传统的方式，这样一来就对传统的加强了。

### 安全性本文 Security Context

**CentOS 7.x** 的 **target** 政策提供了非常多的规则，只需要如何开启关闭某项规则即可。

安全性本文则非常麻烦，可能需要自行配置它，比如你常常设置文件的 **rwx** 权限，那么这个安全性本文就类似，可以看成是 **SELinux** 中的 **rwx**

安全性本文存在于主体进程中与目标文件资源中，物理位置是放在文件的 **inode** 中，因此主体进程想要读取目标文件资源时，同样需要读取 **inode**，这就可以对比安全性本文一级 **rwx** 等权限是否正确了。

观察安全性本文可使用 `ls -Z` ，但是前提是需要启动 **SELinux** 才行，下个小节会介绍如何启动 **SELinux**，这里先介绍知识点

```bash
[root@study ~]# ls -Z
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 accountadd.sh
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 accountadd.txt
-rwxr--r--+ root root unconfined_u:object_r:admin_home_t:s0 acl_test1
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 addaccount2.sh
-rw-------. root root system_u:object_r:admin_home_t:s0 anaconda-ks.cfg
-rw-r--r--. root root system_u:object_r:admin_home_t:s0 initial-setup-ks.cfg
# 上述字段很长的那一栏就是安全性本文了
```

安全性本文主要用冒号分割为三个字段，含义如下：

- **identify**：身份

  相当于账户方面的身份识别，常见有几下几种类型

  - **unconfined_u**：不受限的用户

    该文件来自不受限的进程所产生的，一般来说，可以使用可登录账号来取得 **bash**，预设的 **bash** 环境是不受 **SELinux** 管制的，因为 **bash** 并不是什么特别的网络服务，因此在该 **bash** 进程所产生的文件，其身份识别大多就是该类型了

  - **system_u**：系统用户

    基本上，如果是系统会软件本身所提供的文件，大多就是该类型，如果是用户通过 **bash** 自己建立的文件，大多则是不受限的 **unconfined_u** 身份，如果是网络服务所产生的文件，或则是系统服务运行过程中所产生的文件，则大部分是 **system_u**

- **role**：角色

  通过该字段，可以知道这个资料是属于进程、文件资源还是代表使用者，一般的角色有：

  - **object_r**：代表的是文件或目录等文件资源
  - **system_r**：代表的是进程，不过一般使用者也会被指定为 system_r

- **type**：类型，最重要

  在预设的 **targeted** 政策中， **identify** 与 **role** 字段基本上是不重要的，而 **type** 是最重要的，基本上，一个主体进程能不能读取到这个文件资源，与类型字段有关，而类型字段在文件与进程的定义不相同：

  - **type**：在文件资源（**object**）上面称为类型（**type**）
  - **domain**：在主体进程（**subject**）则称为领域（**domain**）

  **domain** 需要与 **type** 搭配，则该进程才能够顺利的读取文件资源

### 进程与文件 SELinux type 字段的相关性

通过身份识别与角色字段的定义，我们可以大概某个进程所代表的意义

```bash
# 观察下系统 进程的 SELinux 相关信息
[root@study ~]# ps -eZ
LABEL                             PID TTY          TIME CMD
system_u:system_r:init_t:s0         1 ?        00:00:01 systemd
system_u:system_r:kernel_t:s0       2 ?        00:00:00 kthreadd
system_u:system_r:kernel_t:s0       4 ?        00:00:00 kworker/0:0H
system_u:system_r:kernel_t:s0       5 ?        00:00:00 kworker/u2:0
...
system_u:system_r:sshd_t:s0-s0:c0.c1023 2344 ? 00:00:00 sshd
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2350 ? 00:00:00 sshd
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2353 pts/0 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2415 pts/0 00:00:00 su
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2424 pts/0 00:00:00 bash
system_u:system_r:kernel_t:s0    2726 ?        00:00:00 kworker/u2:2
system_u:system_r:kernel_t:s0    2778 ?        00:00:00 kworker/0:1
system_u:system_r:kernel_t:s0    2836 ?        00:00:00 kworker/0:3
system_u:system_r:kernel_t:s0    2877 ?        00:00:00 kworker/0:0
system_u:system_r:ksmtuned_t:s0  2885 ?        00:00:00 sleep
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2886 pts/0 00:00:00 ps

# 基本上进程主要分为两大类，
# 一种是系统有受限的 system_u:system_r，
# 另一种可能是用户自己的，比较不受限的进程（通常是本机用户自己执行的程序 ） unconfined_u:unconfined_r

# unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2424 pts/0 00:00:00 bash
# 比如上面这个进程，就是我们自己执行命令所在的 bash
```

基本上，这些对于资料在 **targeted** 政策下的对应对下

| 身份识别         | 角色             | 对应在 targeted 的意义                                       |
| ---------------- | ---------------- | ------------------------------------------------------------ |
| **unconfined_u** | **unconfined_r** | 一般可登陆使用者的进程，比较没有受限的进程。大多数都是用户已经顺利登陆系统（不论是网络还是本机登陆来取得可用的 **shell**）后，所用来操作系统的进程，如 **bash x window** 相关富安居等 |
| **system_u**     | **system_r**     | 由于为系统账户，因此是非交谈式的系统运行进程，大多数的系统进程均是这种类型 |

如上所述，在预设的 **target** 政策下，最重要的是 **type** 字段，主体与目标之间是否具有可读写的权限，与进程的 **domain** 与文件的 **type** 有关。这两者的关系可以使用 **crond** 以及他的配置文件来说明

```bash
# 1. 先看看 crond 这个进程的安全本文内容
[root@study ~]# ps -eZ | grep cron
system_u:system_r:crond_t:s0-s0:c0.c1023 1398 ? 00:00:00 atd
system_u:system_r:crond_t:s0-s0:c0.c1023 1400 ? 00:00:00 crond
# 这个安全本文的类型名称为 crond_t 格式

# 2. 看看 /usr/ssbin/crond 、 /etc/cron.d、/etc/cron.d 文件的安全本文内容
[root@study ~]# ll -Zd /usr/sbin/crond /etc/crontab /etc/cron.d
drwxr-xr-x. root root system_u:object_r:system_cron_spool_t:s0 /etc/cron.d
-rw-r--r--. root root system_u:object_r:system_cron_spool_t:s0 /etc/crontab
-rwxr-xr-x. root root system_u:object_r:crond_exec_t:s0 /usr/sbin/crond
```

执行 `/usr/ssbin/crond` 后，该程序编程的进程 **domain** 类似是 **crond_t**，它能够读取的配置文件是 `system_cron_spool_t` 类型。因此无论 `/etc/crontab`与 `/etc/cron.d` 以及 `/var/spool/cron` 都会是相关的 SELinux 类型（`/var/spool/cron` 为 `user_cron_spool_t` 类型）。下面图示说明

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/crond-domain.png" style="zoom:67%;" />

1. **crond** 执行后，具有 **crond_exec_t** 类型
2. 该文件类型会造成主体进程 **Subject** 具有 **crond** 这个领域 **domain**，政策针对这个领域有许多规则，其中就包括可以读取的目标资源类型
3. 由于 **crond domain** 被设置为可以读取 **system_cron_spool_t** 类型的目标文件 **object**，因此你的配置文件放到 `/etc/cron.d/` 目录下，就能够被 **crond** 进程读取了
4. 但是最终能不能读到正确的资料，还需要看传统的 **rwx** 是否符合 **Linux** 的权限规范

下面来测试上述说明

```bash
# 1. 假设你因为不熟悉的缘故，因此是在 root 家目录建立一个如下的 cron 设置
[root@study ~]# vim checktime
10 * * * * root sleep 60s

# 2. 发现文件放错目录了，又不想要保留副本，因此使用 mv 移动到正确的目录
[root@study ~]# mv checktime /etc/cron.d/
[root@study ~]# ll /etc/cron.d/checktime 
-rw-r--r--. 1 root root 26 Mar 17 13:12 /etc/cron.d/checktime
# 权限是 644，任何进程都可以读取

# 3. 强制重新启动 crond，然后查看登录日志
[root@study ~]# systemctl restart crond          
[root@study ~]# tail /var/log/cron
Mar 17 13:01:01 study run-parts(/etc/cron.hourly)[3889]: finished mcelog.cron
Mar 17 13:10:01 study CROND[3972]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Mar 17 13:14:01 study crond[1400]: ((null)) Unauthorized SELinux context=system_u:system_r:system_cronjob_t:s0-s0:c0.c1023 file_context=unconfined_u:object_r:admin_home_t:s0 (/etc/cron.d/checktime)
Mar 17 13:14:01 study crond[1400]: (root) FAILED (loading cron table)
Mar 17 13:15:08 study crond[1400]: (CRON) INFO (Shutting down)
Mar 17 13:15:08 study crond[4073]: (CRON) INFO (RANDOM_DELAY will be scaled with factor 13% if used.)
Mar 17 13:15:08 study crond[4073]: ((null)) Unauthorized SELinux context=system_u:system_r:system_cronjob_t:s0-s0:c0.c1023 file_context=unconfined_u:object_r:admin_home_t:s0 (/etc/cron.d/checktime)
Mar 17 13:15:08 study crond[4073]: (root) FAILED (loading cron table)
Mar 17 13:15:08 study crond[4073]: (CRON) INFO (running with inotify support)
Mar 17 13:15:08 study crond[4073]: (CRON) INFO (@reboot jobs will be run at computer's startup.)

# 上述日志中有 Unauthorized 的信息，表示有错误，因为原本的安全本文与文件的实际安全本文无法搭配的缘故，
# 信息还列出了 SELinux context 与 file_context 的信息，表示的确不匹配

```

### SELinux 三种模式的启动、关闭与观察

并非所有的 **Linux distribution** 都支持 **SELinux**，**CentOS 7.x** 本身就有支持 **SELinux**，所以你不需要自行编译 **SELinux** 到你的 **Linux** 核心中。目前 **SELinux** 是否启动有三种模式：

- **enforcing**：强制模式，表示 **SELinux** 运行中，且已经正确的开始限制 **domain/type** 了
- **permissive**：宽容模式，表示 **SELinux** 运行中，不过仅有警告进行并不会实际限制 **domain/type** 的存取。这种模式可以用来 **debug SELinux** 的配置
- **disabled：SELinux** 关闭中

三种模式的示意图如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/linux/selinux-three-pattern.png" style="zoom:50%;" />

注意：并非有所的进程都受 **SELinux** 的管控，注意是有 **受限的进程主体**，可以通过 `ps -eZ` 来观察该进程是否有受限（**confined**）。下面来观察 **crond** 与 **bash** 程序是否有被限制

```bash
[root@study ~]# ps -eZ | grep -E 'cron|bash'
system_u:system_r:crond_t:s0-s0:c0.c1023 1398 ? 00:00:00 atd
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2353 pts/0 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 2424 pts/0 00:00:00 bash
system_u:system_r:crond_t:s0-s0:c0.c1023 4073 ? 00:00:00 crond
```

因为目前 **target** 这个政策下，只有第 **3** 个字段 **type** 会有影响，因此可以看到 **crond** 有 `crond_t` 类型，是受限的，而 **bash** 是 `unconfined_t` 类型，是不受限的，也就是说 **bash** 不会经过上图的流程，而直接去判定 **rwx**

可以通过以下方式获取当前的 **SELinux** 模式

```bash
[root@study ~]# getenforce 
Enforcing
```

查询当前 **SELinux** 的政策（**Policy**）

```bash
sestatus [-vb]

选项与参数：
	-v：检查 /etc/sestatus.conf 内的文件与进程的安全性本文内容
	-b：将目前政策的规则布尔值列出，即某些规则 rule 是否要启动（0/1）
```

```bash
# 范例 1：列出目前 SELinux 使用的哪个政策 Policy

[root@study ~]# sestatus   		
SELinux status:                 enabled				# SELinux 是否启动
SELinuxfs mount:                /sys/fs/selinux		# SELinux 的相关文件数据挂载点
SELinux root directory:         /etc/selinux		# SELinux 的根目录所在
Loaded policy name:             targeted			# 当前的政策
Current mode:                   enforcing			# 当前模式
Mode from config file:          enforcing			# 目前配置文件内规范的 SELinux 模式
Policy MLS status:              enabled				# 是否含有 MLS 的模式机制
Policy deny_unknown status:     allowed				# 是否预设抵挡未知的主体进程
Max kernel policy version:      31

```

上述信息科知道，**SELinux** 目前的政策是 **targeted** ，可通过如下方式修改

```bash
[root@study ~]# vim /etc/selinux/config 
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing		 	# 可选择为上述 3 个
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted		# 可选值为上述 3 个
```

### SElinux 的启动与关闭

由于 **SElinux** 是整合到核心中去的，因此修改上述配置文件之后，需要重新启动。

注意：如果从 **disable** 转到启动 **SELinux** 的模式时，由于系统必须要针对文件写入安全性本文信息，因此开机过程需要耗费不少时间等待重新写入 **SELinux** 安全性本文（有时也称为**SELinux Label**），而且在写完之后还需要重新启动一次，启动成功之后，再使用 `getenforce 和 sestatus` 来观察是否有成功启动到 **Enforcing** 模式

如果当前已经是 **Enforcing** 模式，可能由于一些设置问题大道至 **SELinux** 让某些服务无法正常的运行，此时可将模式修改为宽容模式（**permissive**），让 **SELinux** 只发出警告信息

```bash
setenforce [0|1]

选项与参数：
	0：转成 permissive 宽容模式
	1：转成 Enforcing 强制模式
注意：无法在 Disabled 模式下进程模式的切换	
```

某些时候从 **Disabled** 换成 **Enforcing** 之后，有部分服务可能无法顺利启动，可能会报错 `/lib/xxx` 数据没有权限读取的错误信息。这大多数是由于重新写入 **Selinux type**（**Relabel**）出错的原因，使用 **Permissive** 模式就没有该错误。最简单的办法是在 **Permissive** 模式下使用指令 `restorecon -Rv /` 重新还原所有 **SELinux** 的类型。

### SELinux 政策内的规则管理

#### SELinux 各个规则的布尔值查询：getsebool

```bash
getsebool [-a] [规则名称]

选项与参数：
	-a：列出目前系统上所有 SELinux 规则的布尔值为开启或关闭（on/off）
```

```bash
# 范例 1：查询所有的布尔值设置
[root@study ~]# getsebool -a
abrt_anon_write --> off
abrt_handle_event --> off
abrt_upload_watch_anon_write --> on
...
cron_can_relabel --> off		# 这个与 cron 有关
cron_system_cronjob_use_shares --> off
cron_userdomain_transition --> on
...
httpd_anon_write --> off		# 与网页 http 有关
httpd_builtin_scripting --> on
httpd_can_check_spam --> off
# 每一行都是一个规则

```

#### SELinux 各个规则规范的主体进程能够读取的文件 SELinux type 查询 seinfo、sesearch

上述指令知道了所有的规则开启情况，可以通过 **seinfo**、**sesearch** 等工具来查看每个规则具体在限制什么。

上述工具并未预装，[请拿出安装光盘挂载到 /mnt 目录下](https://zq99299.github.io/linux-tutorial/tutorial-basis/07/03.html#挂载-cd-或-dvd-光盘)，安装

```bash
[root@study ~]# blkid 
/dev/sr0: UUID="2019-09-11-18-50-31-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos" 
/dev/sda1: UUID="e9d54afb-2afe-42de-87fe-9f55d747fcd9" TYPE="xfs" 
/dev/sda2: UUID="CNUXwS-J3Lh-0nDA-TssW-l1vT-90us-MHYnT1" TYPE="LVM2_member" 
/dev/mapper/centos_study-root: UUID="d7e09bb4-2f04-4ed4-b377-91a22fe85ce7" TYPE="xfs" 
/dev/mapper/centos_study-swap: UUID="684eebc0-3f70-4fc1-9a5d-d683f6a07cd0" TYPE="swap" 
[root@study ~]# mount /dev/sr0 /mnt/
mount: /dev/sr0 is write-protected, mounting read-only
[root@study ~]# yum install /mnt/Packages/setools-console-*                               
...
Complete!
[root@study ~]# umount /mnt/  # 卸载光盘
```

```bash
seinfo [-Atrub]

选项与参数：
	-A：列出 SELinux 的状态、规则布尔值、身份识别、角色、类型等所有信息
	-u：列出 SELinux 的所有身份识别 user 种类
	-r：列出 SELinux 的所有角色 role 种类
	-t：列出 SELinux 的所有类型 type 种类
	-b：列出所有规则的种类（布尔值）
```

```bash
# 范例 1：列出 SELinux 在此政策下的统计状态
[root@study ~]# seinfo 

Statistics for policy file: /sys/fs/selinux/policy
Policy Version & Type: v.31 (binary, mls)

   Classes:           130    Permissions:       272
   Sensitivities:       1    Categories:       1024
   Types:            4792    Attributes:        253
   Users:               8    Roles:              14
   Booleans:          316    Cond. Expr.:       362
   Allow:          107360    Neverallow:          0
   Auditallow:        157    Dontaudit:       10020
   Type_trans:      18129    Type_change:        74
   Type_member:        35    Role allow:         39
   Role_trans:        416    Range_trans:      5899
   Constraints:       143    Validatetrans:       0
   Initial SIDs:       27    Fs_use:             32
   Genfscon:          103    Portcon:           614
   Netifcon:            0    Nodecon:             0
   Permissives:         0    Polcap:              5
   
# 当前政策是 targeted ？ （哪里显示的？），此政策下的 Types 类型有 4792 个
# SELinux 的规则（Booleans）有 316 条

```

在前面讲到过几个身份识别 **user** 与 角色 **role**，**seinfo** 可以查询到所有的种类，可自行查询

在前面讲到 `/etc/cron.d/checktime` 的 **SElinux type** 类型不太对，我们知道 **crond** 进程的 **type** 是 `crond_t`，那么查找下 `crond_t` 能够读取的文件 **SELinux type** 有哪些

```bash
sesearch [-A] [-s 主体类别] [-t 目标类别] [-b 布尔值]

选项与参数：
	-A：列出后面数据中，允许「读取或放行」的相关数据
	-t：后面还要接 type、例如 -t httpd_t
	-b：后面接 SELinux 的规则，例如 -b httpd_enable_ftp_server
```

```bash
# 范例 1：找出 crond_t 主体进程能够读取的文件 SELinux type

[root@study ~]# sesearch -A -s crond_t | grep spool
   allow crond_t var_spool_t : dir { ioctl read getattr lock search open } ; 
   allow crond_t system_cron_spool_t : dir { ioctl read getattr lock search open } ; 
   allow crond_t user_cron_spool_t : lnk_file { read getattr } ; 
   allow crond_t user_cron_spool_t : file { ioctl read write create getattr setattr lock append unlink link rename open } ; 
   allow crond_t system_cron_spool_t : file { ioctl read write create getattr setattr lock append unlink link rename open } ; 
   allow crond_t var_spool_t : file { ioctl read getattr lock open } ; 
   allow crond_t cron_spool_t : file { ioctl read write create getattr setattr lock append unlink link rename open } ; 
   allow daemon user_cron_spool_t : file { ioctl read write getattr lock append } ; 
   allow crond_t cron_spool_t : dir { ioctl read write getattr lock add_name remove_name search open } ; 
   allow crond_t user_cron_spool_t : dir { ioctl read write getattr lock add_name remove_name search open } ; 
   allow crond_t user_cron_spool_t : file { ioctl read write create getattr setattr lock append unlink link rename open } ; 
   allow crond_t system_cron_spool_t : file { ioctl read write create getattr setattr lock append unlink link rename open } ;
   
# allow 后面是主体进程以及文件的 SELinux type，上面数据是截取出来的
# crond_t 可以读取 system_cron_spool_t 的文件/目录类型等

# 范例 2：找出 crond_t 是否能读取 /etc/cron.d/checktime 这个我们自定义的配置文件？
[root@study ~]# ll -Z /etc/cron.d/checktime 
-rw-r--r--. root root unconfined_u:object_r:admin_home_t:s0 /etc/cron.d/checktime
# 两个重点：SELinux type 为 admin_home_t，一个是文件（file）

[root@study ~]# sesearch -A -s crond_t | grep admin_home_t
   allow domain admin_home_t : dir { getattr search open } ; 
   allow crond_t admin_home_t : dir { ioctl read getattr lock search open } ; 
   allow userdom_filetrans_type admin_home_t : lnk_file { read getattr } ; 
   allow userdom_filetrans_type admin_home_t : dir { ioctl read write getattr lock add_name remove_name search open } ; 
   allow domain admin_home_t : lnk_file { read getattr } ; 
   allow crond_t admin_home_t : lnk_file { read getattr } ;
   
# 发现有 crond_t admin_home_t 存在，不过这个是总体的信息
# 没有针对某些规则的查询，所以不能确定 checktime 能否被读取，但是基本上就是 SELinux type 出现问题，才无法读取的

```

现在知道了 `/etc/cron.d/checktime` 是 **SELinux type** 错误导致无法读取的。看来在 `getsebool -a` 中看到的 `httpd_enable_homedirs` 是什么？又是规范了哪些主体进程能够读取的 **SELinux type**

```bash
[root@study ~]# semanage boolean -l | grep httpd_enable_homedirs
httpd_enable_homedirs          (off  ,  off)  Allow httpd to enable homedirs
# httpd_enable_homedirs 的功能是允许 httpd 进程读取用户家目录

# 范例 3：列出该规则中，主体进程能够读取的文件  SELinux type
[root@study ~]# sesearch -A -b httpd_enable_homedirs
Found 77 semantic av rules:
   allow httpd_t user_home_type : lnk_file { read getattr } ; 
   allow httpd_suexec_t user_home_type : lnk_file { read getattr } ; 
   allow httpd_suexec_t user_home_dir_t : lnk_file { read getattr } ; 
   allow httpd_t nfs_t : lnk_file { read getattr } ; 
   allow httpd_sys_script_t nfs_t : file { ioctl read getattr lock open } ; 
   allow httpd_sys_script_t cifs_t : lnk_file { read getattr } ; 
   allow httpd_user_script_t user_home_type : lnk_file { read getattr } ; 
   allow httpd_user_script_t user_home_type : dir { getattr search open } ; 
   allow httpd_t cifs_t : file { ioctl read getattr lock open } ; 
   allow httpd_sys_script_t nfs_t : dir { getattr search open } ; 
   allow httpd_sys_script_t nfs_t : dir { ioctl read getattr lock search open } ; 
   allow httpd_sys_script_t nfs_t : dir { getattr search open } ; 
   allow httpd_sys_script_t nfs_t : dir { ioctl read getattr lock search open } ; 
   allow httpd_t user_home_dir_t : dir { getattr search open } ; 
   allow httpd_sys_script_t cifs_t : file { ioctl read getattr lock open } ; 
   allow httpd_sys_script_t user_home_dir_t : dir { getattr search open } ; 
   allow httpd_sys_script_t user_home_dir_t : lnk_file { read getattr } ;
   xxx
 # 从上面的数据才可以理解，主要是放行 httpd_t 能否读取用户家目录的文件 （笔者这里是懵逼的没有看出来）
 # 所以，如果该规则没有启动，基本上 httpd_t 这种进程就无法读取用户家目录下的文件

```

#### 修改 SELinux 规则的布尔值 setsebool

查询到某个 **SELinux rule** ，并且以 **seaserch** 知道该规则的用途后，可以通过下面的方式来管理

```bash
setsebool [-p] [规则名称][0|1]

-P：直接将设置值写入配置文件，该设置数据未来会生效
```

```bash
# 范例 1：查询 httpd_enable_homedirs 这个规则的状态，并且修改这个规则为不同的布尔值
[root@study ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> off			# 关闭状态
[root@study ~]# setsebool -P httpd_enable_homedirs 1		# 开启它
[root@study ~]# getsebool httpd_enable_homedirs
httpd_enable_homedirs --> on


```

### SELinux 安全本文的修改

**SELinux** 对受限的主体进程没有影响：

1. 考虑 **SELinux** 的三种类型
2. 考虑 **SELinux**的政策规则是否放行
3. 比对 **SELinux type** 关系

上面讲解过可以通过 **sesearch** 来找到主体进程与文件的 **SELinux type** 关系，那么怎么修改文件的 **SELinux type**，能让主体进程读到呢？

#### **使用 chcon 手动修改文件的 SELinux type**

```bash
chcon [-R] [-t type] [-u user] [-r role] 文件
chcon [-R] --reference=范例文件 文件

选项与参数：
	-R：连同该目录下的次目录也同时修改
	-t：后面接安全性本文的类型字段，例如 httpd_sys_content_t
	-u：后面接身份识别，例如 system_u (不重要)
	-r：后面接角色，例如 system_r （不重要）
	-v：若有变化成功，将变动的结果列出来
	--reference=文件：拿某个文件档范例来修改后续接的文件的类型
```

```bash
# 范例 1：查询 /etc/hosts 的 SELinux type，并将该类型套用到 /etc/cron.d/checktime 上
[root@study ~]# ll -Z /etc/hosts
-rw-r--r--. root root system_u:object_r:net_conf_t:s0  /etc/hosts
# net_conf_t 是上面文件中的类型
[root@study ~]# chcon -v -t net_conf_t /etc/cron.d/checktime 
changing security context of '/etc/cron.d/checktime'
[root@study ~]# ll -Z /etc/cron.d/checktime 
-rw-r--r--. root root unconfined_u:object_r:net_conf_t:s0 /etc/cron.d/checktime

# 范例 2：直接以 /etc/shadow 的 type 套用
[root@study ~]# chcon -v --reference=/etc/shadow /etc/cron.d/checktime
changing security context of '/etc/cron.d/checktime'
[root@study ~]# ll -Z /etc/shadow /etc/cron.d/checktime 
-rw-r--r--. root root system_u:object_r:shadow_t:s0    /etc/cron.d/checktime
----------. root root system_u:object_r:shadow_t:s0    /etc/shadow
```

上面的示例并不能解决 **crond** 不能读取 `/etc/cron.d/checktime` 的问题，因为需要改成 `/etc/cron.d` 下的标准 **type** 才行。可以使用 **restorecon** 来让 **SELinux** 自己默认解决目录下的 **type** 问题

#### 使用 restorecon 让文件恢复正确的 SELinux type

```bash
restorecon [-Rv] 文件或目录

选项与参数：
	-R：连同次目录一起修改
	-v：将过程显示到屏幕上
```

```bash
# 范例 3：将 /etc/cron.d/ 下的文件都恢复成预设的 SELinux type
[root@study ~]# restorecon -Rv /etc/cron.d/
restorecon reset /etc/cron.d/checktime context system_u:object_r:shadow_t:s0->system_u:object_r:system_cron_spool_t:s0

# 上面将  shadow_t 改成了 system_cron_spool_t 类型

# 范例 4：重新启动 crond 看看有没有正确启动 checktime
[root@study ~]# systemctl restart crond          
[root@study ~]# tail /var/log/cron
Mar 17 16:01:01 study CROND[5886]: (root) CMD (run-parts /etc/cron.hourly)
Mar 17 16:01:01 study run-parts(/etc/cron.hourly)[5886]: starting 0anacron
Mar 17 16:01:01 study run-parts(/etc/cron.hourly)[5898]: finished 0anacron
Mar 17 16:01:01 study run-parts(/etc/cron.hourly)[5886]: starting mcelog.cron
Mar 17 16:01:01 study run-parts(/etc/cron.hourly)[5904]: finished mcelog.cron
Mar 17 16:10:01 study CROND[5989]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Mar 17 16:12:48 study crond[4073]: (CRON) INFO (Shutting down)
Mar 17 16:12:48 study crond[6068]: (CRON) INFO (RANDOM_DELAY will be scaled with factor 62% if used.)
Mar 17 16:12:49 study crond[6068]: (CRON) INFO (running with inotify support)
Mar 17 16:12:49 study crond[6068]: (CRON) INFO (@reboot jobs will be run at computer's startup.)
# 没有报错信息

```

从这里看来 **restorecon** 很方便，**chcon** 还是比较麻烦的

#### semanage 默认目录的安全性本文查询与修改

为什么 **restorecon** 可以恢复原本的 **SELinux type** 呢？那一定是有个地方在记录每个文件/目录的 **SELinux** 默认类型

1. 如何查询预设的 **SELinux type**
2. 如何增加、修改、删除 预设的 **SELinux type**

```bash
semanage {login|user|port|interface|fcontext|translation} -l
semanage fcontext -{a|d|m} [-frst] file_spec

选项与参数：
	fcontext：主要用在安全性本文方面的用途， -l 为查询
	-a：增加；可以增加一些目录的默认安全性本文类型设置
	-m：修改
	-d：删除
```

```bash
# 范例 1：查询 /etc/   /etc/cron.d/ 的预设 SELinux type
[root@study ~]# semanage fcontext -l | grep -E '^/etc |^/etc/cron'
/etc/cron.daily(/.*)?                              all files          system_u:object_r:bin_t:s0 
/etc/cron.weekly(/.*)?                             all files          system_u:object_r:bin_t:s0 
/etc/cron.hourly(/.*)?                             all files          system_u:object_r:bin_t:s0 
/etc/cron.monthly(/.*)?                            all files          system_u:object_r:bin_t:s0 
/etc/cron.minutely/openshift-facts                 regular file       system_u:object_r:openshift_cron_exec_t:s0 
/etc/cron\.(daily|monthly)/acct                    regular file       system_u:object_r:acct_exec_t:s0 
/etc/cron\.(daily|weekly)/sysklogd                 regular file       system_u:object_r:logrotate_exec_t:s0 
/etc/cron\.(daily|monthly)/mailman                 regular file       system_u:object_r:mailman_queue_exec_t:s0 
/etc/cron\.(daily|weekly)/man-db.*                 regular file       system_u:object_r:mandb_exec_t:s0 
/etc/cron\.(daily|monthly)/radiusd                 regular file       system_u:object_r:radiusd_exec_t:s0 
/etc/cron\.(daily|weekly)/ntp-simple               regular file       system_u:object_r:ntpd_exec_t:s0 
/etc/cron\.(daily|weekly)/ntp-server               regular file       system_u:object_r:ntpd_exec_t:s0 
/etc/cron\.((daily)|(weekly)|(monthly))/freeradius regular file       system_u:object_r:radiusd_exec_t:s0 
/etc/cron\.d(/.*)?                                 all files          system_u:object_r:system_cron_spool_t:s0 
/etc/cron\.daily/[sm]locate                        regular file       system_u:object_r:locate_exec_t:s0 
/etc/cron\.weekly/(c)?fingerd                      regular file       system_u:object_r:fingerd_exec_t:s0 
/etc                                               all files          system_u:object_r:etc_t:s0 
/etc/crontab                                       regular file       system_u:object_r:system_cron_spool_t:s0 
/etc/cron\.daily/prelink                           regular file       system_u:object_r:prelink_cron_system_exec_t:s0 
/etc/cron\.daily/calamaris                         regular file       system_u:object_r:calamaris_exec_t:s0 
/etc/cron\.daily/certwatch                         regular file       system_u:object_r:certwatch_exec_t:s0 
/etc/cron\.monthly/proftpd                         regular file       system_u:object_r:ftpd_exec_t:s0


```

看 `/etc/cron\.d(/.*)? all files system_u:object_r:system_cron_spool_t:s0` 这一行，这也是为什么直接使用 vim 在 `/etc/cron.d` 下新建文件时，预设 SELinux type 是正确的。

练习：下面要建立一个 `/srv/mycron` 目录，默认也是需要变成 `system_cron_spool_t` 时

```bash
# 1. 先建立 mycron 目录，再放入配置文件，观察 SELinux type
[root@study ~]# mkdir /srv/mycron
[root@study ~]# cp /etc/cron.d/checktime /srv/mycron/
[root@study ~]# ll -dZ /srv/mycron/ /srv/mycron/checktime 
drwxr-xr-x. root root unconfined_u:object_r:var_t:s0   /srv/mycron/
-rw-r--r--. root root unconfined_u:object_r:var_t:s0   /srv/mycron/checktime
# 发现变成了 var_t

# 2. 观察上层 /srv 的 SELinux type
[root@study ~]# semanage fcontext -l | grep '^/srv'
/srv/.*                                            all files          system_u:object_r:var_t:s0 
/srv/([^/]*/)?www(/.*)?                            all files          system_u:object_r:httpd_sys_content_t:s0 
/srv/([^/]*/)?ftp(/.*)?                            all files          system_u:object_r:public_content_t:s0 
/srv/([^/]*/)?rsync(/.*)?                          all files          system_u:object_r:public_content_t:s0 
/srv/([^/]*/)?www/logs(/.*)?                       all files          system_u:object_r:httpd_log_t:s0 
/srv/node(/.*)?                                    all files          system_u:object_r:swift_data_t:s0 
/srv/gallery2(/.*)?                                all files          system_u:object_r:httpd_sys_content_t:s0 
/srv/lib/gitosis(/.*)?                             all files          system_u:object_r:gitosis_var_lib_t:s0 
/srv/gallery2/smarty(/.*)?                         all files          system_u:object_r:httpd_sys_rw_content_t:s0 
/srv/loopback-device(/.*)?                         all files          system_u:object_r:swift_data_t:s0 
/srv                                               all files          system_u:object_r:var_t:s0
# 可以看到这里默认就是  var_t 类型的

# 3. 将 mycron 默认值改为 system_cron_spool_t
[root@study ~]# semanage fcontext -a -t system_cron_spool_t "/srv/mycron(/.*)?"
[root@study ~]# semanage fcontext -l | grep '^/srv/mycron'
/srv/mycron(/.*)?                                  all files          system_u:object_r:system_cron_spool_t:s0 

# 4. 回复 /srv/mycron 以及子目录相关的 SELinux type
[root@study ~]# restorecon -Rv /srv/mycron/
restorecon reset /srv/mycron context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:system_cron_spool_t:s0
restorecon reset /srv/mycron/checktime context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:system_cron_spool_t:s0

 
```

通过这个例子来看，**restorecon** 的确是很方便 ，学会这些基础的工具，对于 **SELinux** 来说基本上也够用了

----

## 一个网络服务案例及登录文件协助

本章在 **SELinux** 小节中介绍到的各个指令，尤其是 **setsebool**、**chcon**、**restorecon** 等都是为了当你的某些网络服务无法正常提供相关功能时，才需要进行修改的一些指令动作。

可以通过主动检查的方式来检查是否有 **SELinux** 产生的错误。而不是等客户端联机失败来反馈

### setroubleshoot：错误信息写入 `/var/log/messages`

几乎所有 **SELinux** 相关的程序都是以 **se** 开头，该服务时错误克服，启动后，会将关于 **SELinux** 的错误信息与克服方法记录到 `/var/log/messages` 与 `/var/log/setroubleshoot/*` 中

需要安装：**setroubleshoot** 与 **setroubleshoot-server**。原本 **SELinux** 信息是两个服务来记录的，分别是 **auditd** 与 **setroubleshoot**。在 **CentOS 6.x** 起整合成 **auditd** 了。所以安装好 **setroubleshoot-server** 后，需要重新启动 **auditd** 服务，否则 **setroubleshoot** 功能不会被启动

实际上。CentOS 7.x 对 **setroubleshoot** 的运行方式是：先由 **auditd** 去呼叫 **audispd** 服务，然后 **audispd** 服务启动 **sedispatch** 程序， **sedispatch** 再将原本的 **auditd** 信息转成 **setroubleshoot** 的信息，存储下来

```bash
[root@study ~]# rpm -qa | grep setroubleshoot
setroubleshoot-3.2.30-7.el7.x86_64
setroubleshoot-plugins-3.0.67-4.el7.noarch
setroubleshoot-server-3.2.30-7.el7.x86_64
```

在预设的情况下 **setroubleshoot** 被安装了，记得刚安装 **setroubleshoot** 的话，需要重新启动 **auditd** 服务的、

目前我们没有任何受限的网络服务主体进程在运行，下面使用一个简单的 **FTP** 服务器软件示例，来了解上面讲到的许多重点应用

### 实例说明：通过 vsftpd 这个 FTP 服务器来存取系统上的文件

在 **CentOS 7.x** 环境下， **FTP** 的默认服务器软件主要是 **vsftpd**

详细的 FTP 协议在服务器篇讲解，这里简单利用 **vsftpd** 与 **FTP** 的协议来讲解 **SELinux** 的问题与错误克服。

下面只接受一些简单的 **FTP** 知识：客户端需要使用 **FTP** 账户登录 **FTP** 服务器，有一个称为「匿名 （**anonymous**）」的账户可以登录系统，但是这个匿名的账户登录后，只能存取一个特定的目录，而无法脱离该目录

在 **vsftpd** 中，一般用户与匿名者的家目录说明如下：

- 匿名者：如果使用浏览器来联机到 **FTP** 服务器，那预设就是使用匿名者登录系统。匿名者的家目录默认是在 `/var/ftp` 中，同时，匿名者在家目录下只能下载数据，不能上传数据到 **FTP** 服务器，同时匿名者无法离开 FTP 服务器的 `/var/ftp` 目录
- 一般 **FTP** 账户：在预设情况下，所有 **UID** 大于 **1000** 的账户，都可以使用 **FTP** 来登录系统，登录系统后，所有的账户都能够取得自己家目录下的文件数据，预设也可以上传、下载文件的

为了避免与之前章节的用户产生误解情况，创建一个名为 **ftptest** 的账户，且账户密码为 **myftp123**

```bash
[root@study ~]# useradd -s /sbin/nologin ftptest
[root@study ~]# echo "myftp123" | passwd --stdin ftptest
Changing password for user ftptest.
passwd: all authentication tokens updated successfully.
```

下面来安装 **vsftp** 服务器软件（还是在光盘中安装，前面挂载那样）

```bash
[root@study ~]# yum install /mnt/Packages/vsftpd-3*                    

[root@study ~]# systemctl start vsftpd		# 启动 vsftpd 服务
[root@study ~]# systemctl enable vsftpd		# 设置为开机启动
Created symlink from /etc/systemd/system/multi-user.target.wants/vsftpd.service to /usr/lib/systemd/system/vsftpd.service.
[root@study ~]# netstat -tlnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1374/cupsd          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1578/master         
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      2350/sshd: mrcode@p 
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd           
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1975/dnsmasq        
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1378/sshd           
tcp6       0      0 ::1:631                 :::*                    LISTEN      1374/cupsd          
tcp6       0      0 ::1:25                  :::*                    LISTEN      1578/master         
tcp6       0      0 ::1:6010                :::*                    LISTEN      2350/sshd: mrcode@p 
tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd           
tcp6       0      0 :::21                   :::*                    LISTEN      6656/vsftpd         
tcp6       0      0 :::22                   :::*                    LISTEN      1378/sshd

# 可以看到   6656/vsftpd 这行数据，代表已经启动了
```

### 匿名者无法下载的问题

模拟一些 **FTP** 的常用状态，假设将 `/etc/securetty` 以及主要的 `/etc/sysctl.conf` 放置给所有人下载，那么可以能会这样做

```bash
[root@study ~]# cp -a /etc/securetty /etc/sysctl.conf /var/ftp/pub
[root@study ~]# ll /var/ftp/pub/
total 8
-rw-------. 1 root root 221 Oct 31  2018 securetty
-rw-r--r--. 1 root root 449 Aug  9  2019 sysctl.conf
```

一般来说，默认要给用户下载的 **FTP** 文件会放在 `/var/ftp/pub` 目录中。下面使用简单的终端机浏览器 **curl** 来观察

```bash
# 1. 查看 FTP 根目录下有哪些内容
[root@study ~]# curl ftp://localhost
drwxr-xr-x    2 0        0              42 Mar 17 09:03 pub
# 确实看到了 pub 目录

# 2. 查看 pub 目录内的内容
[root@study ~]# curl ftp://localhost/pub
curl: (78) RETR response: 550
# 无法访问，是因为 pub 是一个目录需要后缀 / 结尾
[root@study ~]# curl ftp://localhost/pub/
-rw-------    1 0        0             221 Oct 30  2018 securetty
-rw-r--r--    1 0        0             449 Aug 08  2019 sysctl.conf

# 3. 查看里面的文件内容
[root@study ~]# curl ftp://localhost/pub/sysctl.conf
# sysctl settings are defined through files in
# /usr/lib/sysctl.d/, /run/sysctl.d/, and /etc/sysctl.d/.
#
# Vendors settings live in /usr/lib/sysctl.d/.
# To override a whole file, create a new file with the same in
# /etc/sysctl.d/ and put new settings there. To override
# only specific settings, add a file with a lexically later
# name in /etc/sysctl.d/ and put new settings there.
#
# For more information, see sysctl.conf(5) and sysctl.d(5).

# 上面不是错误信息，是哪个文件的内容

# 4. 继续查看下一个文件内容
[root@study ~]# curl ftp://localhost/pub/securetty  
curl: (78) RETR response: 550
# 这里看不到了，但是 securetty 的确是一个文件而不是一个目录，基本原因应该是权限问题
# 因为 vsftpd 默认放在 /var/ftp/pub 内的资料，无论什么 SELinux type 几乎都可以被读取才对

# 5. 修正权限后，再观察一次 securetty 文件
[root@study ~]# ll /var/ftp/pub/
total 8
-rw-------. 1 root root 221 Oct 31  2018 securetty
-rw-r--r--. 1 root root 449 Aug  9  2019 sysctl.conf
# 可以看到 securetty 的其他人权限没有。改变成其他人也可以读取
[root@study ~]# chmod a+r /var/ftp/pub/securetty 
[root@study ~]# curl ftp://localhost/pub/securetty
console
vc/1
vc/2
vc/3
# 此时已经能看到文件内容了

# 6. 修正 SELinux type 的内容（非必须）
[root@study ~]# restorecon -Rv /var/ftp/
restorecon reset /var/ftp/pub/securetty context system_u:object_r:etc_runtime_t:s0->system_u:object_r:public_content_t:s0
restorecon reset /var/ftp/pub/sysctl.conf context system_u:object_r:system_conf_t:s0->system_u:object_r:public_content_t:s0

 
```

上述列子告诉我们，要先从权限角度来检查，如果无法被读取 ，可能是因为没有 **r** 或则没有 **rx** 权限，并不一定是 **SELinux** 引起的。下面看看用一般账户登录

### 无法从家目录下载文件的问题分析与解决

由于通过一般账户，前面建立的 **ftptest** 账户登录的话，文字型的 FTP 客户端软件，默认会将用户引导在根目录，而不是家目录，因此，访问的 **URL** 需要更改一下

```bash
# 0. 在 ftptest 家目录下创建一些数据
[root@study ~]# echo  ~ftptest/
/home/ftptest/
[root@study ~]# echo "testing" >  ~ftptest/test.txt
[root@study ~]# cp -a /etc/hosts /etc/sysctl.conf ~ftptest/
[root@study ~]# ll ~ftptest/
total 12
-rw-r--r--. 1 root root 158 Jun  7  2013 hosts
-rw-r--r--. 1 root root 449 Aug  9  2019 sysctl.conf
-rw-r--r--. 1 root root   8 Mar 17 17:23 test.txt

# 1. 一般账户直接登录 FTP 服务器，同时变换目录到家目录
[root@study ~]# curl ftp://ftptest:myftp123@localhost/~/
curl: (67) Access denied: 530			# 这里报错了
# 注意：书上在增加 ftptest 用户的时候，使用的是 /sbin/nologin，就无法访问 ftp，这里修改下，就可以了
[root@study ~]# usermod -s  /bin/bash ftptest
[root@study ~]# curl ftp://ftptest:myftp123@localhost/~/
-rw-r--r--    1 0        0             158 Jun 07  2013 hosts
-rw-r--r--    1 0        0             449 Aug 08  2019 sysctl.conf
-rw-r--r--    1 0        0               8 Mar 17 09:23 test.txt
# 看左边的权限也是没有问题的
# 从这里开始，笔者的实验和书上的结果对不上了，下面只记录书上的操作指令
# 就是因为上面修改用户的 bash 后，虽然可以访问了，但是下面的却可以下载文件，无法达到和书上的效果一样

# 2. 下载上面可以阅读的权限文件
[root@study ~]# curl ftp://ftptest:myftp123@localhost/~/test.txt
curl:(78) RETR response:550
# 无法访下载，是否是 SELinux 造成的？

# 3. 将 SELinux 从 Enforce 转成 Permissive 
[root@study ~]# setenforce 0
[root@study ~]# curl ftp://ftptest:myftp123@localhost/~/test.txt
testing
[root@study ~]# setenforce 1	# 确定是 SELinux 权限问题后，改回来
# 需要该规则还是该 type？现在不知道
# 所以先查询下登录日志有没有相关的信息提供给我们处理

[root@study ~]# vim /var/log/messages
Aug 9 02:55:58 station3-39 setroubleshoot:SELinux is preventing /usr/sbin/vsftpd
	from lock access on the file /home/ftptest/test.txt. For complete SELinux messages.
	run sealert -l 3axxxxxxxx
# 之类的字样，关键词就是 sealert ，执行这条命令
[root@study ~]# sealert -l 3axxxxxxxx
SELinux is preventing /usr/sbin/vsftpd from lock access on the file /home/ftptest/test/txt.
# 下面说有 47.5% 的几率是由于这个原因所发生，并且可以使用 setsebool 去解决的意思
******* Plugin catchall_boolean(47.5 confidence) suggests ********

if you want to allow ftp to home dir
...
Do
setsebool -P ftp_home_dir 1

******* Plugin catchall(6.38confidence) suggests ********
DO
# grep vsftpd /var/log/audit/audit.log | audit2allow -M mypol
# semodule -i mypol.pp

# 下面就重要了，是整个问题发生的主要原因
Additional Information:
Source Context	system_u:system_r:ftpd_t:s0-s0:c0.c1023
Target Context	unconfined_u:object_r:user_home_t:s0
Target Objects	/home/ftptest/test/txt [ file ]
```

通过上面的测试，知道主要的问题发生在 **SElinux** 的 **type** 不是 **vsftpd_t** 所能读取的原因，上面 **47.5** 的概率问题，**ftp_home_dir** 是 **SELinux rules** 的配置

```bash
# 1. 确认下 SELinux 的模式，并且无法访问
[root@study ~]# getenforce 
Enforcing
[root@study ~]# curl ftp://ftptest:myftp123@localhost/~/test.txt
curl:(78) RETR response:550
[root@study ~]# setsebool -P ftp_home_dir 1
Boolean ftp_home_dir is not defined
# 可惜笔者这里提示没有被定义，与书上对不上啊
```

### 一般账户用户从非正规目录上传/下载文件

提供 `/srv/gogogo` 目录给 **ftptest** 用户使用，该如何处理？假设不考虑 **SELiunx** 的话，就是如下方式

```bash
# 1. 处理好所需要的目录数据
[root@study ~]# mkdir /srv/gogogo
[root@study ~]# chgrp ftptest /srv/gogogo/
# 把用户组改成 ftptest 这个组
[root@study ~]# ll -d /srv/gogogo/
drwxr-xr-x. 2 root ftptest 22 3月  17 22:43 /srv/gogogo/
[root@study ~]# echo "test" > /srv/gogogo/test.txt
[root@study ~]# curl ftp://ftptest:myftp123@localhost//srv/gogogo/test.txt
curl: (78) RETR response: 550
# 访问不了，查看日志
[root@study ~]# grep sealert /var/log/messages | tail
Mar 17 22:46:35 study setroubleshoot: SELinux is preventing /usr/sbin/vsftpd from read access on the file test.txt. For complete SELinux messages run: sealert -l 88f08c09-c510-4518-bbcc-58bcee06ffb0

[root@study ~]# sealert -l 88f08c09-c510-4518-bbcc-58bcee06ffb0
SELinux is preventing /usr/sbin/vsftpd from read access on the file test.txt.

# 虽然这个可信度很高，不过，因为会全部方向 FTP，所以不考虑
*****  Plugin catchall_boolean (57.6 confidence) suggests   ******************

If you want to allow ftpd to full access
Then you must tell SELinux about this by enabling the 'ftpd_full_access' boolean.

Do
setsebool -P ftpd_full_access 1

# 因为是非正规目录的使用，所以这边加上预设 SELinux type 恐怕能解决
*****  Plugin catchall_labels (36.2 confidence) suggests   *******************

If you want to allow vsftpd to have read access on the test.txt file
Then you need to change the label on test.txt
Do
# 下面这一条数据
# semanage fcontext -a -t FILE_TYPE 'test.txt'
.... 很多数据
Then execute:
restorecon -v 'test.txt'		# 还有这一条数据，都是要参考的解决方案

*****  Plugin catchall (7.64 confidence) suggests   **************************

If you believe that vsftpd should be allowed read access on the test.txt file by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# ausearch -c 'vsftpd' --raw | audit2allow -M my-vsftpd
# semodule -i my-vsftpd.pp


Additional Information:
Source Context                system_u:system_r:ftpd_t:s0-s0:c0.c1023
Target Context                unconfined_u:object_r:var_t:s0
Target Objects                test.txt [ file ]
Source                        vsftpd
Source Path                   /usr/sbin/vsftpd
Port                          <Unknown>
Host                          study.centos.mrcode
Source RPM Packages           
Target RPM Packages           
Policy RPM                    selinux-policy-3.13.1-252.el7.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     study.centos.mrcode
Platform                      Linux study.centos.mrcode 3.10.0-1062.el7.x86_64
                              #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64
Alert Count                   2
First Seen                    2020-03-17 22:46:17 CST
Last Seen                     2020-03-17 22:46:32 CST
Local ID                      88f08c09-c510-4518-bbcc-58bcee06ffb0

Raw Audit Messages
type=AVC msg=audit(1584456392.386:979): avc:  denied  { read } for  pid=10979 comm="vsftpd" name="test.txt" dev="dm-0" ino=35108539 scontext=system_u:system_r:ftpd_t:s0-s0:c0.c1023 tcontext=unconfined_u:object_r:var_t:s0 tclass=file permissive=0


Hash: vsftpd,ftpd_t,var_t,file,read

# 3. 查看 /var/ftp 的 SELinux type
[root@study ~]#  ll -Zd /var/ftp/
drwxr-xr-x. root root system_u:object_r:public_content_t:s0 /var/ftp/
[root@study ~]#  ll -Zd /srv/gogogo/
drwxr-xr-x. root ftptest unconfined_u:object_r:var_t:s0   /srv/gogogo/

# 4. 以 sealert 建议的方法来处理好 SELinux type
[root@study ~]# semanage fcontext -a -t public_content_t '/srv/gogogo(/.*)?'
[root@study ~]# restorecon -Rv /srv/gogogo
restorecon reset /srv/gogogo context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:public_content_t:s0
restorecon reset /srv/gogogo/test.txt context unconfined_u:object_r:var_t:s0->unconfined_u:object_r:public_content_t:s0
# 再次访问就可以了
[root@study ~]# curl ftp://ftptest:myftp123@localhost//srv/gogogo/test.txt
test
```

在这个范例中，修改的是 **type**，前一个范例中修改的是 **rule**，不太一样的

### 无法变更 FTP 联机端口问题分析解决

比如你想要改变 FTP 默认的启动端口 21 改成 555，基本上，既然 SELinux 的主体进程大多是被受限的网络服务，很有可能连端口也限制了，下面尝试修改端口，来查看是怎么解决问题的

```bash
# 1. 先处理 vsftpd 的配置文件，加入 port 的端口参数
[root@study ~]# vim /etc/vsftpd/vsftpd.conf 
listen_port=555

# 2. 重启服务，并查看日志
[root@study ~]# systemctl restart vsftpd
Job for vsftpd.service failed because the control process exited with error code. See "systemctl status vsftpd.service" and "journalctl -xe" for details.
[root@study ~]# grep sealert /var/log/messages
Mar 17 23:03:23 study setroubleshoot: SELinux is preventing /usr/sbin/vsftpd from name_bind access on the tcp_socket port 555. For complete SELinux messages run: sealert -l e3e3dee0-83eb-4cb8-b894-8be590fee082

[root@study ~]# sealert -l e3e3dee0-83eb-4cb8-b894-8be590fee082
SELinux is preventing /usr/sbin/vsftpd from name_bind access on the tcp_socket port 555.

# 这个 92.2 的概率，基本上就是这个了
*****  Plugin bind_ports (92.2 confidence) suggests   ************************

If you want to allow /usr/sbin/vsftpd to bind to network port 555
Then you need to modify the port type.
Do
# semanage port -a -t PORT_TYPE -p tcp 555
    where PORT_TYPE is one of the following: certmaster_port_t, cluster_port_t, ephemeral_port_t, ftp_data_port_t, ftp_port_t, hadoop_datanode_port_t, hplip_port_t, isns_port_t, port_t, postgrey_port_t, unreserved_port_t.

*****  Plugin catchall_boolean (7.83 confidence) suggests   ******************

If you want to allow nis to enabled
Then you must tell SELinux about this by enabling the 'nis_enabled' boolean.

Do
setsebool -P nis_enabled 1

*****  Plugin catchall (1.41 confidence) suggests   **************************

If you believe that vsftpd should be allowed name_bind access on the port 555 tcp_socket by default.
Then you should report this as a bug.
You can generate a local policy module to allow this access.
Do
allow this access for now by executing:
# ausearch -c 'vsftpd' --raw | audit2allow -M my-vsftpd
# semodule -i my-vsftpd.pp


Additional Information:
Source Context                system_u:system_r:ftpd_t:s0-s0:c0.c1023
Target Context                system_u:object_r:hi_reserved_port_t:s0
Target Objects                port 555 [ tcp_socket ]
Source                        vsftpd
Source Path                   /usr/sbin/vsftpd
Port                          555
Host                          study.centos.mrcode
Source RPM Packages           vsftpd-3.0.2-25.el7.x86_64
Target RPM Packages           
Policy RPM                    selinux-policy-3.13.1-252.el7.noarch
Selinux Enabled               True
Policy Type                   targeted
Enforcing Mode                Enforcing
Host Name                     study.centos.mrcode
Platform                      Linux study.centos.mrcode 3.10.0-1062.el7.x86_64
                              #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64
Alert Count                   1
First Seen                    2020-03-17 23:03:20 CST
Last Seen                     2020-03-17 23:03:20 CST
Local ID                      e3e3dee0-83eb-4cb8-b894-8be590fee082

Raw Audit Messages
type=AVC msg=audit(1584457400.225:1008): avc:  denied  { name_bind } for  pid=11443 comm="vsftpd" src=555 scontext=system_u:system_r:ftpd_t:s0-s0:c0.c1023 tcontext=system_u:object_r:hi_reserved_port_t:s0 tclass=tcp_socket permissive=0


type=SYSCALL msg=audit(1584457400.225:1008): arch=x86_64 syscall=bind success=no exit=EACCES a0=4 a1=55e9e4d4e800 a2=1c a3=3 items=0 ppid=11440 pid=11443 auid=4294967295 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=(none) ses=4294967295 comm=vsftpd exe=/usr/sbin/vsftpd subj=system_u:system_r:ftpd_t:s0-s0:c0.c1023 key=(null)

Hash: vsftpd,ftpd_t,hi_reserved_port_t,tcp_socket,name_bind

# 3. 根据建议解决执行指令, 92% 哪个指令下面 PORT_TYPE 下面又可选的 ftp_port_t
# 但是笔者还是懵逼的，不知道为什么那么多里面就选这个了
[root@study ~]# semanage port -a -t ftp_port_t -p tcp 555
[root@study ~]# systemctl restart vsftpd
[root@study ~]# netstat -tlnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1374/cupsd          
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1578/master         
tcp        0      0 127.0.0.1:6010          0.0.0.0:*               LISTEN      2350/sshd: mrcode@p 
tcp        0      0 127.0.0.1:6011          0.0.0.0:*               LISTEN      10579/sshd: root@pt 
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd           
tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1975/dnsmasq        
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1378/sshd           
tcp6       0      0 ::1:631                 :::*                    LISTEN      1374/cupsd          
tcp6       0      0 ::1:25                  :::*                    LISTEN      1578/master         
tcp6       0      0 ::1:6010                :::*                    LISTEN      2350/sshd: mrcode@p 
tcp6       0      0 ::1:6011                :::*                    LISTEN      10579/sshd: root@pt 
tcp6       0      0 :::555                  :::*                    LISTEN      11573/vsftpd        
tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd           
tcp6       0      0 :::22                   :::*                    LISTEN      1378/sshd     
# 可以看到 vsftpd 的端口变成了 555 了

# 4. 实验看看该 port 是否可用
[root@study ~]# curl ftp://localhost:555
drwxr-xr-x    2 0        0              42 Mar 17 09:03 pub
```
