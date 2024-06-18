---
title: git详细教程
date: 2024-01-18 15:10:19
tags:
- git
- 版本控制
categories: 版本控制
---

# 概述

`Git` 是一个免费的开源版本控制系统，最初由 `Linus Torvalds` 于 2005 年创建。与 SVN 和 CVS 等旧的集中式版本控制系统不同，`Git` 是分布式的：每个开发人员都在本地拥有其代码存储库的完整历史记录。这使得存储库的初始克隆速度变慢，但后续操作（例如提交、责备、比较、合并和日志）速度显着加快。

`Git` 还对分支、合并和重写存储库历史提供出色的支持，这导致了许多创新且强大的工作流程和工具。Pull 请求是一种流行的工具，它允许团队在 `Git` 分支上进行协作并有效地审查彼此的代码。`Git` 是当今世界上使用最广泛的版本控制系统，被认为是软件开发的现代标准。

# 工作流

你的本地仓库由 git 维护的三棵“树”组成。第一个是你的 `工作目录`，它持有实际文件；第二个是 `暂存区（Index）`，它像个缓存区域，临时保存你的改动；最后是 `HEAD`，它指向你最后一次提交的结果。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/Git%20Diagram.svg" style="zoom:50%;" />

**Git 目录**（位于 `YOUR-PROJECT-PATH/.git/` 中）是 Git 存储准确跟踪项目所需的所有内容的位置。这些内容包括元数据和一个对象数据库，其中包含项目文件的压缩版本。

**工作目录**是用户在本地对项目进行更改的地方。工作目录从 `Git` 目录的对象数据库中提取项目的文件，并将其放置在用户的本地计算机上。

**暂存区**是一个文件，用于存储下一次 `commit` 内容的信息。“commit” 的意思是你告诉 Git 保存暂存区的更改。 Git 照原样拍摄文件快照，并将该快照永久存储在 Git 目录中。

在三个部分中，文件可以在任何给定时间处于三种主要状态：`提交`，`修改`或`暂存`。在工作目录中对文件进行**修改**，然后，将其移至暂存区进行**暂存**，最后，`commit` 提交文件。

**以下是 Git 工作的基本概述：**

1. 使用 git 托管工具（如 `Bitbucket`）创建一个“存储库”（项目）
2. 将远程存储库复制（或克隆）到本地计算机
3. 将文件`add`到本地存储库并`commit`（保存）更改
4. 将您的更改`push`到您的主分支
5. 使用 git 托管工具更改文件并提交
6. 将更改`pull`到本地计算机
7. 创建“分支”（版本），进行更改，提交更改
8. 打开“拉取请求”（建议对主分支进行更改）
9. 将您的分支“合并”到主分支

----------

# 设置存储库

## git init

`git init` 命令会创建一个新的 Git 仓库。它可以用来将一个现有的、未版本控制的项目转换为 Git 仓库，也可以用来初始化一个新的、空的仓库。大多数其他 Git 命令在初始化仓库之前都不可用，所以这通常是在新项目中运行的第一个命令。

执行 `git init` 会在当前工作目录下创建一个 `.git` 子目录，其中包含新仓库所需的所有 Git 元数据。这些元数据包括对象、引用和模板文件的子目录。此外，还会创建一个 `HEAD` 文件，它是一个指向当前已签出分支或提交的指针，其中包含特定时间内整个代码库的不可更改快照。无论 HEAD 直接（使用哈希值）或通过引用（使用分支）引用哪个提交，它始终都是本地更改所依据的提交。

除了项目根目录中的 `.git` 目录外，现有项目不会被改动（与 SVN 不同，Git 并不要求每个子目录中都有 .git 子目录）。

默认情况下，`git init` 会将 Git 配置初始化为 .`git` 子目录路径。如果你想把子目录放在其他地方，可以修改或自定义子目录路径。你可以将 `$GIT_DIR` 环境变量设置为自定义路径，这样 `git init` 就会在那里初始化 Git 配置文件。此外，你还可以通过`--separate-git-dir`参数来达到同样的效果。

与 SVN 相比，该`git init`命令是创建新版本控制项目的极其简单的方法。Git 不需要您创建存储库、导入文件和签出工作副本。此外，Git 不需要任何预先存在的服务器或管理员权限。您所要做的就是 cd 进入您的项目子目录并运行`git init`，您将拥有一个功能齐全的 Git 存储库。

```bash
git init
```

将当前目录转换为 Git 存储库。这会向当前目录创建一个`.git`子目录，并可以开始记录项目的修订。

```bash
git init <directory>
```

在指定目录下创建一个空的 Git 仓库。运行此命令将创建一个名为 `＜directory＞` 的新子目录，其中只包含 `.git` 子目录。

如果已经在某个项目目录下运行过 `git init`，且其中包含 `.git` 子目录，那么可以放心地在同一项目目录下再次运行 git init。它不会覆盖现有的 `.git` 配置。

## git clone

### 目的：repo-to-repo协作开发副本

如果一个项目已经在中央仓库已建立，`git clone` 命令是用户获取开发副本的最常用方法。与 `git init` 一样，`git clone` 通常也是一次性操作。开发人员获得工作副本后，所有版本控制操作和协作都将通过本地仓库进行管理。

要知道，Git 的 "工作副本 "与从 SVN 仓库中签出代码所得到的工作副本是完全不同的。与 SVN 不同，Git 不区分工作副本和中央仓库，它们都是完整的 Git 仓库。

这就使得使用 Git 与使用 SVN 进行协作有了本质区别。SVN 依赖于中心仓库和工作副本之间的关系，而 Git 的协作模式则基于仓库与仓库之间的交互。你不需要把工作副本检查到 SVN 的中心仓库，而是从一个仓库推送或拉取提交到另一个仓库。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/central-repo.png" style="zoom:50%;" />

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/repo-to-repo.png" style="zoom:50%;" />

`git clone` 主要用于指向现有存储库，并在另一个位置的新目录中克隆或复制该存储库。 原始存储库可以位于本地文件系统或远程计算机可访问的支持协议上。 `git clone` 命令复制现有的 Git 存储库。 这有点像 SVN `checkout`，只不过“工作副本”是一个成熟的 Git 存储库——它有自己的历史记录，管理自己的文件，并且是与原始存储库完全隔离的环境。

为了方便起见，克隆会自动创建一个名为`“origin”`的远程连接，指向原始存储库。 这使得与中央存储库交互变得非常容易。 这种自动连接是通过配置在`refs/remotes/origin`下的`remote.origin.url`和`remote.origin.fetch` 变量来建立对远程分支头的 Git 引用。

下面的示例演示了如何获取存储在可使用 SSH 用户名 john 访问的服务器上的中央存储库的本地副本：`git clone` `example.com`

```bash
git clone ssh://john@example.com/path/to/my-project.git 
cd my-project 
# Start working on the project
```

第一条命令是在本地计算机的 my-project 文件夹中初始化一个新的 Git 仓库，并将中央仓库的内容填充到其中。您可以 cd 进入项目并开始编辑文件、提交快照以及与其他存储库交互。还要注意的是，克隆仓库中省略了 `.git` 扩展名。这反映了本地副本的非裸状态。

### clone到特定文件夹

```bash
git clone <repo> <directory>
```

将位于 `＜repo＞` 的版本库克隆到本地计算机上名为 `＜directory＞` 的文件夹中。

### clone特定标签

```bash
git clone --branch <tag> <repo>
```

克隆位于 `＜repo＞` 的资源库，并只克隆 `＜tag＞` 的 `ref`。

### clone特定分支

```bash
git clone --branch
```

通过 `-branch` 参数，你可以指定要克隆的特定分支，而不是远程 `HEAD` 指向的分支(通常是主分支)。此外，你还可以传递一个 `tag` 来代替分支，以达到同样的效果。

### Git URL 协议

#### -SSH

`Secure Shell` (SSH) 是一种普遍存在的经过身份验证的网络协议，大多数服务器上通常默认配置该协议。由于 SSH 是一种经过身份验证的协议，因此您需要在连接之前与托管服务器建立凭据。

#### - GIT

`git` 独有的协议。Git 自带一个守护进程，运行端口为 (9418)。该协议与`SSH`类似，但没有身份验证。

#### - HTTP

超文本传输协议。Web 协议，最常用于通过 Internet 传输网页 HTML 数据。Git 可以配置为通过`HTTP`进行通信

------

## git config 

`git config` 命令是一个方便的功能，用于在全局或本地项目级别设置 Git 配置值。这些配置级别与 `.gitconfig` 文本文件相对应。执行 `git config` 会修改配置文本文件。

我们将讨论常见的配置设置，如电子邮件、用户名和编辑器。我们还将讨论 Git 别名，它允许你为常用的 Git 操作创建快捷方式。熟悉`git config`和各种 Git 配置设置，将有助于你创建强大的、个性化的 Git 工作流程。

`git config` 最基本的用例是用一个配置名来调用它，从而显示该配置名下的设置值。配置名称是以点分隔的字符串。例如：`user.email`

```bash
git config user.email
```

查看配置文件中设置的所有变量，以及它们的值。

```bash
git config --list
```

### git 配置级别和文件

在进一步讨论`git config`的用法之前，我们先来了解一下配置级别。`git config`命令可以接受参数来指定操作的配置级别。以下是可用的配置级别：

- `--local`

  默认情况下，如果没有通过配置选项，`git config`会写入本地级别。本地配置会应用到调用 git config 的上下文仓库。本地配置值保存在一个文件中，可以在 repo 的 .git 目录中找到：`.git/config`

-  `--global`

  全局配置是针对特定用户的，这意味着它适用于操作系统用户。全局配置值存储在用户主目录下的一个文件中。在 unix 系统中为 `~ /.gitconfig`，在 windows 系统中为 `C:\Users\\.gitconfig`

- `--system`

  系统级配置适用于整个机器。这包括操作系统上的所有用户和所有版本库。系统级配置文件位于系统根目录下的 `gitconfig` 文件中。在 unix 系统中为 `$(prefix)/etc/gitconfig`。在 Windows XP 上，该文件位于 `C:\Documents and Settings\All Users\Application Data\Git\config` 下；在 Windows Vista 及更新版本上，该文件位于 `C:\ProgramData\Git\config` 下。

因此，配置级别的优先顺序是：`local`、`global`、`system`。这意味着在查找配置值时，Git 会从本地层级开始，然后上升到系统层级。

### 写一个配置项

以我们已经了解的`git config`为基础，我们来看一个写值的例子：

```bash
git config --global user.email "your_email@example.com"
```

​	此示例将配置项`user.email`的值写为 `your_email@example.com` 。它使用了 `--global` 标志，因此该值是为当前操作系统用户设置的。

### git 配置编辑器 - core.editor

许多 Git 命令都会启动一个文本编辑器，提示进一步输入。`git config`最常见的用例之一就是配置 Git 应该使用哪个编辑器。下面列出了常用的编辑器和与之匹配的 git 配置命令：

| 编辑器                               |                           配置命令                           |
| :----------------------------------- | :----------------------------------------------------------: |
| `Atom`                               |      `~ git config --global core.editor "atom --wait"~`      |
| `emacs`                              |         `~ git config --global core.editor "emacs"~`         |
| `nano`                               |        `~ git config --global core.editor "nano -w"~`        |
| `vim`                                |          `~ git config --global core.editor "vim"~`          |
| `Sublime Text (Mac)`                 |      `~ git config --global core.editor "subl -n -w"~`       |
| `Sublime Text (Win, 32-bit install)` | `~ git config --global core.editor "'c:/program files (x86)/sublime text 3/sublimetext.exe' -w"~` |
| `Sublime Text (Win, 64-bit install)` | `~ git config --global core.editor "'c:/program files/sublime text 3/sublimetext.exe' -w"~` |
| `Textmate`                           |        `~ git config --global core.editor "mate -w"~`        |

### 配置合并工具

如果出现合并冲突，Git 会启动一个 "合并工具"。默认情况下，Git 使用的是通用 Unix diff 程序的内部实现。Git 内部的 diff 程序是最基本的合并冲突查看器。有许多外部第三方合并冲突解决工具可以替代它。有关各种合并工具和配置的概述，请参阅"[tips and tools to resolve conflits with Git](https://developer.atlassian.com/blog/2015/12/tips-tools-to-solve-git-conflicts/)"指南。

```bash
git config --global merge.tool kdiff3
```

### 配置彩色输出

Git 支持彩色终端输出，有助于快速读取 Git 输出。你可以自定义 Git 输出，使用个性化的颜色主题。git config 命令用于设置这些颜色值。

`color.ui`是 Git 颜色的主变量。设置为 `false` 将禁用所有 Git 彩色终端输出。

```bash
git config --global color.ui false
```

默认情况下，`color.ui` 设置为`auto`，这将在直接终端输出流中应用颜色。如果输出流被重定向到文件或管道到其他进程，自动设置将省略颜色代码输出。

可以将 `color.ui` 值设置为`always`，这样在将输出流重定向到文件或管道时也会应用颜色代码输出。这可能会无意中造成问题，因为接收管道可能并不期待彩色编码输入。

### Git 颜色值

除了 `color.ui`，还有许多其他细粒度的颜色设置。与 `color.ui` 一样，这些颜色设置都可以设置为 `false`、`auto` 或 `always`。这些颜色设置还可以设置特定的颜色值。支持的颜色值举例如下

- `normal`
- `black`
- `red`
- `green`
- `yellow`
- `blue`
- `magenta`
- `cyan`
- `white`

颜色也可以指定为十六进制颜色代码（如 `#ff0000`），或者 ANSI 256 颜色值（如果您的终端支持）。

1. `color.branch`

   配置 Git 分支命令的输出颜色

2. `color.branch.`<`slot`> 

   - 此值也适用于 Git 分支输出。`<slot>` 是以下选项之一：
     1. `current`: 当前分支 
     2. `local`: 本地分支 
     3. `remote`: refs/remotes中的远程分支
     4. `upstream`: 上游跟踪分支 
     5. `plain`: 任何其他引用

3. `color.diff`

   为 `git diff`、`git log` 和 `git show`命令 配置输出颜色

4. `color.grep`

   为 `git grep` 的应用输出颜色。

5. `color.showBranch` 

   启用或禁用`git show branch`命令的颜色输出

6. `color.status` 

### Aliases-别名

你可能对操作系统命令行中的别名概念并不陌生；如果不熟悉，别名是一种自定义快捷方式，它定义了哪条命令将扩展为更长或更组合的命令。别名可以节省输入常用命令的时间和精力。Git 提供了自己的别名系统。Git 别名的一个常见用例是缩短提交命令。Git 别名存储在 Git 配置文件中。这意味着你可以使用 git config 命令来配置别名。

```bash
git config --global alias.ci commit
```

这个例子为 `commit` 命令创建了一个 `ci` 别名。然后你就可以通过执行 `git ci` 来调用 `git commit`。别名还可以引用其他别名来创建强大的组合。

-------

## git alias

本节将重点讨论 Git 别名。要更好地理解 Git 别名的价值，我们必须先讨论一下什么是别名。别名是快捷方式的同义词。

创建别名是其他流行工具（如 `bash` `shell`）的常见模式。别名用于创建可映射到较长命令的较短命令。别名可以减少执行命令所需的击键次数，从而提高工作流程的效率。

以 `git checkout` 命令为例。`checkout` 命令是一个频繁使用的 git 命令，随着时间的推移，按键次数会不断增加。我们可以创建一个别名，将 `git co` 映射到 `git checkout`，这样就可以用更短的按键形式：`git co` 代替，从而节省宝贵的人类指尖力量。

需要注意的是，没有直接的 `git alias` 命令。别名是通过使用 `git config` 命令和 Git 配置文件创建的。与其他配置值一样，别名可以在本地或全局范围内创建。

为了更好地理解 Git 别名，让我们举几个例子。

```bash
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
```

前面的代码示例为常用的 git 命令创建了全局存储的快捷方式。创建别名不会修改源代码命令。因此，即使我们现在有了 `git co` 别名，`git checkout` 仍然可用。这些别名是使用 `--global` 标志创建的，这意味着它们将存储在 Git 的全局操作系统级配置文件中。在 Linux 系统中，全局配置文件位于用户主目录下的 `/.gitconfig`。

```bash
[alias]
        co = checkout
            br = branch
            ci = commit
            st = status
```

### 如何创建 Git 别名？

可以通过两种主要方法创建别名：

1. **直接编辑 Git 配置文件**

   可以手动编辑并保存全局或本地配置文件以创建别名。全局配置文件位于`$HOME/.gitconfig`文件路径中。本地路径位于活动 git 存储库中`/.git/config`

   配置文件将遵循如下所示的部分：`[alias]`

   ```bash
   [alias]
    co = checkout
   ```

2. #### 使用 git config 创建别名

   如前所述，`git config` 命令是快速创建别名的便捷工具。`git config`命令实际上是一个辅助工具，用于写入全局和本地 Git 配置文件。

   ```bash
   git config --global alias.co checkout
   ```

------

# 保存更改

在 Git 或其他版本控制系统中工作时，"保存 "的概念要比在文字处理程序或其他传统文件编辑程序中的 "保存 "更为细微。传统软件中的 "保存 "与 Git 中的 "提交 "同义。提交相当于 Git 的 "保存"。传统的 "保存 "应被视为一种文件系统操作，用于覆盖现有文件或写入新文件。而 Git 的提交则是对文件和目录集合的操作。

在 Git 和 SVN 中保存更改也是一个不同的过程。SVN 提交或“签入”是远程推送到集中式服务器的操作。这意味着 SVN 提交需要互联网访问才能完全“保存”项目更改。Git 提交可以在本地捕获并建立，然后根据需要使用 `git push -u origin main` 命令推送到远程服务器。Git 是分布式应用模式，而 SVN 是集中式模式。分布式应用程序通常更健壮，因为它们不像集中式服务器那样存在单点故障。

`git add`、`git status` 和 `git commit` 这几条命令结合使用，可以保存 Git 项目当前状态的快照。

Git 有一个额外的保存机制，做 "储藏库"。储藏室是一个短暂的存储区域，用于存储尚未提交的变更。储藏室在工作目录（三棵树中的第一棵）上运行，有很多使用选项。要了解更多信息，请访问 [git stash](https://www.atlassian.com/git/tutorials/saving-changes/git-stash) 页面。

Git 仓库可以配置为忽略特定文件或目录。这将阻止 Git 保存对忽略内容的修改。Git 有多种配置方法来管理忽略列表。关于 Git 忽略配置的更多详情，请参阅 [git ignore](https://www.atlassian.com/git/tutorials/saving-changes/gitignore) 页面。

## git add

`git add` 命令将工作目录中的改动添加到暂存区域。它告诉 Git，您想在下一次提交中包含对某个文件的更新。不过，`git add`并不会对版本库产生任何重大影响--直到运行 `git commit` 才会真正记录更改。

除了这些命令，你还需要使用 `git status` 来查看工作目录和暂存区域的状态。

### How it works

`git add`和`git commit`命令构成了 Git 的基本工作流程。无论团队的协作模式如何，每个 Git 用户都需要了解这两个命令。它们是将项目版本记录到版本库历史中的手段。

项目开发围绕着基本的编辑/暂存/提交模式。首先，在工作目录中编辑文件。准备好保存一份项目当前状态的副本时，就用`git add`进行阶段性修改。对暂存的快照满意后，用`git commit`将其提交到项目历史中。`git reset`命令用于撤销提交或暂存快照

除了`git add` 和 `git commit`，第三个命令 `git push` 对于完整的 Git 协作工作流程也必不可少。`git push`用于将提交的更改发送到远程存储库以进行协作。这使得其他团队成员能够访问一组已保存的更改。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-push-2x.png" style="zoom:50%;" />

### The staging area-暂存区

`git add`命令的主要功能是将工作目录中的待处理更改推广到 git 暂存区域。暂存区是 Git 较为独特的功能之一，如果你来自 SVN（甚至 Mercurial）背景，可能需要一些时间来理解它。把它想象成工作目录和项目历史记录之间的缓冲区会有所帮助。暂存区与工作目录和提交历史一起被视为 Git 的 "三棵树"。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-three-tree-2x.png" style="zoom:50%;" />

在提交到项目历史之前，暂存区可以让你把相关的改动归类为高度集中的快照，而不是提交上次提交后的所有改动。这意味着你可以对不相关的文件进行各种编辑，然后通过将相关变更添加到阶段并逐条提交，将它们分割成符合逻辑的提交。在任何版本控制系统中，创建原子提交都是很重要的，这样可以轻松跟踪错误，并在对项目其他部分影响最小的情况下还原更改。

### 常用命令

```bash
git add <file>
```

暂存 `<file>` 中的所有更改，以便下一次提交。

```bash
git add <directory>
```

暂存目录 `<directory>` 中的所有更改，以便下一次提交。

```bash
git add -p
```

开始交互式暂存会话，让你选择文件的部分内容添加到下一次提交中。系统会显示一大段修改，并提示你输入命令。使用 `y` 可以暂存该修改块，使用 `n` 可以忽略该修改块，使用 `s` 可以将其分割成更小的修改块，使用 `e` 可以手动编辑该修改块，使用 `q` 可以退出。

在启动一个新项目时，`git add`的功能与 svn import 相同。要创建当前目录的初始提交，请使用以下两条命令：

```bash
git add .
git commit
```

### 总结

回顾一下，`git add` 是一系列操作中的第一个命令，它指示 Git 将当前项目状态的快照 "保存 "到提交历史中。单独使用时，`git add` 会将工作目录中的待定修改推进到暂存区域。`git status` 命令用于检查版本库的当前状态，并可用于确认 `git add` 的推送。`git reset` 命令用于撤销 `git add`。然后使用 `git commit` 命令将暂存目录的快照提交到版本库的提交历史中。

---

## git diff

Diffing 是一个接收两个输入数据集并输出它们之间差异的函数。`git diff`是一条多用途 Git 命令，执行时会在 Git 数据源上运行差异函数。这些数据源可以是提交、分支、文件等。本文将讨论`git diff`的常见调用方式和差异化工作流程模式。`git diff`命令通常与`git status`和`git log`一起用于分析 Git 仓库的当前状态。

### 读取差异：输出

1. **比较输入**

   ```bash
   diff --git a/diff_test.txt b/diff_test.txt
   ```

   这一行显示 `diff` 的输入源。我们可以看到，`a/diff_test.txt` 和 `b/diff_test.txt` 已被传递给 `diff`。

2. **元数据**

   ```bash
   index 6b0c6cf..b37e70a 100644
   ```

   这一行显示一些 Git 内部元数据。您很可能不需要这些信息。输出中的数字对应 Git 对象版本哈希标识符。

3. **变更标记**

   ```
   --- a/diff_test.txt
   +++ b/diff_test.txt
   ```

   这是为每个差异输入源分配符号的案例。在本例中，来自文件 `a/diff_test.txt` 的变更用符号 `---` 标记，来自文件 `b/diff_test.txt` 的变更用符号 `+++` 标记。

4. **变更高亮**

   ```bash
   git diff --color-words
   ```

   `git diff` 还有一种特殊模式，能以更好的粒度高亮显示改动：`--color-words`。该模式用空白标记添加和删除的行，然后进行差异化。

### 比较文件：git diff file

```bash
git diff HEAD ./path/to/file
```

git diff 命令可以通过一个明确的文件路径选项。如果给`git diff`传递了一个文件路径，diff 操作就会作用于指定的文件。

此示例的作用域是 `./path/to/file`，调用时，它会将工作目录中的具体改动与索引进行比较，显示尚未暂存的改动。默认情况下，`git diff`会执行与`HEAD`的比较。

```bash
git diff --cached ./path/to/file
```

当使用`--cached`选项调用`git diff`时，diff 会将已缓存的变更与本地版本库进行比较。`--cached`选项与`--staged`选项同义。

### 比较所有变化

`git diff`命令不带文件路径的调用将比较整个存储库中的更改。上述文件特定示例可以在没有参数的情况下调用`./path/to/file`，并且在本地存储库中的所有文件中具有相同的输出结果。

### 自上次提交以来的更改

默认情况下，`git diff`将显示自上次提交以来所有未提交的更改。

```bash
git diff
```

### 比较两个不同提交之间的文件差异

`git diff`可以将提交的 Git refs 传递给 diff。例如 HEAD、标签和分支名。Git 中的每个提交都有一个commit ID，执行`GIT LOG`命令时可以得到。您也可以将此commit ID 传递给`git diff`。

```bash
git log --pretty=oneline
957fbc92b123030c389bf8b4b874522bdf2db72c add feature
ce489262a1ee34340440e55a0b99ea6918e19e7a rename some classes
6b539f280d8b0ec4874671bae9c6bed80b788006 refactor some code for feature
646e7863348a427e1ed9163a9a96fa759112f102 add some copy to body

$:> git diff 957fbc92b123030c389bf8b4b874522bdf2db72c ce489262a1ee34340440e55a0b99ea6918e19e7a
```

### 比较分支

```bash
git diff branch1..branch2
```

本例介绍点运算符。示例中的两个点表示 diff 输入是两个分支的顶端。如果省略点，在分支之间使用空格，也会产生同样的效果。此外，还有一个三点操作符：

```
git diff branch1...branch2
```

## 比较两个分支的文件差异

要跨分支比较特定文件，请将文件的路径作为第三个参数传递给`git diff`

```bash
git diff main new_branch ./diff_test.txt
```

---------

## git stash

`git stash`可以暂时搁置（或储藏）您对工作副本所做的修改，这样您就可以处理其他事情，稍后再回来重新应用。如果您需要快速切换上下文并处理其他工作，但代码改动进行到一半还没准备好提交，那么`stash`就很方便了。

### Stashing your work

`git stash`命令会获取未提交的修改（包括已暂存和未暂存的），将其保存起来以备后用，然后从工作副本中将其还原。例如:

```bash
$ git status
On branch main
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

$ git stash
Saved working directory and index state WIP on main: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage

$ git status
On branch main
nothing to commit, working tree clean
```

这时，你可以自由地进行修改、创建新提交、切换分支以及执行其他任何 Git 操作；准备好后，再回来重新应用你的储藏。

请注意，储藏库是本地的 Git 仓库；推送时储藏库不会转移到服务器上。

### Re-applying your stashed changes

你可以使用`git stash pop`命令重新应用之前储藏的更改：

```bash
$ git status
On branch main
nothing to commit, working tree clean
$ git stash pop
On branch main
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Dropped refs/stash@{0} (32b3aa1d185dfe6d57b3c3cc3b32cbf3e380cc6a)
```

*Popping* 会重新应用之前储藏的更改，并从储藏库中将其删除。

或者，你也可以使用`git stash apply`将更改重新应用到工作副本，并将其继续保留在储藏库中：

```bash
$ git stash apply
On branch main
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html
```

如果你想在多个分支中应用相同的隐藏更改，这一点非常有用。

现在你已经了解了储藏的基础知识，但还需要注意一个问题：默认情况下，Git 不会储藏对未跟踪或忽略的文件所做的修改。

### Stashing untracked or ignored files

默认情况下，运行 `git stash` 会储藏以下内容：

- 已添加到索引的变更（已暂存变更）
- 对当前由 Git 跟踪的文件所做的更改（未暂存的更改）

但它不会储藏以下内容：

- 工作副本中尚未暂存的新文件
- 被忽略的文件

因此，如果我们在上面的例子中添加了第三个新文件，但没有将其放入暂存阶段（即没有运行`git add`），那么`git stash`也不会将其存放起来。

```bash
$ script.js

$ git status
On branch main
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Untracked files:

    script.js

$ git stash
Saved working directory and index state WIP on main: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage

$ git status
On branch main
Untracked files:

    script.js

```

添加`-u`选项（或`--include-untracked`选项）后，`git stash`也会将未跟踪的文件储藏起来：

```bash
$ git status
On branch main
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Untracked files:

    script.js

$ git stash -u
Saved working directory and index state WIP on main: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage

$ git status
On branch main
nothing to commit, working tree clean
```

在运行`git stash`时，你也可以通过`-a`选项（或`--all`选项）来包含对忽略文件的修改。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-stash-option-2x.png" style="zoom:50%;" />

### Managing multiple stashes

你并不局限于一个储藏库。你可以多次运行 git stash 来创建多个储藏库，然后使用`git stash list`来查看它们。默认情况下，储藏库会在您创建储藏库的分支和提交上简单标识为 "`WIP`"（正在进行中）。时间久了，就很难记住每个匿藏库包含的内容：

```bash
$ git stash list
stash@{0}: WIP on main: 5002d47 our new homepage
stash@{1}: WIP on main: 5002d47 our new homepage
stash@{2}: WIP on main: 5002d47 our new homepage
```

为了提供更多的上下文信息，使用 `git stash save "message "`为储藏注释说明是个不错的做法：

```bash
$ git stash save "add style to our site"
Saved working directory and index state On main: add style to our site
HEAD is now at 5002d47 our new homepage

$ git stash list
stash@{0}: On main: add style to our site
stash@{1}: WIP on main: 5002d47 our new homepage
stash@{2}: WIP on main: 5002d47 our new homepage
```

默认情况下，`git stash pop`会重新应用最近创建的储藏： `stash@{0}`

例如，你可以通过最后一个参数传递储藏库的标识符来选择要重新应用的储藏库：

```bash
$ git stash pop stash@{2}
```

### Viewing stash diffs

您可以使用`git stash show`查看储藏的摘要：

```bash
$ git stash show
 index.html | 1 +
 style.css | 3 +++
 2 files changed, 4 insertions(+)
```

或者通过`-p`选项（或`--patch`）来查看储藏的完整差异：

```bash
$ git stash show -p
diff --git a/style.css b/style.css
new file mode 100644
index 0000000..d92368b
--- /dev/null
+++ b/style.css
@@ -0,0 +1,3 @@
+* {
+  text-decoration: blink;
+}
diff --git a/index.html b/index.html
index 9daeafb..ebdcbd2 100644
--- a/index.html
+++ b/index.html
@@ -1 +1,2 @@
+<link rel="stylesheet" href="style.css"/>
```

### Partial stashes

你也可以选择只储藏一个文件、一组文件或文件中的单个改动。如果向`git stash`传递`-p`选项（或`--patch`），它将遍历工作副本中每个更改的“块”并询问您是否希望储藏它:

```bash
$ git stash -p
diff --git a/style.css b/style.css
new file mode 100644
index 0000000..d92368b
--- /dev/null
+++ b/style.css
@@ -0,0 +1,3 @@
+* {
+  text-decoration: blink;
+}
Stash this hunk [y,n,q,a,d,/,e,?]? y
diff --git a/index.html b/index.html
index 9daeafb..ebdcbd2 100644
--- a/index.html
+++ b/index.html
@@ -1 +1,2 @@
+<link rel="stylesheet" href="style.css"/>
Stash this hunk [y,n,q,a,d,/,e,?]? n
```

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-stash-p-2x.png" style="zoom:50%;" />

你可以点击? 查看所有的块命令。常用的有:

| Command |                         Description                          |
| :------ | :----------------------------------------------------------: |
| /       |       `search for a hunk by regex`(通过正则表达式搜索)       |
| ?       |                             help                             |
| n       |          don't stash this hunk（不储藏这个修改块）           |
| q       | quit (any hunks that have already been selected will be stashed) 退出 |
| s       |     split this hunk into smaller hunks（拆分成更小的块）     |
| y       |              stash this hunk（储藏这个修改块）               |

没有明确的 "中止 "命令，但点击 CTRL-C(SIGINT)可以中止储藏过程。

------

### Creating a branch from your stash

如果分支上的更改与储藏库中的更改不同，则在弹出或应用存储时可能会遇到冲突。相反，您可以`git stash branch`创建一个新分支来将储藏的更改应用到新分支：

```bash
$ git stash branch add-stylesheet stash@{1}
Switched to a new branch 'add-stylesheet'
On branch add-stylesheet
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Dropped refs/stash@{1} (32b3aa1d185dfe6d57b3c3cc3b32cbf3e380cc6a)
```

这会根据你创建储藏的提交检查出一个新的分支，然后将你储藏的更改添加到该分支中。

### Cleaning up your stash

如果决定不再需要某个特定的储藏库，可以使用`git stash drop`删除它：

```bash
$ git stash drop stash@{1}
Dropped stash@{1} (17e2697fd8251df6163117cb3d58c1f62a5e7cdb)
```

或者，您也可以使用以下方法删除所有储藏库：

```bash
$ git stash clear
```

### How git stash works

如果你只想知道如何使用 `git stash`，那就别读了。但如果你想知道 Git（和`git stash`）在底层是如何工作的，请继续往下读！

储藏库实际上是以提交对象的形式在版本库中编码的。`.git/refs/stash` 中的特殊 ref 指向最近创建的 `stash`，而之前创建的 stash 则由 stash ref 的 reflog 引用。这就是为什么你会用 `stash@{n}` 来引用 `stash`：你实际上是在引用 `stash` 引用 的第 n 个 `reflog` 条目。因为 `stash` 只是一个提交，所以可以用 `git log` 来检查它。

根据您存储的内容，单个`git stash`操作会创建两个或三个新提交。上图中的提交是：

- `stash@{0}`，一个新提交，用于存储运行 `git stash` 时工作副本中的跟踪文件
- `stash@{0}`的第一个父级，也就是运行 `git stash` 时位于 HEAD 的已有提交
- `stash@{0}`的第二个父级，一个新的提交代表您运行`git stash`时的索引
- `stash@{0}`的第三个父级，一个新的提交，代表您运行`git stash`时工作副本中未跟踪的文件。该第三个父级仅在以下情况下创建：
  - 您的工作副本实际上包含了未跟踪文件；并且
  - 您在调用 `git stash` 时指定了 `--include-untracked` 或 `--all` 选项。

`git stash` 如何将工作树和索引编码为提交：

- 在`stash`之前，工作树可能包含对已跟踪文件、未跟踪文件和忽略文件的更改。其中一些更改也可能暂存于索引中。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/before-stashing-2x.png" style="zoom:33%;" />

- 调用`git stash`将对跟踪文件的任何更改编码为 DAG 中的两项新提交：一项用于未暂存的更改，一项用于索引中暂存的更改。特殊`refs/stash`引用已更新以指向它们。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-stash-2x.png" style="zoom:37%;" />

- 使用 `--include-untracked` 选项还会将对未跟踪文件的任何修改编码为额外提交。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-stash-untracked-2x.png" style="zoom:38%;" />

- 使用 `--all` 选项会将对任何忽略文件的修改与对未跟踪文件的修改一起包含在同一提交中。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-stash-all-2x.png" style="zoom:39%;" />

当你运行 `git stash pop` 时，上面提交的改动会被用来更新你的工作副本和索引，而 stash reflog 会被洗牌以移除pop的提交。请注意，弹出的提交不会被立即删除，但会成为未来垃圾回收的候选者。

-------

## .ignore

Git 将工作副本中的每个文件视为三种情况之一：

1. `tracked`（已跟踪） -- 之前已暂存或已提交的文件；

2. `untracked`（未跟踪）--未暂存或未提交的文件；

3. `ignored`（忽略）--明确告知 Git 忽略的文件。

忽略的文件通常是构建工件和机器生成的文件，这些文件可以从版本库源代码中导出，或者不应提交。常见的例子有：

- 依赖缓存，如 /node_modules 或 /packages 的内容
- 编译过的代码，如 .o、.pyc 和 .class 文件
- 编译输出目录，如 /bin、/out 或 /target
- 运行时生成的文件，如 .log、.lock 或 .tmp
- 隐藏的系统文件，如 .DS_Store 或 Thumbs.db
- 个人IDE配置文件，如 .idea/workspace.xml

被忽略的文件会被记录在一个名为 `.gitignore` 的特殊文件中，该文件位于版本库的根目录下。没有明确的 git 忽略命令：相反，当您有新文件需要忽略时，必须手动编辑并提交 `.gitignore` 文件。`.gitignore` 文件包含的模式会与版本库中的文件名进行匹配，以决定是否忽略这些文件。

### Git ignore patterns

`.gitignore` 使用通配模式来匹配文件名。你可以使用各种符号来构建匹配模式：

| 模式                        |                           匹配示例                           |                             解释                             |
| :-------------------------- | :----------------------------------------------------------: | :----------------------------------------------------------: |
| **/logs                     |  logs/debug.log   logs/monday/foo.bar build/logs/debug.log   |     您可以在模式前加上双星号，以匹配版本库中的任何目录。     |
| **/logs/debug.log           | logs/debug.log   build/logs/debug.log  <br>*但不是* <br/>  logs/build/debug.log |  您还可以使用双星号，根据文件名及其父目录的名称来匹配文件。  |
| *.log                       |      debug.log <br/>foo.log<br/>.log<br/>logs/debug.log      |            星号是通配符，可匹配 0 个或多个字符。             |
| *.log <br> !important.log   |         debug.log<br/> *但不是* <br/>logs/debug.log          | 在模式前加上感叹号会否定该模式。如果文件与某个模式匹配，但也与文件后面定义的否定模式匹配，则不会被忽略。 |
| /debug.log                  |          debug.log<br/> *但不是*<br/>logs/debug.log          |    在否定模式之后定义的模式将重新忽略之前否定的任何文件。    |
| debug.log                   |                 debug.log<br/>logs/debug.log                 |             前缀斜线只匹配版本库根目录下的文件。             |
| debug?.log                  |   debug0.log<br/>debugg.log<br/> *但不是*<br/>debug10.log    |                    问号只能匹配一个字符。                    |
| debug[0-9].log              |   debug0.log<br/>debug1.log<br/> *但不是*<br/>debug10.log    |           方括号也可用于匹配指定范围内的单个字符。           |
| debug[01].log               | debug0.log<br/>debug1.log<br/> *但不是*<br/>debug2.log<br/>debug01.log |              方括号匹配指定字符集中的单个字符。              |
| debug[!01].log              | debug2.log<br/> *但不是*<br/>debug0.log<br/>debug1.log<br/>debug01.log |          感叹号可用于匹配指定字符集以外的任何字符。          |
| debug[a-z].log              |    debuga.log<br/>debugb.log<br/> *但不是*<br/>debug1.log    |                    范围可以是数字或字母。                    |
| logs                        | logs<br/>logs/debug.log<br/> logs/latest/foo.bar<br/>build/logs<br/>build/logs/debug.log | 如果不添加斜线，模式将同时匹配文件和该名称下的目录内容。在左边的匹配示例中，名为 logs 的目录和文件都会被忽略 |
| logs/                       | logs/debug.log<br/>logs/latest/foo.bar<br/>build/logs/foo.bar<br/>build/logs/latest/debug.log | 添加斜线表示该模式是一个目录。版本库中与该名称匹配的任何目录的全部内容（包括其所有文件和子目录）都将被忽略 |
| logs/   !logs/important.log |             logs/debug.log   logs/important.log              | 等一下！在左边的示例中，logs/important.log 不应该被忽略吗？不对！由于 Git 中一个与性能相关的怪癖，你不能否定一个由于模式匹配目录而被忽略的文件 |
| logs/**/debug.log           | logs/debug.log   logs/monday/debug.log   logs/monday/pm/debug.log |                  双星号可匹配零个或多个目录                  |
| logs/*day/debug.log         | logs/monday/debug.log   logs/tuesday/debug.log<br/>   *但不是*<br/>logs/latest/debug.log |                  目录名中也可以使用通配符。                  |
| logs/debug.log              | logs/debug.log<br/>   *但不是*<br/>  debug.log   build/logs/debug.log | 指定特定目录中文件的模式是相对于版本库根目录而言的。(如果你愿意，可以在前面加上斜线，但这并没有什么特别的作用）。 |

这些解释假定您的 `.gitignore` 文件按照惯例位于版本库的顶层目录中。如果您的存储库有多个 `.gitignore` 文件，只需在心里将“存储库根目录”替换为“包含 `.gitignore` 文件的目录”（并考虑统一它们，以确保您团队的理智）。

除了这些字符，你还可以使用 `#` 在 `.gitignore` 文件中加入注释：

```bash
# ignore all logs
*.log
```

如果文件或目录中包含 `.gitignore` 模式字符，你可以使用 `\` 来转义这些字符：

```bash
# ignore the file literally named foo[01].txt
foo\[01\].txt
```

### Shared .gitignore files in your repository

Git 忽略规则通常在版本库根目录下的 `.gitignore` 文件中定义。不过，你也可以选择在版本库的不同目录中定义多个 `.gitignore` 文件。特定 `.gitignore` 文件中的每个模式都会相对于该文件的目录进行测试。不过最简单的方法还是在根目录下定义一个 `.gitignore` 文件。在签入 `.gitignore` 文件时，它会像版本库中的其他文件一样进行版本控制，并在推送时与队友共享。通常情况下，你应该只在 `.gitignore` 文件中包含有利于版本库其他用户的模式。

### Personal Git ignore rules

你还可以在 `.git/info/exclude` 这个特殊文件中为特定版本库定义个人忽略模式。这些文件没有版本控制，也不随版本库一起发布，所以在这里加入可能只会对你有利的模式很合适。例如，如果你有自定义日志设置，或者有特殊的开发工具会在版本库的工作目录中生成文件，你可以考虑把它们添加到 `.git/info/exclude` 中，以防止它们被意外提交到版本库中。

### Global Git ignore rules

此外，你还可以通过设置 Git `core.excludesFile` 属性，为本地系统中的所有仓库定义全局 Git 忽略模式。这个文件需要自己创建。如果你不确定把全局 `.gitignore` 文件放在哪里，那么您的主目录是一个不错的选择（并且可以方便以后查找）。创建文件后，您需要使用以下命令配置其位置`git config`：

```bash
$ touch ~/.gitignore
$ git config --global core.excludesFile ~/.gitignore
```

由于不同的文件类型适用于不同的项目，因此在选择全局忽略的模式时应慎重。特殊的操作系统文件（如 .DS_Store 和 thumbs.db）或某些开发工具创建的临时文件是典型的全局忽略对象。

### Ignoring a previously committed file

如果想忽略过去提交过的文件，需要先从版本库中删除该文件，然后为其添加 `.gitignore` 规则。在 `git rm` 中使用 `--cached` 选项意味着该文件将从版本库中删除，但会作为忽略文件保留在工作目录中。

```bash
$ echo debug.log >> .gitignore
  
$ git rm --cached debug.log
rm 'debug.log'
  
$ git commit -m "Start ignoring debug.log"
```

如果想从版本库和本地文件系统中删除文件，可以省略 `--cached` 选项。

### Committing an ignored file

使用 `git add` 的 `-f`（或 `--force`）选项，可以强制将忽略的文件提交到版本库：

```bash
$ cat .gitignore
*.log
  
$ git add -f debug.log
  
$ git commit -m "Force adding debug.log"
```

如果定义了通用匹配模式（如 `*.log`），但又想提交特定文件，可以考虑这样做。不过，更好的解决办法是定义一般规则的例外：

```bash
$ echo !debug.log >> .gitignore
  
$ cat .gitignore
*.log
!debug.log
  
$ git add debug.log
  
$ git commit -m "Adding debug.log"
```

对于您的其他开发伙伴来说，这种方法更明显，也更不容易混淆。

### Stashing an ignored file

`git stash`是一项强大的 Git 功能，用于暂时储藏和还原本地更改，以便日后重新应用。如你所料，默认情况下，`git stash` 会忽略被忽略的文件，只存放 Git 追踪到的文件的改动。不过，你也可以使用 `--all` 选项调用 `git stash`，将被忽略和未被跟踪的文件的改动也储藏起来。

### Debugging .gitignore files

如果你有复杂的 `.gitignore`模式，或者这些模式分布在多个 `.gitignore` 文件中，就很难找出某个文件被忽略的原因。你可以使用 `git check-ignore` 命令和 `-v`（或 `--verbose`）选项来确定是哪种模式导致了某个文件被忽略：

```bash
$ git check-ignore -v debug.log
.gitignore:3:*.log  debug.log
```

输出显示：

```
<file containing the pattern> : <line number of the pattern> : <pattern>  <file name>
```

你可以向 `git check-ignore` 传递多个文件名，文件名本身甚至不必与版本库中存在的文件相对应。

--------

# 检查存储库

## git statue

`git status`命令会显示工作目录和暂存区域的状态。通过它，你可以看到哪些变更已被暂存，哪些未被暂存，以及哪些文件未被 Git 跟踪。状态输出不会显示任何有关已提交项目历史的信息。为此，你需要使用 `git log`。

### 相关 git 命令

- `git tag`
  - `tag`是指向 Git 历史记录中特定点的引用。 `git tag`通常用于捕获用于标记版本发布（即 v1.0.1）的历史记录点。 
- `git blame`
  - `git blame` 的高级功能是显示附加到文件中已提交行的作者元数据。这可以用来探索特定代码的历史，回答关于代码是什么、如何以及为什么被添加到版本库中的问题。
- `git log`
  - `git log` 命令会显示已提交的快照。您可以用它列出项目历史、过滤历史记录并搜索特定变更。

### Usage

```bash
git status
```

列出暂存、未暂存和未跟踪的文件。

`git status` 命令是一个相对简单的命令。它只需显示 `git add` 和`git commit`的状态。状态信息还包括文件暂存/未暂存的相关说明。下面的示例输出显示了 git 状态调用的三个主要类别：

```
# On branch main
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
#modified: hello.py
#
# Changes not staged for commit:
# (use "git add <file>..." to update what will be committed)
# (use "git checkout -- <file>..." to discard changes in working directory)
#
#modified: main.py
#
# Untracked files:
# (use "git add <file>..." to include in what will be committed)
#
#hello.pyc
```

### Ignoring Files

未跟踪文件通常分为两类。它们要么是刚添加到项目中但尚未提交的文件，要么是编译过的二进制文件，如 .pyc、.obj、.exe 等。在 git 状态输出中包含前者肯定是有好处的，但后者会让人很难看出仓库里到底发生了什么。

因此，Git 可以让你完全忽略文件，方法是把路径放在一个叫做 `.gitignore` 的特殊文件中。任何你想忽略的文件都应该单独列一行，`*`符号可以用作通配符。例如，在项目根目录下的 `.gitignore` 文件中添加以下内容，就能阻止编译后的 Python 文件出现在 `git status` 中：

```
*.pyc
```

在提交更改之前，检查版本库的状态是个很好的做法，这样就不会不小心提交了一些你不想提交的内容。此示例显示了暂存和提交快照前后的版本库状态：

```bash
# Edit hello.py
git status
# hello.py is listed under "Changes not staged for commit"
git add hello.py
git status
# hello.py is listed under "Changes to be committed"
git commit
git status
# nothing to commit (working directory clean)
```

第一个状态输出将显示文件未暂存。`git add` 操作会反映在第二个 git 状态输出中，最后一个状态输出会告诉你没有要提交的内容--工作目录与最近的提交一致。有些 Git 命令（如 `git merge`）要求工作目录必须是干净的，以免意外覆盖改动。

## git log

`git log` 命令会显示已提交的快照。通过它，您可以列出项目历史，对其进行过滤，并搜索特定的改动。`git status` 可以查看工作目录和暂存区域，而 `git log` 只能查看已提交的历史记录。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-log-2x.png" style="zoom:50%;" />

日志输出可通过多种方式自定义，从简单过滤提交到以完全由用户定义的格式显示。下面介绍一些最常见的 git 日志配置。

```bash
git log
```

使用默认格式显示整个提交历史。如果输出占用了一个屏幕以上，可以使用`空格`滚动，使用 `q` 退出。

```bash
git log -n <limit>
```

限制显示条数，例如，`git log -n 3` 只显示最近 3 次提交。

将每个提交压缩为一行。这有助于获得项目历史的高层概览。

```bash
git log --oneline
```

```bash
git log --stat
```

除了普通的 `git log` 信息外，还包括哪些文件被修改，以及每个文件被添加或删除的行数。

```bash
git log -p
```

显示代表每次提交的补丁。这将显示每次提交的完整差异，是项目历史最详细的视图。

```bash
git log --author="<pattern>"
```

搜索特定作者的提交。参数 `＜pattern`＞ 可以是纯字符串或正则表达式。

```bash
git log --grep="<pattern>"
```

搜索特定`commit` 提交信息的提交。参数 `＜pattern`＞ 可以是纯字符串或正则表达式。

```bash
git log <since>..<until>
```

只显示 `<since>` 和 `<until>` 之间的提交。两个参数都可以是`commit ID`、分支名称、HEAD 或任何其他类型的修订引用。

```bash
git log <file>
```

只显示包含指定文件的提交。这是查看特定文件历史记录的简便方法。

```bash
git log --graph --decorate --oneline
```

有几个有用的选项值得考虑。`--graph` 标志会在提交信息的左侧绘制基于文本的提交图表。`--decorate`（装饰）标记会添加分支名称或提交标签。`--oneline`（单线）会将提交信息显示在一行上，方便用户一目了然地浏览提交信息。

检查文件状态:

```
commit 3157ee3718e180a9476bf2e5cab8e3f1e78a73b7
Author: John Smith
```

大部分内容都很简单明了，但第一行需要解释一下。提交后的 40 个字符字符串是提交内容的 SHA-1 校验和。这样做有两个目的。首先，它可以确保提交的完整性--如果提交内容被破坏，提交将生成不同的校验和。其次，它可以作为提交的唯一 ID。

这个 ID 可以在 `git log ..` 等命令中用来指代特定的提交。例如，`git log 3157e..5ab91`  将显示 ID 为 3157e 和 5ab91 的提交之间的所有内容。除了校验和之外，分支名（分支模块中有讨论）和 HEAD 关键字也是引用单个提交的常用方法。HEAD 总是指当前提交，无论是分支还是特定提交。

`~` 字符用于相对引用提交的父提交。例如，3157e\~1 指的是 3157e 之前的提交，而 HEAD\~3 则是当前提交的曾祖父。

前面提供了许多 `git log` 的示例，但请记住，多个选项可以合并为一条命令：

```bash
git log --author="John Smith" -p hello.py
```

这将显示约翰-史密斯对文件 hello.py 所做更改的完整差异。

`..`语法是比较分支的一个非常有用的工具。下一个示例将简要显示所有在`some-feature`中但不在`main`中的提交。

```bash
git log --oneline main..some-feature
```

-------------

## git tag

本文将讨论 Git 标签概念和 git 标签命令。`tag`是指向 Git 历史中特定点的 ref。标签一般用于捕捉历史中的某一点，该点用于标记版本发布（如`v1.0.1`）。

标签就像一个不会改变的分支。与分支不同的是，标签在创建后就不会再有提交历史。有关分支的更多信息，请访问 git 分支页面。

本文将介绍不同类型的标签、如何创建标签、列出所有标签、删除标签、共享标签等内容。

### 创建标签

要创建新标签，请执行以下命令：

```bash
git tag <tagname>
```

将 `<tagname>` 替换为创建标签时版本仓库状态的语义标识符。常见的模式是使用版本号，如 `git tag v1.4`。Git 支持两种不同类型的标签：注释标签和轻量级标签。前面的例子创建了一个轻量级标签。轻量级标签和注释标签存储的元数据量不同。最佳做法是将注释标签视为公共标签，将轻量级标签视为私有标签。注释标签存储额外的元数据，例如：标签名称、电子邮件和日期。这对于公开发布来说是非常重要的数据。轻量级标签本质上是提交的 "书签"，它们只是一个名称和指向提交的指针，可用于创建指向相关提交的快速链接。

### 附加说明注释的标签

注释标签（`annotated tags`）作为存储在 Git 数据库中完整对象。它们存储了额外的元数据，如：标签名称、电子邮件和日期。与提交和提交信息类似，注释标签也有标签信息。此外，为了安全起见，注释标签可以使用 GNU Privacy Guard（GPG）进行签名和验证。建议使用 `git tag` 的最佳做法是，优先使用注释标签，而不是轻量级标签，这样就能获得所有相关的元数据。

```bash
git tag -a v1.4
```

执行该命令将创建一个标有 `v1.4` 的新注释标签。然后，该命令将打开配置的默认文本编辑器，提示进一步输入元数据。

```bash
git tag -a v1.4 -m "my version 1.4"
```

执行该命令的过程与之前的调用类似，不过这个版本的命令会传入 `-m` 选项和一条信息。这是一种类似于 `git commit -m` 的便捷方法，它会立即创建一个新标签，并放弃打开本地文本编辑器，转而保存通过 `-m` 选项传递的信息。

### 轻量级标签

```bash
git tag v1.4-lw
```

执行该命令将创建一个轻量级标签，标识为 `v1.4-lw`。创建轻量级标签时不使用 `-a`、`-s` 或 `-m` 选项。轻量级标签会创建一个新的标签校验和，并将其存储在项目 repo 的 `.git/` 目录中。

### 列出所有标签

要列出 repo 中已存储的标记，请执行以下操作：

```
git tag
```

这将输出一个标签列表：

```
v0.10.0
    v0.10.0-rc1
    v0.11.0
    v0.11.0-rc1
    v0.11.1
    v0.11.2
    v0.12.0
    v0.12.0-rc1
    v0.12.1
    v0.12.2
    v0.13.0
    v0.13.0-rc1
    v0.13.0-rc2
```

要细化标签列表，`-l` 选项可与通配符表达式一起使用：

```bash
$ git tag -l *-rc*
    v0.10.0-rc1
    v0.11.0-rc1
    v0.12.0-rc1
    v0.13.0-rc1
    v0.13.0-rc2
    v0.14.0-rc1
    v0.9.0-rc1
    v15.0.0-rc.1
    v15.0.0-rc.2
    v15.4.0-rc.3
```

上例中使用了 `-l` 选项和 `-rc` 通配符表达式，可返回所有标有 `-rc` 前缀（传统上用于识别候选发布版本）的标记列表。

### 标记旧提交

默认情况下，`git tag`将在`HEAD`引用的提交上创建一个标签。另外，也可以将 `git tag` 作为特定提交的引用。这将标记所传递的提交，而不是默认的 `HEAD`。要收集较早提交的列表，请执行 `git log` 命令。

```bash
$ git log --pretty=oneline
    15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'feature'
    a6b4c97498bd301d84096da251c98a07c7723e65 add update method for thing
    0d52aaab4479697da7686c15f77a3d64d9165190 one more thing
    6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
```

执行 git log 会输出提交列表。在本例中，我们将选取注释为 "Merge branch 'feature' "去创建新标签。我们需要引用提交的 SHA 哈希值传递给 Git：

```bash
git tag -a v1.2 15027957951b64cf874c3557a0f3547bd83b3ff6
```

执行上述`git tag`调用将将为SHA哈希值为'15027957951b64cf874c3557a0f3547bd83b3ff6' 的提交创建一个新标签`v1.2`。

### 重新标记/替换旧标签

如果试图创建一个与现有标签标识符相同的标签，Git 会抛出类似的错误：

```bash
fatal: tag 'v0.4' already exists
```

此外，如果您尝试用现有的标记标识符标记旧提交，Git 也会抛出同样的错误。

如果必须更新现有标签，则必须使用 `-f` FORCE 选项。

```bash
git tag -a -f v1.4 15027957951b64cf874c3557a0f3547bd83b3ff6
```

执行上述命令将把 15027957951b64cf874c3557a0f3547bd83b3ff6 提交映射到 `v1.4` 标签标识符。它将覆盖 `v1.4` 标签的任何现有内容。

### 共享：向远程仓库推送标签

共享标签与推送分支类似。默认情况下，`git push` 不会推送标签。标签必须明确传递给 `git push`。

```bash
$ git push origin v1.4
    Counting objects: 14, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (12/12), done.
    Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
    Total 14 (delta 3), reused 0 (delta 0)
    To git@bitbucket.com:atlasbro/gittagdocs.git
     * [new tag]         v1.4 -> v1.4
```

要同时推送多个标签，可在 `git push` 命令中加入 `--tags` 选项。当其他用户克隆或拉取 repo 时，他们就会收到新标签。

### 检查标签

您可以使用 `git checkout` 命令在标签上查看 repo 的状态。

```
git checkout v1.4
```

上述命令将签出 v1.4 标签。这将使 repo 处于分离`HEAD`的状态。这意味着任何更改都不会更新标签。它们将创建一个新的分离提交。这个新的分离提交不会成为任何分支的一部分，只能通过提交的 SHA 哈希值直接访问。因此，在分离 HEAD 状态下进行修改时，最好先创建一个新的分支。

### 删除标签

删除标签的操作很简单。向 `git tag` 传递 `-d` 选项和标签标识符，就能删除标识的标签。

```
$ git tag
    v1
    v2
    v3
    $ git tag -d v1
    $ git tag
    v2
    v3
```

在本例中，执行`git tag`会显示一个标签列表，其中显示 `v1`、`v2`、`v3`，然后执行 `git tag -d v1` 会删除`v1`标签。

-------

## git blame

`git blame` 命令是一个多功能的故障诊断工具，有大量的使用选项。`git blame` 的高级功能是显示附加在文件的特定提交行上的作者元数据。这可以用来检查文件历史中的特定点，了解修改该行的最后一个作者是谁。`git blame` 还可以用来探索特定代码的历史，回答代码是什么、如何以及为什么被添加到版本库中的问题。

`Git blame`通常与 GUI 显示屏一起使用。在线 Git 托管网站（如 Bitbucket）提供的`blame`视图是 Git blame 的 UI 包装。这些视图在围绕拉取请求和提交的协作讨论中被引用。此外，大多数集成了 Git 的集成开发环境也有动态 blame 视图。

### How it works

为了演示 `git blame`，我们需要一个有一定历史的版本库。我们将使用开源项目 [git-blame-example](https://bitbucket.org/kevzettler/git-blame-example)。这个开源项目是一个简单的版本库，包含一个 README.md 文件，其中有一些来自不同作者的提交。`git blame`使用示例的第一步是`git clone` 该示例仓库。

```bash
git clone https://kevzettler@bitbucket.org/kevzettler/git-blame-example.git && cd git-blame-example
```

现在我们有了示例代码的副本，可以用`git blame`开始探索它了。使用`git log`可以查看示例仓库的状态。提交历史应该如下所示：

```bash
$ git log
    commit 548dabed82e4e5f3734c219d5a742b1c259926b2
    Author: Juni Mukherjee <jmukherjee@atlassian.com>
    Date:   Thu Mar 1 19:55:15 2018 +0000

        Another commit to help git blame track the who, the what, and the when

    commit eb06faedb1fdd159d62e4438fc8dbe9c9fe0728b
    Author: Juni Mukherjee <jmukherjee@atlassian.com>
    Date:   Thu Mar 1 19:53:23 2018 +0000

        Creating the third commit, along with Kev and Albert, so that Kev can get git blame docs.

    commit 990c2b6a84464fee153253dbf02e845a4db372bb
    Merge: 82496ea 89feb84
    Author: Albert So <aso@atlassian.com>
    Date:   Thu Mar 1 05:33:01 2018 +0000

        Merged in albert-so/git-blame-example/albert-so/readmemd-edited-online-with-bitbucket-1519865641474 (pull request #2)

        README.md edited online with Bitbucket

    commit 89feb84d885fe33d1182f2112885c2a64a4206ec
    Author: Albert So <aso@atlassian.com>
    Date:   Thu Mar 1 00:54:03 2018 +0000

        README.md edited online with Bitbucket
```

`git blame` 只能对单个文件进行操作。任何有用的输出都需要文件路径。`git blame` 的默认执行方式只是输出命令帮助菜单。在本例中，我们将对 `README.MD` 文件进行操作。在 git 仓库的根目录中包含一个 README 文件作为项目的文档源是开源软件的常见做法。

```
git blame README.MD
```

执行上述命令后，我们将获得第一个`blame`输出示例。以下输出是 `README` 中全部责备输出的子集。此外，该输出是静态的，反映了本文撰写时软件包的状态。

```bash
$ git blame README.md
    82496ea3 (kevzettler     2018-02-28 13:37:02 -0800  1) # Git Blame example
    82496ea3 (kevzettler     2018-02-28 13:37:02 -0800  2)
    89feb84d (Albert So      2018-03-01 00:54:03 +0000  3) This repository is an example of a project with multiple contributors making commits.
    82496ea3 (kevzettler     2018-02-28 13:37:02 -0800  4)
    82496ea3 (kevzettler     2018-02-28 13:37:02 -0800  5) The repo use used elsewhere to demonstrate `git blame`
    82496ea3 (kevzettler     2018-02-28 13:37:02 -0800  6)
    89feb84d (Albert So      2018-03-01 00:54:03 +0000  7) Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod TEMPOR incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum
    89feb84d (Albert So      2018-03-01 00:54:03 +0000  8)
    eb06faed (Juni Mukherjee 2018-03-01 19:53:23 +0000  9) Annotates each line in the given file with information from the revision which last modified the line. Optionally, start annotating from the given revision.
    eb06faed (Juni Mukherjee 2018-03-01 19:53:23 +0000 10)
    548dabed (Juni Mukherjee 2018-03-01 19:55:15 +0000 11) Creating a line to support documentation needs for git blame.
    548dabed (Juni Mukherjee 2018-03-01 19:55:15 +0000 12)
    548dabed (Juni Mukherjee 2018-03-01 19:55:15 +0000 13) Also, it is important to have a few of these commits to clearly reflect the who, the what and the when. This will help Kev get good screenshots when he runs the git blame on this README.
```

这是 README.md 文件前 13 行的示例。为了更好地理解这些输出，让我们逐行分析。下表显示的是第 3 行的内容，表中的列表示该列的内容。

| Id       |  Author   |         Timestamp         | Line Number |                         Line Content                         |
| :------- | :-------: | :-----------------------: | :---------: | :----------------------------------------------------------: |
| 89feb84d | Albert So | 2018-03-01 00:54:03 +0000 |      3      | This repository is an example of a project with multiple contributors making commits. |

如果我们查看一下`blame`输出列表，就会发现一些问题。列表中有三位作者。除了项目维护者 Kev Zettler，还有 Albert So 和 Juni Mukherjee。作者通常是 `git blame` 输出中最有价值的部分。时间戳列也很有帮助。行内容列则显示了改动的内容。

### 常见选项

```
git blame -L 1,5 README.md
```

`-L`选项将把输出限制在所要求的行范围内。在这里，我们将输出限制在第 1 行至第 5 行。

```
git blame -e README.md
```

`-e`选项显示的是作者的电子邮件地址，而不是用户名。

```
git blame -w README.md
```

`-w`选项会忽略空白处的改动。如果前作者修改了文件的间距，将制表符换成了空格或添加了新行，那么很不幸，git blame 的输出就会因为显示了这些改动而变得模糊不清。

```
git blame -M README.md
```

`-M`选项可检测同一文件中被移动或复制的行。这将报告该行的原作者，而不是移动或复制该行的最后一位作者。

```
git blame -C README.md
```

`-C`选项可检测从其他文件中移动或复制的行。这将报告该行的原作者，而不是移动或复制该行的最后一位作者。

### Git blame vs git log

虽然 `git blame` 会显示修改行最后修改的作者，但很多时候您还是想知道一行最初添加的时间。要做到这一点，使用 `git blame` 可能比较麻烦。这需要结合使用 -w、-C 和 -M 选项。使用 git log 命令会方便得多。

要列出添加或修改特定代码的所有原始提交，请执行带 `-S` 选项的 `git log` 命令。在 -S 选项后加上您要查找的代码。让我们以上面 README 输出中的一行为例。让我们以 README 输出第 12 行中的 "CSS3D and WebGL renderers. "为例。

```bash
$ git log -S"CSS3D and WebGL renderers." --pretty=format:'%h %an %ad %s'
    e339d3c85 Mario Schuettel Tue Oct 13 16:51:06 2015 +0200 reverted README.md to original content
    509c2cc35 Daniel Tue Sep 8 13:56:14 2015 +0200 Updated README
    cb20237cc Mr.doob Mon Dec 31 00:22:36 2012 +0100 Removed DOMRenderer. Now with the CSS3DRenderer it has become irrelevant.
```

输出结果显示，README 中的内容被 3 位不同的作者添加或修改了 3 次。最初是由 Mr.doob 在 cb20237cc 提交中添加的。在本例中，git log 还预置了 --pretty-format 选项。该选项将 git log 的默认输出格式转换为与 git log 格式一致的格式。有关使用和配置选项的更多信息，请访问 git log 页面。

### Summary

`git blame` 命令用于逐行检查文件内容，查看每一行的最后修改时间和作者。`git blame` 的输出格式可以通过各种命令行选项来改变。在线 Git 托管解决方案（如 Bitbucket）提供了责备视图，与使用命令行的 `git blame` 相比，它能提供更好的用户体验。`git log` 命令也有一些类似的 blame 功能，如需了解更多，请访问 git log 概述页面。

-------

# 撤消提交和更改

在本节中，我们将讨论可用的 Git "撤销 "策略和命令。首先需要注意的是，Git 并没有像文字处理程序那样的传统 "撤销 "系统。避免将 Git 操作映射到任何传统的 "撤消 "思维中会有所裨益。此外，Git 有自己的 "撤消 "操作术语，在讨论中最好加以利用。这些术语包括重置（`reset`）、还原（`revert`）、签出（`checkout`）、清理（`clean`）等。

一个有趣的比喻是把 Git 看成一个时间轴管理工具。提交是项目历史时间轴上某个时间点或兴趣点的快照。此外，还可以通过使用分支来管理多条时间线。在 Git 中进行 "撤消 "时，通常是向后移动，或移动到另一条没有发生错误的时间线上。

本教程提供了处理项目历史版本所需的全部技能。首先，它将向你展示如何探索旧的提交，然后解释在项目历史中恢复公开提交与在本地机器上重置未发布的修改之间的区别。

## 查找丢失的内容：Reviewing old commits

任何版本控制系统背后的理念都是存储项目的 "安全 "副本，这样你就不必担心会不可挽回地破坏你的代码库。一旦建立了项目提交历史，就可以查看和重温历史中的任何提交。`git log`命令是查看 Git 仓库历史记录的最佳工具之一。在下面的例子中，我们使用`git log`获取了一个流行的开源图形库的最新提交列表。

```bash
git log --oneline
e2f9a78fe Replaced FlyControls with OrbitControls
d35ce0178 Editor: Shortcuts panel Safari support.
9dbe8d0cf Editor: Sidebar.Controls to Sidebar.Settings.Shortcuts. Clean up.
05c5288fc Merge pull request #12612 from TyLindberg/editor-controls-panel
0d8b6e74b Merge pull request #12805 from harto/patch-1
23b20c22e Merge pull request #12801 from gam0022/improve-raymarching-example-v2
fe78029f1 Fix typo in documentation
7ce43c448 Merge pull request #12794 from WestLangley/dev-x
17452bb93 Merge pull request #12778 from OndrejSpanel/unitTestFixes
b5c1b5c70 Merge pull request #12799 from dhritzkiv/patch-21
1b48ff4d2 Updated builds.
88adbcdf6 WebVRManager: Clean up.
2720fbb08 Merge pull request #12803 from dmarcos/parentPoseObject
9ed629301 Check parent of poseObject instead of camera
219f3eb13 Update GLTFLoader.js
15f13bb3c Update GLTFLoader.js
6d9c22a3b Update uniforms only when onWindowResize
881b25b58 Update ProjectionMatrix on change aspect
```

每个提交（`commit`）都有一个唯一的`SHA-1`哈希值。这些 ID 用于浏览提交时间线和重访提交。默认情况下，`git log` 只显示当前选定分支的提交。您要找的提交完全有可能在另一个分支上。您可以执行 `git log --branches=*`，查看所有其他分支的所有提交。`git branch` 命令用于查看和访问其他分支。调用 `git branch -a` 命令将返回所有已知分支名称的列表。然后可以使用`git log`.

当找到要访问的历史节点的某个提交引用时，就可以使用`git checkout`命令访问该提交。Git 签出是将这些保存的快照 "加载 "到开发机器上的简单方法。在正常开发过程中，`HEAD`通常指向主分支或其他本地分支，但当你签出之前的提交时，`HEAD`不再指向分支，而是直接指向某个提交。这就是所谓的 "分离 HEAD "状态，可视化如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-detached-head.png" style="zoom:50%;" />

## 查看旧版本

此示例假设您已经开始开发一个疯狂的实验，但您不确定是否要保留它。为了帮助您做出决定，您需要在开始实验之前查看项目的状态。首先，您需要找到您想要查看的修订版本的 ID。

```bash
git log --oneline
```

假设您的项目历史记录如下所示：

```bash
b7119f2 Continue doing crazy things
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```

您可以使用 `git checkout` 查看 "对 hello.txt 做一些导入改动 "的提交，如下所示：

```
git checkout a1e8fb5
```

这将使你的工作目录与 a1e8fb5 提交时的状态完全一致。你可以查看文件、编译项目、运行测试，甚至编辑文件，而不必担心丢失项目的当前状态。在这里所做的一切都不会保存到版本库中。要继续开发，就必须回到项目的 "当前 "状态：

```
git checkout main
```

这假定您正在默认的主分支上进行开发。回到主分支后，您可以使用 `git revert` 或 `git reset` 撤销任何不希望的改动。

## 撤销已提交的快照

从技术上讲，"撤销 "提交有几种不同的策略。下面的示例假定我们的提交历史如下：

```bash
git log --oneline
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```

我们将重点撤销 872fa7e Try something crazy 提交。也许事情有点太疯狂了。

### 如何使用 git checkout 撤销提交

使用 `git checkout` 命令，我们可以签出上一个提交（a1e8fb5），使仓库处于疯狂提交之前的状态。签出特定提交将使版本库处于 "分离 HEAD "状态。这意味着你不再在任何分支上工作。在分离状态下，当你将分支改回已建立的分支时，你所做的任何新提交都将成为孤儿。孤儿提交会被 Git 的垃圾回收器删除。垃圾回收器会按照设定的时间间隔运行，并永久销毁已成为孤儿的提交。为了防止被垃圾回收器回收的提交，我们需要确保自己在一个分支上。

在分离的 HEAD 状态下，我们可以执行 `git checkout -b new_branch_without_crazy_commit`。这将创建一个名为 `new_branch_without_crazy_commit` 的新分支，并切换到该状态。现在，该 repo 已进入新的历史时间线，其中的 872fa7e 提交已不复存在。此时，我们可以继续在这个新分支上工作，而 872fa7e 提交已不复存在，并将其视为 "撤消"。不幸的是，如果你需要之前的分支，也许它是你的主分支，那么这种撤消策略就不合适了。让我们看看其他一些 "撤销 "策略。如需了解更多信息和示例，请参阅我们的 git check 深入讨论。

### 如何用 git revert 撤销公开提交

让我们回到最初的提交历史示例。包含 872fa7e 提交的历史。这一次，让我们试试 "撤销"（undo）。如果我们执行 `git revert HEAD`，Git 会创建一个与上次提交相反的新提交。这就在当前分支历史中添加了一个新的提交，现在看起来就像

```bash
git log --oneline
e2f9a78 Revert "Try something crazy"
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```

至此，我们在技术上再次 "撤销 "了 872fa7e 提交。虽然 872fa7e 仍存在于历史中，但新的 e2f9a78 提交是 872fa7e 变动的反转。与之前的签出策略不同，我们可以继续使用同一分支。这个解决方案的撤消效果令人满意。这是处理公共共享源的理想 "撤销 "方法。如果您需要保留精选且最少的 Git 历史记录，则此策略可能无法令人满意。

### 如何使用 git reset 撤消提交

`git reset` 是一个用途广泛、功能多样的命令。如果我们调用 `git reset --hard a1e8fb5`，提交历史将重置为指定的提交。现在用`git log`查看提交历史会如下所示

```bash
git log --oneline
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```

交历史中。此时，我们可以继续工作并创建新的提交，就好像这些 "疯狂 "的提交从未发生过一样。这种撤销修改的方法对历史记录的影响最为干净。`reset` 对于本地变更来说很好，但在使用共享远程版本库时却会增加复杂性。如果我们有一个推送了 872fa7e 提交的共享远程仓库，而我们试图用 git 推送一个我们已经重置了历史的分支，Git 会捕捉到这一点并抛出一个错误。Git 会认为推送的分支因为缺少提交而不是最新的。在这种情况下，`git revert` 应该是首选的撤销方法。

### 撤销最后一次提交

在上一节中，我们讨论了撤销提交的不同策略。这些策略同样适用于最近的提交。不过在某些情况下，你可能并不需要删除或重置最后一次提交。也许只是提交时间过早。在这种情况下，您可以修改最近的提交。一旦你在工作目录中做了更多改动，并用 `git add` 将它们暂存以备提交，你就可以执行 `git commit --amend`。这会让 Git 打开配置好的系统编辑器，让你修改上次的提交信息。新的改动将被添加到修改后的提交中。

### 撤销公开更改

当在一个拥有远程仓库的团队中工作时，在撤销更改时需要格外注意。`Git reset` 通常被视为一种 "本地 "撤销方法。在撤销私有分支的变更时，应使用重置。这样可以安全地将提交与其他开发者正在使用的分支隔离开来。如果在共享分支上执行重置，然后用 git push 远程推送该分支，就会出现问题。在这种情况下，Git 会阻止推送，抱怨被推送的分支与远程分支不同步，因为缺少提交。

撤销共享版本库的首选方法是 `git revert`。`revert`比`reset`更安全，因为它不会从共享版本库历史中删除任何提交。还原会保留要撤销的提交，并创建一个新的提交来反转不想要的提交。这种方法对于共享远程协作更安全，因为远程开发人员可以拉取分支，然后接收新的还原提交，从而撤销不想要的提交。

## Summary

我们介绍了许多在 Git 中撤销的高级策略。重要的是要记住，在 Git 项目中 "撤销 "的方法不止一种。本页讨论的大部分内容都涉及更深层次的话题，而这些话题在相关 Git 命令的具体页面中会有更详尽的解释。最常用的 "撤销 "工具是 `git checkout`、`git revert` 和 `git reset`。需要记住的要点有：

- 修改一旦提交，通常就是永久性的
- 使用 `git checkout` 查看历史提交
- `git revert` 是撤销共享版本库的公共修改的最佳工具
- `git reset` 是撤销本地私有修改的最佳工具

除了主要的撤消命令，我们还学习了其他 Git 工具：用于查找丢失提交的 git log、用于撤消未提交改动的 git clean、用于修改暂存索引的 git add。

每个命令都有自己的深入文档。要进一步了解此处提到的某个命令，请访问相应链接。

## git clean

 在本节中，我们将重点详细讨论 `git clean` 命令。`git clean` 在某种程度上是一个 "撤销 "命令。`git clean` 可以说是其他命令（如 `git reset` 和 `git checkout`）的补充。这些命令针对的是之前添加到 Git 跟踪索引中的文件，而 `git clean` 命令针对的是未被跟踪的文件。未跟踪文件是指在您的 repo 工作目录中创建的、但尚未使用 git add 命令添加到版本库跟踪索引中的文件。

为了更好地展示跟踪文件和非跟踪文件的区别，请看下面的命令行示例:

```bash
$ mkdir git_clean_test
$ cd git_clean_test/
$ git init .
Initialized empty Git repository in /Users/kev/code/git_clean_test/.git/
$ echo "tracked" > ./tracked_file
$ git add ./tracked_file
$ echo "untracked" > ./untracked_file
$ mkdir ./untracked_dir && touch ./untracked_dir/file
$ git status
On branch master

Initial commit

Changes to be committed: (use "git rm --cached <file>..." to unstage)

new file: tracked_file

Untracked files: (use "git add <file>..." to include in what will be committed) untracked_dir/ untracked_file
```

该示例在 git_clean_test 目录下创建了一个新的 Git 仓库。然后创建了一个跟踪文件（tracked_file），并将其添加到 Git 索引中，此外还创建了一个未跟踪文件（untracked_file）和一个未跟踪目录（untracked_dir）。然后，示例调用了 git status，输出显示了 Git 内部已跟踪和未跟踪的变更状态。有了这样的仓库状态，我们就可以执行 `git clean` 命令来演示其预期目的了。

```bash
$ git clean fatal: clean.requireForce defaults to true and neither -i, -n, nor -f given; refusing to clean
```

此时，执行默认的 `git clean` 命令可能会产生致命错误。上面的例子演示了这种情况。默认情况下，Git 的全局配置要求 `git clean` 必须通过 "`force` "选项才能启动。这是一个重要的安全机制。当最后执行时，`git clean` 是不可撤销的。完全执行后，`git clean` 会硬性删除文件系统，类似于执行命令行 `rm` 工具。运行前请确保您真的想删除未跟踪的文件。

### 常用选项和用法

鉴于前面对 git 清理默认行为和注意事项的解释，下面的内容将演示各种 git 清理用例及其操作所需的命令行选项。

```
-n
```

-n 选项将执行 git clean 的“试运行”。这会显示哪些文件将被移除，但不会真正移除。最好的做法是先执行一次`git clean` "试运行 "。我们可以在之前创建的演示版本中演示这个选项。

```
$ git clean -n
Would remove untracked_file
```

输出结果告诉我们，当执行 `git clean` 命令时，untracked_file 将被移除。注意这里的输出没有报告 untracked_dir。默认情况下，git clean 不会对目录进行递归操作。这是防止意外永久删除的另一种安全机制。

```
-f or --force
```

强制选项会启动实际删除当前目录中未跟踪文件的操作。除非 clean.requireForce 配置选项设置为 false，否则强制是必须的。这不会删除 .gitignore 指定的未跟踪文件夹或文件。现在让我们在示例仓库中执行一次实时的 git clean。

```
$ git clean -f 
Removing untracked_file
```

该命令将输出已删除的文件。这里可以看到 untracked_file 已被删除。此时执行 git status 或进行 ls 将显示 untracked_file 已被删除，并且无处可寻。默认情况下，`git clean -`f 会对当前目录下所有未被跟踪的文件执行操作。此外，还可以通过 `-f` 选项传递 `< path >` 值来删除特定文件。

```bash
git clean -f <path>
-d include directories
```

`-d`选项会告诉`git clean`您还想移除任何未跟踪的目录，默认情况下它会忽略这些目录。我们可以在之前的例子中添加 -d 选项：

```bash
$ git clean -dn
Would remove untracked_dir/
$ git clean -df
Removing untracked_dir/
```

在这里，我们使用 -dn 组合执行了一次 "试运行"，输出结果是 untracked_dir 将被移除。然后，我们执行了强制清理，得到的输出结果是 untracked_dir 已被删除。

```
-x force removal of ignored files
```

一种常见的软件发布模式是建立一个未提交到版本库跟踪索引的构建或发布目录。构建目录将包含从已提交源代码生成的短暂构建工程。该构建目录通常会添加到版本库的 `.gitignore` 文件中。清理该目录中的其他未跟踪文件也很方便。`-x` 选项会告诉 `git clean` 也包含任何被忽略的文件。与之前的 git clean 一样，在最终删除之前，最好先执行一次 "试运行"。-x 选项将作用于所有被忽略的文件，而不仅仅是项目构建时特定的文件。比如 ./.idea IDE 配置文件。

```
git clean -xf
```

与 `-d` 选项一样，`-x` 可以与其他选项一起传递和组合。本例演示了与 `-f` 的组合，可以移除当前目录中未跟踪的文件以及 Git 忽略的文件。

### 交互模式或 git clean 交互式

除了我们已经演示过的临时命令行执行外，git clean 还有一个 "交互式 "模式，可以通过 `-i` 选项启动。让我们重温一下本文导言中的示例仓库。在初始状态下，我们将启动一个交互式的清理会话。

```bash
$ git clean -di
Would remove the following items:
  untracked_dir/  untracked_file
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit                 6: help
What now>
```

我们使用 -d 选项启动了交互式会话，因此它也会作用于 untracked_dir。交互模式将显示 "What now> "提示，要求对未跟踪文件执行命令。命令本身很容易解释。我们将从命令 6：帮助开始，按随机顺序简要介绍每条命令。选择命令 6 将进一步解释其他命令：

```bash
What now> 6
clean               - start cleaning
filter by pattern   - exclude items from deletion
select by numbers   - select items to be deleted by numbers
ask each            - confirm each deletion (like "rm -i")
quit                - stop cleaning
help                - this screen
?                   - help for prompt selection
```

```
5: quit
```

直截了当，退出互动会话。

```
1: clean
```

将删除指定的项目。如果此时执行 1: `clean`，`untracked_dir/ untracked_file` 将被删除

```
4: ask each
```

会遍历每个未跟踪的文件，并显示 "`Y/N` "提示是否删除。如下所示

```bash
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit                 6: help
What now> 4
Remove untracked_dir/ [y/N]? N
Remove untracked_file [y/N]? N
```

```
2: filter by pattern
```

将显示额外提示，输入用于过滤未跟踪文件列表的信息。

```bash
Would remove the following items:
  untracked_dir/  untracked_file
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit                 6: help
What now> 2
  untracked_dir/  untracked_file
Input ignore patterns>> *_file
  untracked_dir/
```

在这里，我们输入 `*_file` 通配符模式，然后将未跟踪文件列表限制为 untracked_dir。

```
3: select by numbers
```

与命令 2 类似，命令 3 的作用也是完善未跟踪文件名列表。交互会话将提示输入与未跟踪文件名相对应的数字。

```bash
Would remove the following items:
  untracked_dir/  untracked_file
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit                 6: help
What now> 3
    1: untracked_dir/    2: untracked_file
Select items to delete>> 2
    1: untracked_dir/  * 2: untracked_file
Select items to delete>>
Would remove the following item:
  untracked_file
*** Commands ***
    1: clean                2: filter by pattern    3: select by numbers    4: ask each             5: quit                 6: help
```

### Summary

概括地说，git clean 是一种方便的方法，用于删除 repo 工作目录中未被跟踪的文件。未跟踪文件是指那些在仓库目录中，但尚未被 git add 添加到仓库索引中的文件。总的来说，git clean 的效果可以通过使用 git status 和操作系统原生的删除工具来实现。Git clean 可与 git reset 同时使用，以完全撤销版本库中的任何新增和提交。

## git revert

git revert 命令可被视为 "撤销 "类型的命令，但它并不是传统的撤销操作。它不是从项目历史中删除提交，而是找出如何反转提交带来的改动，并用反转后的内容追加新的提交。这可以防止 Git 丢失历史记录，而这对于保持修订历史的完整性和可靠的协作非常重要。

当你想从项目历史中应用逆向提交时，就应该使用还原。例如，如果你在追踪一个 Bug 时发现它是由一次提交引入的，这就很有用了。你可以使用 git revert 自动完成所有这些工作，而不用手动去修复它，再提交一个新的快照。

### How it works

git revert 命令用于撤销对版本库提交历史的修改。其他 "撤消 "命令，如 git checkout 和 git reset，会将 HEAD 和分支的 ref 指针移动到指定的提交。git revert 也需要指定的提交，但 git revert 不会将 ref 指针移动到该提交。

还原操作会使用指定的提交，反转该提交的改动，并创建一个新的 "还原提交"。然后 ref 指针就会更新，指向新的 revert 提交，使其成为分支的顶端。

为了演示，让我们使用下面的命令行示例创建一个 repo：

```bash
$ mkdir git_revert_test
$ cd git_revert_test/
$ git init .
Initialized empty Git repository in /git_revert_test/.git/
$ touch demo_file
$ git add demo_file
$ git commit -am"initial commit"
[main (root-commit) 299b15f] initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 demo_file
$ echo "initial content" >> demo_file
$ git commit -am"add new content to demo file"
[main 3602d88] add new content to demo file
n 1 file changed, 1 insertion(+)
$ echo "prepended line content" >> demo_file
$ git commit -am"prepend content to demo file"
[main 86bb32e] prepend content to demo file
 1 file changed, 1 insertion(+)
$ git log --oneline
86bb32e prepend content to demo file
3602d88 add new content to demo file
299b15f initial commit
```

在这里，我们在名为 git_revert_test 的新建目录中初始化了一个 repo。我们对 repo 进行了 3 次提交，其中添加了一个文件 demo_file，并对其内容修改了两次。在仓库设置过程的最后，我们调用了 git log 来显示提交历史，总共显示了 3 次提交。这样，我们就可以启动 git revert 了。

```bash
$ git revert HEAD
[main b9cd081] Revert "prepend content to demo file" 1 file changed, 1 deletion(-)
```

git revert 希望传递一个提交引用，否则不会执行。这里我们传入了 HEAD。这将还原最新的提交。这与还原 3602d8815dbfa78cd37cd4d189552764b5e96c58 提交的行为相同。与合并类似，还原将创建一个新的提交，并打开配置的系统编辑器，提示输入新的提交信息。输入并保存提交信息后，Git 将恢复运行。现在我们可以用 git 日志查看 repo 的状态，发现在之前的日志中添加了新的提交：

```bash
$ git log --oneline 1061e79 Revert "prepend content to demo file" 86bb32e prepend content to demo file 3602d88 add new content to demo file 299b15f initial commit
```

请注意，第 3 次提交在还原后仍在项目历史中。git revert 并没有删除它，而是添加了一个新的提交来撤销它的改动。因此，第 2 次和第 4 次提交代表的是完全相同的代码库，而第 3 次提交仍保留在历史记录中，以备日后回溯。

### 常见选项

```
-e
--edit
```

这是默认选项，无需指定。该选项将打开配置的系统编辑器，并提示你在提交还原之前编辑提交信息。

```
--no-edit
```

这与 `-e` 选项相反。revert 不会打开编辑器。

```
-n
--no-commit
```

传递此选项将阻止 git revert 创建一个与目标提交相反的新提交。该选项不会创建新的提交，而是将反转的改动添加到暂存索引和工作目录中。这些是 Git 用来管理仓库状态的其他树。更多信息，请访问 git reset页面。

### Resetting vs. reverting

重要的是要明白，git revert 只撤销一次提交，而不是通过删除所有后续提交来 "还原 "到项目之前的状态。在 Git 中，这被称为重置，而非还原。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-reverting-resetting.png" style="zoom:50%;" />

与`resetting`相比，`Reverting`有两个重要优势。首先，它不会改变项目历史，因此对于已经发布到共享仓库的提交来说，这是一个 "安全 "的操作。关于更改共享历史为何是危险的，请参阅 git reset 页面。

其次，git revert 可以针对历史中任意点的单个提交，而 git reset 只能从当前提交向后操作。举例来说，如果想用 git reset来撤消一次旧提交，就必须移除目标提交之后的所有提交，移除之后再重新提交所有后续提交。不用说，这不是一个优雅的撤消解决方案。关于 git revert 与其他 "撤消 "命令的区别，请参阅重置、签出和回退。 

### Summary

git revert 命令是一种向前移动的撤销操作，它提供了一种安全的撤销修改的方法。它不会删除或销毁提交历史中的提交，而是创建一个新的提交来反转指定的改动。就丢失工作而言，git revert 是比 git reset 更安全的选择。

## git reset

`git reset`命令是一个复杂的多功能工具，用于撤销更改。它有三种主要的调用形式。这些形式与命令行参数`--soft`, `--mixed`, `--hard`相对应。这三个参数分别对应 Git 的三个内部状态管理机制：提交树（HEAD）、暂存索引和工作目录。

### git reset & git 三棵树

要正确理解 `git reset`的用法，我们必须先了解 Git 的内部状态管理系统。这些机制有时被称为 Git 的 "三棵树"。三棵树 "可能名不副实，因为它们并非严格意义上的传统树形数据结构。不过，它们是基于节点和指针的数据结构，Git 用它来跟踪编辑的时间轴。演示这些机制的最佳方式是在版本库中创建一个变更集，并通过三棵树来跟踪它。

首先，我们将使用下面的命令创建一个新的版本库：

```bash
$ mkdir git_reset_test
$ cd git_reset_test/
$ git init .
Initialized empty Git repository in /git_reset_test/.git/
$ touch reset_lifecycle_file
$ git add reset_lifecycle_file
$ git commit -m"initial commit"
[main (root-commit) d386d86] initial commit
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 reset_lifecycle_file
```

上面的示例代码创建了一个新的 git 仓库，其中只有一个空文件 `reset_lifecycle_file`。此时，示例仓库中只有一次提交 (d386d86) 是在添加 reset_lifecycle_file 时提交的。

### The working directory

我们要查看的第一个树是 "工作目录"。这棵树与本地文件系统同步，代表着对文件和目录内容所做的即时更改。

```bash
$ echo 'hello git reset' > reset_lifecycle_file
 $ git status 
 On branch main
 Changes not staged for commit: 
 (use "git add ..." to update what will be committed) 
 (use "git checkout -- ..." to discard changes in working directory) 
 modified: reset_lifecycle_file
```

在我们的演示版本库中，我们修改了 reset_lifecycle_file 并添加了一些内容。调用 git status 可以看到 Git 已经知道了文件的改动。这些改动目前是第一个树 "工作目录 "的一部分。`git status` 可以用来显示工作目录的改动。它们将以红色显示，并带有 "modified"前缀。

### Staging index

接下来是 "暂存索引 "树。这棵树会跟踪工作目录中的变更，这些变更已通过 git add 进行了暂存，并将存储在下一次提交中。这棵树是一个复杂的内部缓存机制。git 通常会尽量向用户隐藏暂存索引的执行细节。

接下来是 "暂存索引 "树。这棵树会跟踪工作目录中的变更，这些变更已通过 git add 进行了推广，并将存储在下一次提交中。这棵树是一个复杂的内部缓存机制。Git 通常会尽量向用户隐藏暂存索引的执行细节。

要准确查看暂存索引的状态，我们必须使用一个鲜为人知的 Git 命令 `git ls-files`。`git ls-files` 命令本质上是一个用于检查暂存索引树状态的调试工具。

```bahh
git ls-files -s
100644 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 0   reset_lifecycle_file
```

这里我们使用了 `-s` 或 `--stage` 选项来执行 `git ls-files`。不使用 `-s` 选项时，git ls-files 的输出只是一个当前索引中的文件名和路径列表。-s选项会显示暂存索引中文件的附加元数据。这些元数据包括暂存内容的模式位、对象名称和阶段编号。在这里，我们感兴趣的是对象名称的第二个值（d7d77c1b04b5edd5acfc85de0b592449e5303770）。这是标准的 Git 对象 SHA-1 哈希值。它是文件内容的哈希值。提交历史存储了自己的 SHA 对象，用于识别提交和引用的指针，暂存索引也有自己的 SHA 对象，用于跟踪索引中文件的版本。

接下来，我们将把修改后的 reset_lifecycle_file 加到暂存索引中。

```bash
$ git add reset_lifecycle_file 

 
$ git status 

 
On branch main Changes to be committed: 

 
(use "git reset HEAD ..." to unstage) 

 
modified: reset_lifecycle_file
```

在这里，我们调用了 git add reset_lifecycle_file，将文件添加到了暂存索引中。现在调用 git status，reset_lifecycle_file 在 "Changes to be committed"下显示为绿色。需要注意的是，git status 并不能真实反映暂存索引。git status 命令输出显示的是提交历史和暂存索引之间的变化。让我们来看看暂存索引的内容。

```
$ git ls-files -s 100644 d7d77c1b04b5edd5acfc85de0b592449e5303770 0 reset_lifecycle_file
```

我们可以看到，reset_lifecycle_file 的对象 SHA 哈希值已从 e69de29bb2d1d6434b8b29ae775ad8c2e48c5391 更新为 d7d77c1b04b5edd5acfc85de0b592449e5303770。

### Commit history

最后一棵树是提交历史。git commit 命令会将修改添加到永久快照中，该快照保存在 "提交历史 "中。该快照还包括提交时暂存索引的状态。

```bash
$ git commit -am"update content of reset_lifecycle_file"
[main dc67808] update content of reset_lifecycle_file
1 file changed, 1 insertion(+)
$ git status
On branch main
nothing to commit, working tree clean
```

在这里，我们创建了一个新提交，其信息为 "更新 resetlifecyclefile 的内容"。该变更集已被添加到提交历史中。此时调用 git status 会显示没有任何待处理的修改。执行 git log 会显示提交历史。现在，我们已经在三棵树中跟踪了这个变更集，可以开始使用 git reset了。

### How it works

从表面上看，git reset 的行为与 git checkout 类似。git checkout 只对 HEAD ref 指针进行操作，而 git reset 则会移动 HEAD ref 指针和当前分支 ref 指针。为了更好地演示这一行为，请看下面的例子：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-head-mian.png" style="zoom:50%;" />

这个例子演示了主分支上的一系列提交。现在让我们执行并比较一下 `git checkout b` 和 `git reset b`。

**git checkout b**

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-checkout-b.png" style="zoom:50%;" />

通过 git checkout，Main引用仍指向 d。HEAD 引用已被移动，现在指向提交 b。

**git reset b**

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-reset-b.png" style="zoom:50%;" />

相比之下，git reset 会把 HEAD 和 Main 分支的引用都移到指定的提交上。

除了更新commit ref 指针，git 重置还会修改三棵树的状态。ref 指针的修改总是会发生，而且是对第三棵树，即提交树的更新。命令行参数--soft、--mixed 和--hard 会指示如何修改暂存索引树和工作目录树。

### 主要选项

git reset 的默认参数是 `--mixed HEAD`。这意味着执行 `git reset` 等同于执行 `git reset --mixed HEAD`。在这种形式中，HEAD 就是指定的提交。可以使用任何 Git SHA-1 commit哈希值来代替 HEAD。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-reset-models.png" style="zoom:50%;" />

#### '--hard

这是最直接、最危险和最常用的选项。当使用 `--hard` 时，提交历史 ref 指针会更新到指定的提交。然后，暂存索引和工作目录会重置为与指定提交一致。任何之前对暂存索引和工作目录的待定更改都会被重置，以匹配提交树的状态。这意味着暂存索引和工作目录中的任何待处理工作都将丢失。

为了演示这一点，让我们继续之前建立的三棵树示例 repo。首先，让我们对 repo 做一些修改。在示例 repo 中执行以下命令：

```bash
$ echo 'new file content' > new_file
$ git add new_file
$ echo 'changed content' >> reset_lifecycle_file
```

这些命令创建了一个名为 new_file 的新文件，并将其添加到 repo 中。此外，reset_lifecycle_file 的内容也会被修改。有了这些改动，现在让我们用 git status 查看一下 repo 的状态。

```bash
$ git status
On branch main
Changes to be committed:
   (use "git reset HEAD ..." to unstage)

new file: new_file

Changes not staged for commit:
   (use "git add ..." to update what will be committed)
   (use "git checkout -- ..." to discard changes in working directory)

modified: reset_lifecycle_file
```

在这里，我们调用了 git add reset_lifecycle_file，将文件添加到了暂存索引中。现在调用 git status，reset_lifecycle_file 在 "Changes to be committed "下显示为绿色。

需要注意的是，git status 并不能真实反映暂存索引。git status 命令输出显示的是提交历史和暂存索引之间的变化。让我们来看看暂存索引的内容。

```
$ git ls-files -s
100644 8e66654a5477b1bf4765946147c49509a431f963 0 new_file
100644 d7d77c1b04b5edd5acfc85de0b592449e5303770 0 reset_lifecycle_file
```

我们可以看到 new_file 已被添加到索引中。我们对 reset_lifecycle_file 进行了更新，但暂存索引的 SHA (d7d77c1b04b5edd5acfc85de0b592449e5303770) 仍保持不变。这是意料之中的事情，因为我们并没有使用 git add 将这些更改推广到暂存索引。这些改动存在于工作目录中。

现在，让我们执行 git reset --hard 并检查仓库的新状态。

```bash
$ git reset --hard
HEAD is now at dc67808 update content of reset_lifecycle_file
$ git status
On branch main
nothing to commit, working tree clean
$ git ls-files -s
100644 d7d77c1b04b5edd5acfc85de0b592449e5303770 0 reset_lifecycle_file
```

在这里，我们使用 `--hard` 选项执行了一次 "硬重置"。Git 的输出显示 HEAD 指向了最新提交 dc67808。

接下来，我们用 git status 检查 repo 的状态。Git 显示没有待处理的修改。我们还检查了暂存索引的状态，发现它已被重置到 new_file 被添加之前的位置。我们对 reset_lifecycle_file 所做的修改和 new_file 的添加都被销毁了。这种数据丢失是无法挽回的，因此必须引起注意。

#### '--mixed

这是默认运行模式。ref 指针会被更新。暂存索引会重置为指定提交时的状态。任何从暂存索引中撤销的更改都会被移到工作目录中。让我们继续。

```bash
$ echo 'new file content' > new_file
$ git add new_file
$ echo 'append content' >> reset_lifecycle_file
$ git add reset_lifecycle_file
$ git status
On branch main
Changes to be committed:
    (use "git reset HEAD ..." to unstage)

new file: new_file
modified: reset_lifecycle_file


$ git ls-files -s
100644 8e66654a5477b1bf4765946147c49509a431f963 0 new_file
100644 7ab362db063f9e9426901092c00a3394b4bec53d 0 reset_lifecycle_file
```

在上面的示例中，我们对版本库做了一些修改。我们添加了一个 new_file，并修改了 reset_lifecycle_file 的内容。这些改动会通过 git add 应用到暂存索引中。有了这样的版本库，我们现在就要执行重置。

```bash
$ git reset --mixed
$ git status
On branch main
Changes not staged for commit:
    (use "git add ..." to update what will be committed)
    (use "git checkout -- ..." to discard changes in working directory)

modified: reset_lifecycle_file

Untracked files:
    (use "git add ..." to include in what will be committed)

new_file


no changes added to commit (use "git add" and/or "git commit -a")
$ git ls-files -s
100644 d7d77c1b04b5edd5acfc85de0b592449e5303770 0 reset_lifecycle_file
```

这里我们执行了一次 "mixed reset"。重申一遍，--mixed 是默认模式，与执行 git reset的效果相同。查看 git status 和 git ls-files 的输出，可以发现暂存索引已被重置到 reset_lifecycle_file 是索引中唯一文件的状态。reset_lifecycle_file 的 SHA 对象也被重置为之前的版本。

这里需要注意的是，git status 显示 reset_lifecycle_file 有修改，而且还有一个未跟踪的文件：new_file。这是显式`--mixed`行为。暂存索引已被重置，待处理的修改已被移入工作目录。

#### '--soft

当传递--soft 参数时，将更新 ref 指针，重置到此为止。暂存索引和工作目录则保持不变。这种行为很难清晰演示。让我们继续演示 repo，为软重置做好准备。

```bash
$ git add reset_lifecycle_file 

 
$ git ls-files -s 

 
100644 67cc52710639e5da6b515416fd779d0741e3762e 0 reset_lifecycle_file 

 
$ git status 

 
On branch main

 
Changes to be committed: 

 
(use "git reset HEAD ..." to unstage) 

 
modified: reset_lifecycle_file 

 
Untracked files: 

 
(use "git add ..." to include in what will be committed) 

 
new_file
```

在这里，我们再次使用 git add 将修改后的 reset_lifecycle_file 添加到暂存索引中。我们通过 `git ls-files` 的输出确认索引已经更新。git status 的输出现在显示绿色的 "Changes to be committed"。之前例子中的 new_file 作为一个未跟踪文件漂浮在工作目录中。让我们快速执行 `rm new_file` 删除该文件，因为在接下来的示例中我们不需要它了。

在版本库处于这种状态的情况下，我们现在执行软重置。

```bash
$ git reset --soft
$ git status
On branch main
Changes to be committed:
    (use "git reset HEAD ..." to unstage)

modified: reset_lifecycle_file
$ git ls-files -s
100644 67cc52710639e5da6b515416fd779d0741e3762e 0 reset_lifecycle_file
```

我们执行了 "soft reset"。使用 git status 和 git ls-files 检查 repo 的状态显示，没有任何变化。这是意料之中的。软重置只会重置提交历史。默认情况下，git 重置的目标提交是 HEAD。由于我们的提交历史已经在 HEAD 上了，而我们又隐式重置到了 HEAD，所以什么也没发生。

为了更好地理解和利用`--soft`，我们需要一个非 HEAD 的目标提交。我们有 reset_lifecycle_file 在暂存索引中等待。让我们创建一个新的提交。

```bash
$ git commit -m"prepend content to reset_lifecycle_file"
```

此时，我们的 repo 应该有三次提交。我们将回溯到第一次提交。为此，我们需要第一个提交的 ID。这可以通过查看 git 日志的输出找到。

```bash
$ git log
commit 62e793f6941c7e0d4ad9a1345a175fe8f45cb9df
Author: bitbucket 
Date: Fri Dec 1 15:03:07 2017 -0800
prepend content to reset_lifecycle_file

commit dc67808a6da9f0dec51ed16d3d8823f28e1a72a
Author: bitbucket 
Date: Fri Dec 1 10:21:57 2017 -0800

update content of reset_lifecycle_file

commit 780411da3b47117270c0e3a8d5dcfd11d28d04a4

Author: bitbucket 
Date: Thu Nov 30 16:50:39 2017 -0800

initial commit
```

请记住，每个系统的提交历史 ID 都是唯一的。这意味着本示例中的提交 ID 将与您在个人计算机上看到的不同。本例中我们感兴趣的提交 ID 是 780411da3b47117270c0e3a8d5dcfd11d28d04a4。这是对应于 "初始提交 "的 ID。找到这个 ID 后，我们将把它作为soft reset的目标。

在回到过去之前，让我们先检查一下 repo 的当前状态。

```bash
$ git status && git ls-files -s
On branch main
nothing to commit, working tree clean
100644 67cc52710639e5da6b515416fd779d0741e3762e 0 reset_lifecycle_file
```

在这里，我们执行了 git status 和 git ls-files -s 的组合命令，结果显示软件仓库有待处理的更改，暂存索引中的 reset_lifecycle_file 版本为 67cc52710639e5da6b515416fd779d0741e3762e。有鉴于此，让我们执行一次软重置，回到第一次提交。

```bash
$git reset --soft 780411da3b47117270c0e3a8d5dcfd11d28d04a4
$ git status && git ls-files -s
On branch main
Changes to be committed:
    (use "git reset HEAD ..." to unstage)

modified: reset_lifecycle_file
100644 67cc52710639e5da6b515416fd779d0741e3762e 0 reset_lifecycle_file
```

上面的代码会执行一次 "软重置"，同时调用 git status 和 git ls-files 组合命令，输出版本库的状态。我们可以检查 repo 的状态输出，并注意到一些有趣的现象。首先，git status 显示 reset_lifecycle_file 有修改，并高亮显示这些修改是为下一次提交准备的。其次，git ls-files 输出显示暂存索引没有变化，并保留了之前的 SHA 67cc52710639e5da6b515416fd779d0741e3762e。

为了进一步弄清重置过程中发生了什么，让我们查看一下 git 日志：

```bash
$ git log commit 780411da3b47117270c0e3a8d5dcfd11d28d04a4 Author: bitbucket  Date: Thu Nov 30 16:50:39 2017 -0800 initial commit
```

现在，日志输出显示提交历史中有一次提交。这有助于清楚地说明 --soft 所做的一切。与所有的 git 重置调用一样，重置的第一个动作是重置提交树。我们之前用--hard 和--mixed 重置的例子都是针对 HEAD 的，并没有及时移动提交树。在软重置过程中，这就是发生的全部情况。

这可能会让人困惑，为什么 git status 会显示有修改过的文件。`--soft`不会触及暂存索引，因此对暂存索引的更新会随着我们的提交历史记录而及时回溯。git ls-files -s 的输出显示 reset_lifecycle_file 的 SHA 没有变化，这就证实了这一点。需要提醒的是，git status 并不显示 "三棵树 "的状态，而是显示它们之间的差异。在本例中，它显示的是暂存索引比提交历史中的改动要早，就好像我们已经暂存了这些改动一样。

### Resetting vs reverting

如果说 git revert 是撤销修改的 "安全 "方法，那么 git reset 就是危险的方法。使用 git reset 确实存在丢失工作的风险。Git reset永远不会删除提交，但提交可能会成为 "孤儿"，这意味着没有从 ref 访问它们的直接路径。这些 "孤儿 "提交通常可以用 git reflog 找到并恢复。Git 在运行内部垃圾回收器后，会永久删除任何 "孤儿 "提交。默认情况下，Git 会每隔 30 天运行一次垃圾回收器。提交历史是 "git 三棵树 "之一，其他两棵树（暂存索引和工作目录）并不像提交历史那样具有永久性。使用该工具时必须小心，因为它是唯一有可能丢失工作的 Git 命令之一。

Revert 是为了安全地撤销公开提交，而 git reset 则是为了撤销对暂存索引和工作目录的本地修改。由于目标不同，这两个命令的实现方式也不同：reset会完全删除更改集，而revert会保留原始更改集，并使用新提交来应用撤销。

### Don't reset public history

当快照被推送到公共仓库后，千万不要使用 git reset。发布提交后，你必须假定其他开发者也依赖于该提交。

删除其他团队成员还在继续开发的提交会给协作带来严重问题。当他们尝试与你的版本库同步时，看起来就像项目历史的一部分突然消失了。

一旦你在重置后添加了新提交，Git 就会认为你的本地历史与 origin/main 有所偏离，而同步版本库所需的合并提交很可能会让你的团队感到困惑和沮丧。

重点是，请确保 git reset ＜commit＞ 是用在出错的本地版本库上，而不是用在已发布的修改上。如果您需要修复公开提交，git revert 命令就是专门为此设计的。

### Examples

```
git reset ＜file＞
```

从暂存区域删除指定文件，但保留工作目录不变。这将取消文件的暂存，但不会覆盖任何更改。

```
git reset
```

重置暂存区域以匹配最新提交，但工作目录保持不变。这将在不覆盖任何更改的情况下解除所有文件的暂存，让你有机会从头开始重新构建暂存快照。

```
git reset --hard
```

重置暂存区域和工作目录，使其与最新提交一致。除了取消暂存更改，`--hard` 标志还会告诉 Git 覆盖工作目录中的所有更改。换句话说，这会抹去所有未提交的改动，所以在使用前，请确保你真的想扔掉本地的开发成果。

```
git reset  
```

将当前分支顶端向后移动到提交位置，重置暂存区域以匹配，但工作目录保持不变。自＜提交＞后所做的所有更改都将保留在工作目录中，这样你就可以使用更简洁、更原子化的快照来重新提交项目历史。

```
git reset --hard  
```

将当前分支顶端向后移动到＜commit＞，并重置暂存区域和工作目录，使其与之匹配。这不仅会抹去未提交的修改，还会抹去之后的所有提交。

### 取消文件暂存

在准备暂存快照时，经常会用到 git reset 命令。下一个示例假定您已经将两个名为 hello.py 和 main.py 的文件添加到了版本库中。

```bash
# Edit both hello.py and main.py

# Stage everything in the current directory
git add .

# Realize that the changes in hello.py and main.py
# should be committed in different snapshots

# Unstage main.py
git reset main.py

# Commit only hello.py
git commit -m "Make some changes to hello.py"

# Commit main.py in a separate snapshot
git add main.py
git commit -m "Edit main.py"
```

正如你所看到的，git reset 可以让你取消与下一次提交无关的改动，从而帮助你保持高度集中的提交。

### 删除本地提交

下一个示例展示了一个更高级的用例。它演示了当你在一个新实验上工作了一段时间，但在提交了几个快照后决定将其完全丢弃时会发生的情况。

```bash
# Create a new file called `foo.py` and add some code to it

# Commit it to the project history
git add foo.py
git commit -m "Start developing a crazy feature"

# Edit `foo.py` again and change some other tracked files, too

# Commit another snapshot
git commit -a -m "Continue my crazy feature"

# Decide to scrap the feature and remove the associated commits
git reset --hard HEAD~2
```

`git reset HEAD~2` 命令会将当前分支向后移动两次提交，从而有效地从项目历史中删除我们刚刚创建的两个快照。请记住，这种重置只能用于未发布的提交。如果你已经将提交推送到共享版本库，千万不要执行上述操作。

### Summary

回顾一下，`git reset`是一个功能强大的命令，用于撤销对 Git 仓库状态的本地更改。`git reset`基于 "Git 的三棵树"。这三棵树分别是提交历史（`HEAD`）、暂存索引（`Staging Index`）和工作目录（`Working Directory`）。有三个命令行选项与这三棵树相对应。选项`--soft`、`--mixed` 和`--hard`可以传递给 `git reset`。

在本文中，我们还使用了其他几条 Git 命令来演示重置过程。关于这些命令的更多信息，请参见：git status、git log、git add、git checkout、git reflog 和 git revert。

-----------

## git rm

刚开始使用 Git 时，一个常见问题是 "如何让 Git 不再跟踪某个（或某些）文件？`git rm` 命令用于从 Git 仓库中删除文件。它可以看作是 `git add` 命令的反义词。

### git rm overview

git rm 命令可用于删除单个文件或文件集。git rm 的主要功能是从 Git 索引中移除跟踪的文件。此外，git rm 还能用于同时从暂存索引和工作目录中移除文件。没有只从工作目录删除文件的选项。被操作的文件必须与当前 HEAD 中的文件完全相同。如果文件的 HEAD 版本与暂存索引或工作树版本不一致，Git 会阻止删除操作。这种阻止是一种安全机制，以防止正在进行的修改被移除。

注意，git rm 不会移除分支。

### 使用方法

```bash
<file>…
```

指定要删除的目标文件。选项值可以是单个文件、以空格分隔的文件列表 file1 file2 file3 或通配符文件 (~./directory/*)。

```
-f
--force
```

-f 选项用于覆盖 Git 为确保 HEAD 中的文件与暂存索引和工作目录中的当前内容相匹配而进行的安全检查。

```
-n
--dry-run
```

“试运行”选项是一种安全措施，它将执行命令但不会实际删除文件。相反，它会输出它将删除哪些文件。

```
-r
```

该`-r`选项是“递归”的简写。当以递归模式操作时，将删除目标目录以及该目录的所有内容。

```
--
```

分隔符选项用于明确区分文件名列表和传递给 git rm 的参数。 如果某些文件名的语法可能被误认为其他选项，这个选项就很有用。

```
--cached
```

缓存选项指定只在暂存索引中删除文件。工作目录文件将不会被删除。

```
--ignore-unmatch
```

这样，即使没有匹配的文件，命令也会以 0 sigterm 状态退出。这是一个 Unix 级别的状态代码。代码 0 表示命令调用成功。当使用 git rm 作为一个更大的 shell 脚本的一部分时，--ignore-unmatch 选项会很有用。

```
-q
--quiet
```

quiet 选项会隐藏 git rm 命令的输出。通常情况下，每删除一个文件，命令就会输出一行。

### 如何撤销 git rm

执行 git rm 并不是永久更新。该命令会更新暂存索引和工作目录。在创建新提交并将更改添加到提交历史之前，这些更改不会被持久化。这意味着这里的改动可以用普通的 Git 命令 "撤销"。

```bash
git reset HEAD
```

Reset会将当前暂存索引和工作目录还原回 HEAD 提交时的状态。这将撤销 git rm。

```
git checkout .
```

签出也会产生同样的效果，从 HEAD 恢复文件的最新版本。

如果执行了 git rm，但又创建了新的提交来坚持删除，可以使用 git reflog 来查找执行 git rm 之前的 ref。

该命令的 `<file>` 参数可以是精确路径、通配符文件 glob 模式或精确目录名。该命令只删除当前已提交到 Git 仓库的路径。

通配符文件全局匹配可跨目录。使用通配符 glob 时一定要谨慎。请看例子：directory/* 和 directory*。第一个示例将删除 directory/ 的所有子文件，而第二个示例将删除所有同级目录，如 directory1 directory2 directory_whatever，这可能是一个意想不到的结果。

git rm 命令只在当前分支上运行。移除事件只应用于工作目录和暂存索引树。在创建新提交之前，文件移除不会持续到版本库历史中。

### 示例

```bash
git rm Documentation/\*.txt
```

本例使用通配符文件删除Documentation目录及其任何子目录下的所有 *.txt 文件。

请注意，本例中的星号 `*` 是用斜线转义的；这是为了防止 shell 扩展通配符。通配符会扩展 Documentation/ 目录下的文件和子目录的路径名。

```bash
git rm -f git-*.sh
```

本例使用了强制选项，并以所有通配符 `git-*.sh` 文件为目标。强制选项明确地将目标文件从工作目录和暂存索引中删除。

### 如何删除文件系统中已不存在的文件

正如上文 "为什么使用 git rm 而不是 rm "一文所述，git rm 实际上是一个方便的命令，它结合了标准 shell rm 和 git add 命令，可以从工作目录中移除文件，并将移除的文件放到暂存索引中。如果仅使用标准 shell rm 命令就能移除多个文件，那么版本库就会变得非常累赘。

如果打算在下一次提交时记录所有明确移除的文件，git commit -a 会将所有移除事件添加到暂存索引中，为下一次提交做准备。

不过，如果想要持续移除用 shell rm 移除的文件，请使用下面的命令：

```
git diff --name-only --diff-filter=D -z | xargs -0 git rm --cached
```

该命令将生成工作目录中已删除文件的列表，并将该列表导入 git rm --cached 更新暂存索引。

### Git rm summary

git rm 是一条在两个主要的 Git 内部状态管理树（工作目录和暂存索引）上运行的命令。它是一种方便的方法，结合了 shell 默认的 rm 命令和 git add 的效果。也就是说，它会首先从文件系统中移除目标文件，然后将移除事件添加到暂存索引中。该命令是 Git 中用于撤销更改的众多命令之一。

--------

# 重写历史

`Git commit --amend` 和其他重写历史的方法

## 介绍

本教程将介绍各种重写和修改 Git 历史记录的方法。Git 使用几种不同的方法来记录更改。我们将讨论不同方法的优缺点，并举例说明如何使用它们。本教程将讨论覆盖已提交快照的一些最常见原因，并告诉你如何避免这样做的陷阱。

Git 的主要工作是确保你不会丢失已提交的变更。但它也能让你完全控制开发工作流程。这包括让你精确定义你的项目历史；然而，这也带来了丢失提交的可能性。Git 在提供历史记录重写命令时声明，使用这些命令可能会导致内容丢失。

Git 有多种存储历史和保存修改的机制。这些机制包括 `Commit --amend`, `git rebase` 和 `git reflog`。这些选项为你提供了强大的工作流程定制选项。在本教程结束时，你将熟悉一些命令，这些命令能让你重组 Git 提交，并能避免重写历史时常遇到的陷阱。

### Changing the Last Commit: `git commit --amend`

`git commit --amend` 命令是修改最新提交的便捷方法。它能让你把已缓存的改动与上一次提交结合起来，而不是创建一个全新的提交。它也可以用来简单编辑上一次提交的信息，而不改变其快照。不过，修改并不只是修改最近的提交，而是完全替换它，这意味着修改后的提交将是一个拥有自己 ref 的新实体。对 Git 来说，它看起来就像一个全新的提交，在下图中用星号（`*`）表示。使用 `git commit --amend` 有几种常见情况。我们将在下面的章节中举例说明。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-amend-history.png" style="zoom:50%;" />

**更改最近的 Git 提交信息**

```bash
git commit --amend
```

比方说，你刚刚提交了文件，却在提交日志信息中犯了一个错误。在没有任何缓存的情况下运行这条命令，就能在不更改快照的情况下编辑前一次提交的信息。

在日常开发过程中，提交过早的情况时有发生。忘记暂存文件或提交信息格式错误都很容易发生。使用 `--amend` 标志就能很方便地修复这些小错误。

```bash
git commit --amend -m "an updated commit message"
```

添加 `-m` 选项后，就可以从命令行输入新信息，而无需打开编辑器。

**更改已提交的文件**

下面的示例演示了基于 Git 的开发中常见的一种情况。假设我们编辑了几个文件，想在一次快照中提交，但第一次提交时忘记添加其中一个文件。要修复这个错误，只需暂存另一个文件，然后使用 `--amend` 标志提交即可：

```bash
# Edit hello.py and main.py
git add hello.py
git commit 
# Realize you forgot to add the changes from main.py 
git add main.py 
git commit --amend --no-edit
```

使用`--no-edit`（无编辑）标记，可以在不修改提交信息的情况下修改提交。由此产生的提交将取代不完整的提交，看起来就像我们在一个快照中提交了对 hello.py 和 main.py 的修改。

**不要修改公共提交**

修改后的提交实际上是全新的提交，之前的提交将不再出现在当前分支上。其后果与重置公共快照相同。避免修改其他开发人员的工作所基于的提交。这种情况会让其他开发人员感到困惑，而且恢复起来也很复杂。

**Recap**

`git commit --amend`可以让你使用最近的提交，并添加新的暂存修改。您可以从 Git 暂存区添加或移除修改，然后用 `--amend` 提交来应用。如果没有暂存的改动，`--amend` 仍会提示你修改上次提交的信息日志。在与其他团队成员共享的提交中使用 `--amend` 时要谨慎。修改与其他用户共享的提交可能需要混乱而冗长的合并冲突解决方案。

### 更改旧提交或多次提交

要修改旧提交或多个提交，可以使用 `git rebase` 将一系列提交合并为一个新的基本提交。在标准模式下，`git rebase` 可以让你真正改写历史--自动将当前工作分支中的提交应用到已通过的分支顶部。由于新的提交将替换旧的提交，因此不要在已公开推送的提交上使用 git rebase，否则会导致项目历史消失。

在这种或类似情况下，如果需要保留完整的项目历史，可以在 `git rebase` 中添加 -i 选项，以交互方式运行 `rebase`。这样，你就有机会在过程中修改单个提交，而不是移动所有提交。你可以在 git rebase 页面了解更多关于交互式`rebase` 和其他 `rebase` 命令的信息。

**更改已提交的文件**

在`rebase`过程中，`edit` 或 `e` 命令将暂停该提交上的`rebase`回放，并允许您使用 `git commit --amend` 命令进行其他修改：

```bash
Stopped at 5d025d1... formatting
You can amend the commit now, with



 git commit --amend



Once you are satisfied with your changes, run



 git rebase --continue
```

**Multiple messages**

每个常规 Git 提交都会有一条日志信息，解释提交过程中发生了什么。这些信息对了解项目历史很有价值。在`rebase`过程中，你可以对提交执行一些命令来修改提交信息。

**Squash commits for a clean history**

通过 `s` "`squash` "命令，我们可以看到 `rebase` 的真正用途。`Squash` 允许你指定要将哪些提交合并到之前的提交中。这就是实现 "干净历史 "的方法。在 `rebase` 回放过程中，Git 会为每个提交执行指定的 `rebase` 命令

在`squash commits`示例中，Git 会打开配置的文本编辑器，提示合并指定的提交信息。整个过程可视化如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/squashed-commited.png" style="zoom:50%;" />

请注意，使用 rebase 命令修改的提交与原始提交的 ID 不同。如果之前的提交已被改写，则标记为 pick 的提交会有一个新的 ID。

**Recap**

`git rebase` 使您能够修改历史记录，而交互式 `rebase` 使您可以在不留下“混乱”痕迹的情况下执行此操作。这创造了犯错和纠正错误以及完善工作的自由，同时仍然保持干净、线性的项目历史记录。

### 安全网：git reflog

引用日志，或称 "reflog"，是 Git 用于记录应用于分支提示和其他提交引用的更新的机制。`reflog`允许你回溯提交，即使它们没有被任何分支或标记引用。重写历史后，reflog 会包含分支旧状态的信息，允许你在必要时回到旧状态。

每次分支提示因任何原因更新时（通过切换分支、拉入新变更、重写历史或仅仅通过添加新提交），都会在 reflog 中添加一个新条目。在本节中，我们将对 git reflog 命令做一个高层次的了解，并探讨一些常见的用法。

**使用方法**

```
git reflog
```

这将显示本地版本库的 reflog。

```
git reflog --relative-date
```

这将显示指定相对日期信息（如 2 周前）的 reflog。

**示例**

为了理解 git reflog，我们先来看一个例子。

```bash
0a2e358 HEAD@{0}: reset: moving to HEAD~2
0254ea7 HEAD@{1}: checkout: moving from 2.2 to main
c10f740 HEAD@{2}: checkout: moving from main to 2.2
```

上面的引用日志显示了从主分支签出到 2.2 分支并返回。从那里开始，将硬重置到旧的提交。最新活动显示在顶部，标记为。`HEAD@{0}`

如果事实证明您不小心向后移动了，重写日志中将包含您意外删除 2 次提交之前的主提交（0254ea7）。

`git reset --hard 0254ea7`

通过 git reset，现在可以将 main 改回之前的提交状态。这为历史记录被意外更改提供了安全保障。

需要注意的是，reflog 只在变更已提交到本地版本库的情况下提供安全网，而且它只跟踪版本库分支tip的移动。此外，reflog 条目也有过期日期。reflog 条目的默认过期时间是 90 天。

如需了解更多信息，请参阅我们的 git reflog 页面。

### Summary

在本文中，我们讨论了更改 git 历史记录和撤消 git 更改的几种方法。我们对 git rebase 过程进行了高层次的研究。一些关键要点是：

- 有很多方法可以用 git 重写历史
- 使用 `git commit --amend` 来修改最新的提交日志信息
- 使用 `git commit --amend` 修改最新提交
- 用于`git rebase`合并提交并修改分支的历史记录
- `git rebase -i`与标准 `git rebase` 相比，对历史修改提供了更细粒度的控制。

了解有关我们在各个页面中介绍的命令的更多信息：

- [git rebase](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)
- [git reflog](https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog)

-------------

## git rebase

本文档将深入讨论 git rebase 命令。Rebase 命令在建立仓库和重写历史页面中也有介绍。本页将更详细地介绍 git rebase 的配置和执行。这里将介绍常见的 Rebase 用例和陷阱。

Rebase 是两个 Git 工具之一，专门用于将一个分支的变更整合到另一个分支。另一个变更整合工具是 git merge。merge总是向前移动的变更记录。相反，rebase 具有强大的历史重写功能。Rebase 本身有两种主要模式： "手动 "和 "交互 "模式。下面我们将详细介绍不同的 Rebase 模式。

### 什么是 git rebase？

变基是将一系列提交移动或合并到一个新的基本提交的过程。变基在`feature`分支工作流程中最有用，也最容易可视化。一般流程可视化如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/brand-new-commits.png" style="zoom:50%;" />

从内容的角度来看，变基是将分支的基础从一个提交更改为另一个提交，使分支看起来像是从另一个提交创建的。在内部，Git 通过创建新的提交并将它们应用到指定的base来实现这一点。理解这一点非常重要：尽管分支看起来相同，但它是由全新的提交组成的。

### 用法

变基的主要原因是维持线性项目历史。例如，考虑这样一种情况：在你开始创建`feature`分支后，`main`分支已经取得了进展。你想在`feature`分支中获得`main`分支的最新更新，但又想保持分支历史的完整性，这样看起来就像是你一直在最新的主分支上工作。这样做的好处是，以后可以将`feature`分支干净利落地合并回`main`分支。为什么要保持 "干净的历史"？在执行 Git 操作以调查回归的引入时，拥有清晰历史的好处就显而易见了。更真实的场景是

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/clean-history.png" style="zoom:50%;" />

1. 在主分支中发现一个错误。一个曾成功运行的功能现在被破坏了
2. 开发人员检查所使用`git log`的主分支的历史记录，因为“干净的历史记录”使开发人员能够快速推断出项目的历史记录
3. 开发人员无法通过`git log`确定 bug 是何时出现的，因此执行了 `git bisect`
4. 由于 git 历史是干净的，git bisect 在查找回归时就有了一组精炼的提交进行比较。开发人员很快就能找到引入 bug 的提交，并采取相应措施

有关 git log 和 git bisect 的更多信息，请参阅它们各自的用法页面。

将feature分支整合到main分支有两种选择：直接merge或先变基后再合并。前者会导致 3 路合并和合并提交，而后者会导致快进合并和完美的线性历史记录。下图演示了变基到主分支如何促进快进合并。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-rebase-usage.png" style="zoom:50%;" />

变基是将上游更改集成到本地存储库的常用方法。用 Git 合并的方式拉入上游改动会导致每次想查看项目进展时都要提交一次多余的合并。另一方面，变基就像在说："我想把我的改动建立在大家已经完成的基础上"。

### 不要rebase公共历史

正如我们之前在 "重写历史 "一文中讨论过的，一旦提交被推送到公共仓库，就不应该对其进行`rebase`。`rebase`会用新提交替换旧提交，看起来就像项目历史的这一部分突然消失了。

### Git rebase 标准版 vs git rebase 交互版

Git rebase interactive 是指 git rebase 接受 -- i 参数。这代表 "交互式"。如果不带任何参数，命令将以标准模式运行。在这两种情况下，假设我们都创建了一个独立的特性分支。

```bash
# Create a feature branch based off of main 
git checkout -b feature_branch main
# Edit files 
git commit -a -m "Adds new feature" 
```

标准模式下的 git rebase 会自动将当前工作分支中的提交应用到已传递分支的头部。

```bash
git rebase <base>
```

它会自动将当前分支重定向到`＜base＞`上，＜base＞可以是任何类型的提交引用（例如 ID、分支名、标签或 HEAD 的相对引用）。

使用 `-i` 标志运行 git rebase 会开始一个交互式的`rebase`会话。交互式`rebase`不会盲目地将所有提交移到新的基础上，而是让你有机会在过程中修改单个提交。这样，你就可以通过删除、拆分和修改现有的一系列提交来清理历史。这就像是 Git commit --amend。

```bash
git rebase --interactive <base>
```

此操作会将当前分支重定向到`＜base＞`，但使用的是交互式重定向会话。这将打开一个编辑器，你可以为每个要重定向的提交输入命令（如下所述）。这些命令决定了如何将单个提交转移到新的base。你还可以重新排列提交列表，改变提交本身的顺序。一旦你为每个提交指定了命令，Git 就会开始回放应用 rebase 命令的提交。rebase编辑命令如下：

```bash
pick 2231360 some old commit
pick ee2adc2 Adds new feature


# Rebase 2cf755d..ee2adc2 onto 2cf755d (9 commands)
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

### 其他 rebase 命令

正如改写历史页面所详述的，rebase 可用于修改旧提交、多次提交、已提交文件和多条信息。虽然这些都是最常见的应用，但 git rebase 还有额外的命令选项，在更复杂的应用中也很有用。

- `git rebase -- d` 表示在回放过程中，该提交将从最终合并的提交块中丢弃。
- `git rebase -- p` 表示保持提交原样。它不会修改提交信息或内容，在分支历史中仍是一个单独的提交。
- `git rebase -- x` 在播放期间，在每个标记的提交上执行命令行 shell 脚本。一个有用的示例是在特定提交上运行代码库的测试套件，这可能有助于识别变基期间的回归。

### Recap

交互式变基使您可以完全控制项目历史记录。这为开发人员提供了很大的自由，因为它允许他们在专注于编写代码时提交“混乱”的历史记录，然后在事后返回并清理它。

大多数开发人员都喜欢在将特性分支合并到主代码库之前，使用交互式rebase来完善它。这让他们有机会压制无关紧要的提交、删除过时的提交，并在提交到 "正式 "项目历史之前确保其他一切正常。在其他人看来，整个功能就是在一系列精心策划的提交中开发出来的。

交互式`rebase`的真正威力可以从生成的主分支的历史中看出。对于其他人来说，您似乎是一位出色的开发人员，第一次就以完美的提交实现了新功能。这就是交互式变基可以保持项目历史清晰且有意义的方式。

### 配置选项

使用 git config 可以设置一些 rebase 属性。这些选项将改变 git rebase 输出的外观和感觉。

- **`rebase.stat`**: 一个布尔值，默认设置为 false。该选项可切换显示可视化 diffstat 内容，显示自上次变基以来发生的更改
- `rebase.autoSquash:`布尔值，用于切换 --autosquash 行为
- `rebase.missingCommitsCheck:`可设置为多个值，从而改变缺失提交时的回溯行为。

| `warn`   | 在交互模式下打印警告输出，警告已删除的提交 |
| -------- | ------------------------------------------ |
| `error`  | 停止重置并打印已删除的提交警告信息         |
| `ignore` | 默认设置为忽略任何缺失提交警告             |

- `rebase.instructionFormat:`将用于格式化交互式变基显示的格式字符串

### 高级变基应用

命令行参数 --onto 可以传递给 git rebase。在 git rebase --onto 模式下，命令会扩展为

```bash
git rebase --onto <newbase> <oldbase>
```

该`--onto`命令启用更强大的形式或变基，允许传递特定的引用作为变基的提示。假设我们有一个示例存储库，其分支如下：

```bash
   o---o---o---o---o  main
        \
         o---o---o---o---o  featureA
              \
               o---o---o  featureB
```

featureB 基于 featureA，但是，我们意识到 featureB 不依赖于 featureA 的任何更改，并且可以从 main 中分支出来。

```bash
git rebase --onto main featureA featureB
```

featureA 是＜oldbase＞，main 是＜newbase＞，featureB 是＜newbase＞的 HEAD 所指向的引用。结果如下

```bash
                      o---o---o  featureB
                     /
    o---o---o---o---o  main
     \
      o---o---o---o---o  featureA
                        
```

### 了解rebase的危险

使用 Git Rebase 时需要考虑的一个警告是，在 rebase 工作流程中，合并冲突可能会变得更加频繁。如果您有一个长期存在的分支偏离了主分支，就会发生这种情况。最终你会想要针对 main 进行 rebase，那时它可能包含许多新的提交，你的分支更改可能会与这些提交发生冲突。这一点很容易解决，只要频繁地针对主分支rebase，并进行更频繁的提交即可。在处理冲突时，可以向 git rebase 传递 --continue 和 --abort 命令行参数，以推进或重置rebase。

更严重的变基警告是交互式历史重写造成的提交丢失。在交互模式下运行 rebase 并执行 squash 或 drop 等子命令，会从分支的即时日志中删除提交。乍一看，这些提交好像永远消失了。使用 git reflog 可以恢复这些提交，并撤销整个rebase。有关使用 git reflog 查找丢失提交的更多信息，请访问我们的 Git reflog 文档页面。

Git Rebase 本身并没有严重危险。当执行交互式变基重写历史并强制将结果推送到由其他用户共享的远程分支时，真正的危险情况就会出现。这是一种应该避免的模式，因为它能够在其他远程用户拉取时覆盖他们的工作。

### 从上游变基恢复

如果有其他用户执行了rebase并强制推送到您正在提交的分支，那么 git pull 就会用强制推送的提示覆盖您基于之前分支的任何提交。幸运的是，使用 git reflog 可以获得远程分支的 reflog。在远程分支的 reflog 中，你可以找到该分支被重置前的 ref。然后，你就可以使用 --onto 选项，根据该远程 ref 重定向你的分支，如上文 "高级变基应用 "部分所述。

### Summary

在本文中，我们介绍了`git rebase`用法。我们讨论了基本和高级用例以及更高级的示例。一些关键讨论点是：

- git rebase 标准模式与交互模式
- git rebase 配置选项
- git rebase --onto
- git rebase 丢失提交

--------

## git reflog

本页将详细讨论 git reflog 命令。Git 使用一种称为引用日志或 "reflogs "的机制来跟踪分支顶端的更新。许多 Git 命令都接受一个参数来指定引用或 "ref"，也就是指向某个提交的指针。常见的例子包括

- `git checkout `
- `git reset `
- `git merge `

Reflogs会记录本地仓库中 Git 分支的更新时间。除了分支提示 reflog，Git 储藏库也有一个特殊的 reflog。reflog存储在本地存储库 `.git`目录下的目录中。git reflog 目录位于 .git/logs/refs/heads/.、.git/logs/HEAD 和 .git/logs/refs/stash（如果在 repo 上使用了 git stash）。

我们在重写历史页面上对 git reflog 进行了深入讨论。本文档将介绍：git reflog 的扩展配置选项、git reflog 的常见用例和误区、如何使用 git reflog 撤销修改，以及更多。

### 基本用法

最基本的 Reflog 用例是调用：

```bash
git reflog
```

这实质上是一条捷径，相当于：

```bash
git reflog show HEAD
```

这将输出 HEAD reflog。你应该看到类似的输出：

```bash
eff544f HEAD@{0}: commit: migrate existing content
bf871fd HEAD@{1}: commit: Add Git Reflog outline
9a4491f HEAD@{2}: checkout: moving from main to git_reflog
9a4491f HEAD@{3}: checkout: moving from Git_Config to main
39b159a HEAD@{4}: commit: expand on git context 
9b3aa71 HEAD@{5}: commit: more color clarification
f34388b HEAD@{6}: commit: expand on color support 
9962aed HEAD@{7}: commit: a git editor -> the Git editor
```

### Reflog参考

默认情况下，git reflog 会输出 HEAD ref 的 reflog。HEAD 是对当前活动分支的符号引用。引用日志也可用于其他引用。访问 git ref 的语法是 name@{qualifier}。访问 git ref 的语法是`name@{qualifier}`。除了`HEAD`refs 之外，还可以引用其他分支、标签、远程和 Git 储藏库。

您可以通过执行以下命令来获取所有引用的完整引用日志：

```bash
 git reflog show --all 
```

要查看特定分支的 reflog，可将该分支名称传给 git reflog show。

Bitbucket 会显示 "创建新版本库 "页面。花点时间看看对话框的内容。除了版本库类型，你在这个页面上输入的所有内容都可以在以后修改。

```bash
git reflog show otherbranch



9a4491f otherbranch@{0}: commit: seperate articles into branch PRs
35aee4a otherbranch{1}: commit (initial): initial commit add git-init and setting-up-a-repo docs
```

执行此示例将显示 `otherbranch` 分支的 reflog。下面的示例假定您之前已使用 git stash 命令隐藏了一些改动。

```bash
git reflog stash

0d44de3 stash@{0}: WIP on git_reflog: c492574 flesh out intro
```

这将输出 Git 存储的引用日志。返回的 ref 指针可以传递给其他 Git 命令：

```bash
git diff stash@{0} otherbranch@{0}
```

执行该示例代码后，将显示 Git diff 输出，比较 stash@{0} 和 otherbranch@{0} 的改动。

每个 reflog 条目都附有一个时间戳。这些时间戳可用作 Git ref 指针语法的限定符标记。这样就能按时间过滤 Git reflog。以下是一些可用的时间限定符示例：

- `1.minute.ago`
- `1.hour.ago`
- `1.day.ago`
- `yesterday`
- `1.week.ago`
- `1.month.ago`
- `1.year.ago`
- `2011-05-17.09:00:00`

时间限定词可以组合（例如`1.day.2.hours.ago`），此外还接受复数形式（例如`5.minutes.ago`）。

时间限定符可以传递给其他 git 命令。

```bash
 git diff main@{0} main@{1.day.ago} 
```

此示例将当前的主分支与 1 天前的主分支进行比较。如果你想知道某段时间内发生的变化，这个示例非常有用。

### 子命令和配置选项

git reflog 接受一些附加参数，这些参数被视为子命令。

#### Show - `git reflog show`

show 默认为隐式传递。例如命令:

```bash
git reflog main@{0} 
```

等同于命令：

```bash
git reflog show main@{0} 
```

此外，git reflog show 是 git log -g --abbrev-commit --pretty=oneline 的别名。执行 git reflog show 会显示所传递的引用日志。

#### Expire - `git reflog expire`

`expire`子命令可清除旧的或无法访问的 reflog 条目。`expire`子命令有可能导致数据丢失。最终用户通常不会使用该子命令，但 git 内部会使用它。给 git reflog expire 传递 -n 或 --dry-run 选项会执行一次 "试运行"，输出哪些 reflog 条目被标记为要剪枝，但实际上不会剪枝。

默认情况下，reflog 的过期日期设置为 90 天。默认情况下，reflog 的过期日期设置为 90 天。过期时间可以通过向 git reflog expire 传递命令行参数 --expire=time 或设置 git 配置名 gc.reflogExpire 的值来指定。

#### Delete - `git reflog delete`

`Delete`子命令不言自明，它将删除传递的 reflog 条目。与`expire`一样，`delete`有可能丢失数据，终端用户通常不会调用。

### 恢复丢失的提交

Git 不会丢失任何东西，即使是在执行历史重写操作（如rebase或修改提交）时也是如此。在下一个例子中，假设我们对仓库做了一些新的改动。我们的  `git log --pretty=oneline` 看起来如下：

```bash
338fbcb41de10f7f2e54095f5649426cb4bf2458 extended content
1e63ceab309da94256db8fb1f35b1678fb74abd4 bunch of content
c49257493a95185997c87e0bc3a9481715270086 flesh out intro
eff544f986d270d7f97c77618314a06f024c7916 migrate existing content
bf871fd762d8ef2e146d7f0226e81a92f91975ad Add Git Reflog outline
35aee4a4404c42128bee8468a9517418ed0eb3dc initial commit add git-init and setting-up-a-repo docs
```

然后，我们提交这些更改并执行以下操作：

```bash
#make changes to HEAD
git commit -am "some WIP changes"
```

增加了新的提交。日志现在看起来像:

```bash
37656e19d4e4f1a9b419f57850c8f1974f871b07 some WIP changes
338fbcb41de10f7f2e54095f5649426cb4bf2458 extended content
1e63ceab309da94256db8fb1f35b1678fb74abd4 bunch of content
c49257493a95185997c87e0bc3a9481715270086 flesh out intro
eff544f986d270d7f97c77618314a06f024c7916 migrate existing content
bf871fd762d8ef2e146d7f0226e81a92f91975ad Add Git Reflog outline
35aee4a4404c42128bee8468a9517418ed0eb3dc initial commit add git-init and setting-up-a-repo docs
```

请在命令行输入以下内容，如果出现此错误

```bash
$ git clone

https://emmap1@bitbucket.org/emmap1/bitbucketstationlocations.git 

Cloning into 'bitbucketspacestation'...

fatal: could not read

Password for 'https://emmap1@bitbucket.org': No such file or directory
```

此时，我们通过执行以下命令对主分支执行交互式变基：

```bash
git rebase -i origin/main
```

在变基过程中，我们使用 rebase s子命令标记压缩的提交。我们会将一些提交压入最新的 "some WIP changes "提交中。

因为我们删除了提交，所以现在的 git log输出看起来像:

```bash
40dhsoi37656e19d4e4f1a9b419f57850ch87dah987698hs some WIP changes
35aee4a4404c42128bee8468a9517418ed0eb3dc initial commit add git-init and setting-up-a-repo docs
```

如果我们检查一下此时的 git 日志，就会发现已经没有被标记为压缩(squashing)的提交了。如果我们想对其中一个被压缩的提交进行操作呢？也许从历史中删除它的改动？这就是利用 reflog 的好机会。

```bash
git reflog
37656e1 HEAD@{0}: rebase -i (finish): returning to refs/heads/git_reflog
37656e1 HEAD@{1}: rebase -i (start): checkout origin/main
37656e1 HEAD@{2}: commit: some WIP changes
```

我们可以看到有关于`rebase`开始和结束的引用日志条目，在这些条目之前是我们的"some WIP changes"提交。我们可以将 reflog ref 传递给 git reset，然后reset到rebase之前的提交。
