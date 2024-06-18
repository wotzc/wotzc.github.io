---
title: git协作工作流程
date: 2024-03-11 14:59:38
tags:
- git
- 版本控制
categories: 版本控制
---

# git 远程

SVN 使用单一的集中式版本库作为开发人员的通信中心，通过在开发人员的工作副本和中心版本库之间传递变更集来实现协作。这与 Git 的分布式协作模式不同，后者为每个开发人员提供了自己的版本库副本，并拥有自己的本地历史记录和分支结构。用户通常需要共享一系列提交，而不是单个变更集。Git 可让你在不同版本库之间共享整个分支，而不是将工作副本中的变更集提交到中央版本库。

`git remote`命令是更广泛系统中负责同步变更的一个部分。通过 `git remote`命令注册的记录会与 `git fetch`、`git push` 和 `git pull` 命令结合使用。这些命令都有各自的同步职责，可以在相应的链接中查看。

## git remote

使用 `git remote` 命令可以创建、查看和删除与其他版本库的连接。远程连接更像是书签，而不是其他版本库的直接链接。与其说它们提供了对另一个版本库的实时访问，不如说它们是一个方便的名字，可以用来引用一个不太方便的 URL。

例如，下图显示了从你的版本库到中央版本库和另一个开发者版本库的两个远程连接。与其用完整的 URL 来引用它们，你可以把 origin 和 john 的快捷方式传递给其他 Git 命令。

### git remote使用概述

`git remote` 命令本质上是一个界面，用于管理存储在版本库 `./.git/config` 文件中的远程条目列表。以下命令用于查看远程列表的当前状态。

### 查看 git 远程配置

```bash
git remote
```

列出您与其他存储库的远程连接。

```bash
git remote -v
```

与上面的命令相同，但包含每个连接的 URL。

### 创建和修改 git 远程配置

`git remote` 命令也是修改软件仓库 `./.git/config` 文件的一种方便或 "辅助 "方法。下面的命令可以让你管理与其他版本库的连接。以下命令将修改版本库的 `./.git/config` 文件。使用文本编辑器直接编辑 `./.git/config` 文件也能达到以下命令的效果。

```bash
git remote add <name> <url>
```

创建与远程版本库的新连接。添加远程仓库后，你就可以在其他 Git 命令中使用`＜name＞`作为`＜url＞`的快捷方式。

```bash
git remote rm <name>
```

删除与名为 `＜name＞` 的远程存储库的连接。

```bash
git remote rename <old-name> <new-name>
```

将远程连接从 `＜old-name＞` 重命名为 `＜new-name＞`。

### git remote讨论

Git 旨在为每个开发人员提供一个完全独立的开发环境。这意味着信息不会在仓库之间自动来回传递。相反，开发者需要手动将上游提交拉入本地仓库，或手动将本地提交推回中央仓库。`git remote` 命令实际上只是为这些 "共享 "命令传递 URL 的一种更简便的方式。

当你用 `git clone` 克隆一个仓库时，它会自动创建一个名为 `origin` 的远程连接，指向被克隆的仓库。这对于创建中央仓库本地副本的开发者来说非常有用，因为它提供了一种拉取上游变更或发布本地提交的简便方法。这也是大多数基于 Git 的项目将其中央仓库称为 `origin` 的原因。

Git 支持多种引用远程仓库的方式。访问远程仓库最简单的两种方式是 `HTTP` 和 `SSH` 协议。`HTTP` 是一种允许匿名、只读访问仓库的简单方式。例如

```bash
http://host/path/to/repo.git
```

但是，通常无法将提交推送到 `HTTP` 地址（反正你也不想允许匿名推送）。要进行读写访问，应该使用 `SSH`：

```
ssh://user@host/path/to/repo.git
```

你需要在主机上有一个有效的 `SSH` 账户，除此之外，Git 本身就支持通过 `SSH` 验证访问。现代安全的第三方托管解决方案（如 Bitbucket.com）会为你提供这些 URL。

### git remote命令

`git remote`命令是众多 Git 命令中的一个，这些命令会附加 "子命令"。下面将介绍常用的 `git remote`子命令。

```bash
git remote ADD <NAME> <URL>
```

在 `./.git/config` 中为名为`＜name＞`的远程仓库URL`＜url＞`添加一条记录。

接受 `-f` 选项，该选项会在远程记录创建后立即 `git fetch`。

接受 `--tags` 选项，该选项将立即 git 抓取并从远程仓库导入每个标签。

```bash
git remote RENAME <OLD> <NEW>
```

更新 `./.git/config`，将记录`＜OLD＞`重命名为`＜NEW＞`。所有远程跟踪分支和远程配置设置都会更新。

```bash
git remote REMOVE or RM <NAME>
```

修改 `./.git/config` 并删除名为 `＜NAME＞` 的远程。所有远程跟踪分支和远程配置设置都会被删除。

```
git remote GET-URL <NAME>
```

输出远程记录的 URL。

接受 `--push` 时，查询的是推送 URL 而不是获取 URL。

使用 `--all` 时，将列出远程记录的所有 URL。

```
git remote SHOW <NAME>
```

输出远程 `＜NAME＞`的高级信息。

```
git remote PRUNE <NAME>
```

删除远程版本库中不存在的任何本地`＜NAME＞` 分支。

接受 `--dry-run` 选项，该选项会列出要删除的分支，但不会真的删除。

除了`origin`之外，与队友的仓库建立连接通常也很方便。例如，如果你的同事约翰在 dev.example.com/john.git 上维护了一个可公开访问的仓库，你可以添加如下连接：

```bash
git remote add john http://dev.example.com/john.git
```

有了这种访问个人开发者仓库的权限，就可以在中央仓库之外进行协作。这对于开发大型项目的小团队来说非常有用。

### 查看远程存储库

默认情况下，`git remote` 命令会列出之前存储的与其他版本库的远程连接。这将产生单行输出，列出远程仓库的 "书签 "名称。

```bash
$ git remote
origin
upstream
other_users_repo
```

使用`-v`选项调用`git remote`会打印书签仓库名称列表以及相应的仓库 URL。`-v`选项代表 "`verbose`"。下面是 `git remote` 的详细输出示例。

```
git remote -v
origin  git@bitbucket.com:origin_user/reponame.git (fetch)
origin  git@bitbucket.com:origin_user/reponame.git (push)
upstream    https://bitbucket.com/upstream_user/reponame.git (fetch)
upstream    https://bitbucket.com/upstream_user/reponame.git (push)
other_users_repo    https://bitbucket.com/other_users_repo/reponame (fetch)
other_users_repo    https://bitbucket.com/other_users_repo/reponame (push)
```

### 添加远程存储库

`git remote add` 命令将为远程仓库创建一个新的连接记录。添加远程记录后，你就可以把它作为其他 Git 命令的快捷方式。有关可接受的 URL 语法的更多信息，请参阅下面的 "版本库 URL "部分。该命令将在版本库的`./.git/config`文件中创建一条新记录。配置文件更新示例如下

```
$ git remote add fake_test https://bitbucket.com/upstream_user/reponame.git; [remote "remote_test"] 
   url = https://bitbucket.com/upstream_user/reponame.git 
   fetch = +refs/heads/*:refs/remotes/remote_test/*
```

### 检查远程存储库

`show` 子命令可附加到 `git remote`，以提供远程配置的详细输出。该输出将包含与远程相关联的分支列表，以及用于获取和推送的端点。

```bash
git remote show upstream
* remote upstream
   Fetch URL: https://bitbucket.com/upstream_user/reponame.git
   Push URL: https://bitbucket.com/upstream_user/reponame.git
   HEAD branch: main
   Remote branches:
      main tracked
      simd-deprecated tracked
      tutorial tracked
   Local ref configured for 'git push':
      main pushes to main (fast-forwardable)
```

### 从 Git 远程fetching和pulling

使用 `git remote` 命令配置远程记录后，远程名称就可以作为参数传递给其他 Git 命令，以便与远程仓库通信。`git fetch` 和 `git pull` 都可以用来读取远程仓库。这两个命令都有不同的操作，我们将在它们各自的链接中作更深入的解释。

### 推送到 Git 远程

`git push`命令用于写入远程仓库。

```bash
git push <remote-name> <branch-name>
```

此示例将把`＜branch-name＞`的本地状态上传到`＜remote-name＞`指定的远程存储库。

### 重命名和删除Git 远程

```bash
git remote rename <old-name> <new-name>
```

`git remote rename` 命令不言自明。执行该命令后，远程连接将从`<old-name>`更名为`<new-name>`。此外，该命令还将修改 `./.git/config` 中的内容，重新命名远程记录。

```bash
git remote rm <name>
```

`git remote rm`命令将删除与`＜name＞`参数指定的远程仓库的连接。为了演示，让我们 "撤销 "上一个例子中的远程添加。如果我们执行`git remote rm remote_test`，然后检查`./.git/config`的内容，就会发现 [remote "remote_test"] 记录已不复存在。

-----

## git fetch

`git fetch` 命令会将提交、文件和引用从远程仓库下载到本地仓库。当你想看看其他人都在做些什么时，你就会使用 `fetch` 命令。它与`svn update`类似，能让你看到中心历史的进展，但并不强制你把修改合并到自己的仓库中。Git 将获取的内容与现有的本地内容隔离开来，对本地开发工作完全没有影响。获取的内容必须使用 `git checkout` 命令明确签出。因此，在将提交内容整合到本地仓库之前，获取内容是一种安全的审查方式。

从远程仓库下载内容时，可以使用`git pull`和`git fetch`命令来完成任务。`git fetch` 是这两个命令的 "安全 "版本。`git pull`是更激进的选择；它会下载本地活动分支的远程内容，并立即执行 `git merge` 为新的远程内容创建合并提交。如果您有待处理的修改正在进行中，这将导致冲突，并启动合并冲突解决流程。

### git fetch 如何使用远程分支

为了更好地理解 `git fetch` 的工作原理，我们先来讨论一下 Git 是如何组织和存储提交的。在仓库的 `./.git/objects` 目录中，Git 隐藏了所有本地和远程提交。Git 通过使用分支索引将远程和本地分支的提交区分开来。本地分支的 refs 保存在 `./.git/refs/heads/` 目录中。执行 `git branch` 命令会输出本地分支 refs 的列表。下面是一个 git 分支输出示例，其中包含一些演示分支名称。

```bash
git branch
main
feature1
debug2
```

检查 `/.git/refs/heads/` 目录的内容也会发现类似的输出。

```bash
ls ./.git/refs/heads/
main
feature1
debug2
```

远程分支和本地分支一样，只不过它们映射的是别人版本库中的提交。远程分支的前缀是其所属的远程仓库，这样就不会与本地分支混淆。和本地分支一样，Git 也有远程分支的 refs。远程分支参考位于 `./.git/refs/remotes/` 目录中。下一个示例代码片段展示了在获取一个名为`remote-repo`的远程仓库后可能看到的分支：

```bash
git branch -r
# origin/main
# origin/feature1
# origin/debug2
# remote-repo/main
# remote-repo/other-feature
```

该输出显示了我们之前检查过的本地分支，但现在显示的是以 `origin/` 为前缀的分支。此外，我们现在还能看到以 `remote-repo` 为前缀的远程分支。你可以像检出本地分支一样检出远程分支，但这将使你处于分离的 `HEAD` 状态（就像检出旧提交一样）。你可以把它们看作只读分支。要查看远程分支，只需在 `git branch` 命令中加入 `-r` 标志即可。

你还可以用常用的 `git checkout` 和 `git log` 命令来检查远程分支。如果您批准了远程分支包含的改动，就可以用普通的 git 合并将其合并到本地分支中。因此，与 SVN 不同，同步本地仓库和远程仓库实际上只需两步：`fetch`，然后`merge`。`git pull` 命令是实现这一过程的便捷快捷方式。

### git fetch 命令和选项

```bash
git fetch <remote>
```

从版本库中获取所有分支。这也会从另一个版本库下载所有需要的提交和文件。

```bash
git fetch <remote> <branch>
```

与上述命令相同，但只获取指定的分支。

```bash
git fetch --all
```

强力移动，可获取所有已注册的远程版本库及其分支：

```bash
git fetch --dry-run
```

`--dry-run` 选项将执行命令的试运行。它会输出获取过程中的操作示例，但不会真的执行这些操作。

### git fetch示例

#### git fetch远程分支

下面的示例将演示如何获取远程分支并将本地工作状态更新为远程内容。在这个示例中，我们假设有一个中心仓库，本地仓库是使用 `git clone` 命令从该仓库克隆的。我们还假设有一个名为`coworkers_repo`的远程仓库，其中包含一个 `feature_branch`，我们将对其进行配置和获取。有了这些假设，让我们继续举例说明。

首先，我们需要使用 `git remote` 命令配置远程仓库。

```bash
git remote add coworkers_repo git@bitbucket.org:coworker/coworkers_repo.git
```

在这里，我们使用 repo URL 创建了一个指向同事 repo 的引用。现在，我们将把该远程名称传递给 `git fetch` 以下载内容。

```bash
git fetch coworkers_repo coworkers/feature_branch
fetching coworkers/feature_branch
```

现在我们在本地拥有了`coworkers/feature_branch`的内容，我们需要将其整合到本地工作副本中。首先，我们使用 `git checkout` 命令签出新下载的远程分支。

```bash
git checkout coworkers/feature_branch
Note: checking out coworkers/feature_branch'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

git checkout -b <new-branch-name>
```

该检出操作的输出结果显示，我们处于分离的 HEAD 状态。这是意料之中的，这意味着我们的 HEAD ref 指向的 ref 与本地历史不一致。由于 HEAD 指向的是`coworkers/feature_branch` ref，我们可以从该 ref 创建一个新的本地分支。分离的 HEAD "输出向我们展示了如何使用 `git checkout` 命令来做到这一点：

```
git checkout -b local_feature_branch
```

在这里，我们创建了一个名为 `local_feature_branch` 的新本地分支。这样，HEAD 的更新就会指向最新的远程内容，我们就可以在此基础上继续开发。

#### 使用 git fetch 同步远程分支

下面的示例介绍了本地版本库与中央版本库主分支同步的典型工作流程。

```bash
git fetch origin
```

这将显示已下载的分支：

```
a1e8fb5..45e66a4 main -> origin/main
a1e8fb5..9e8ab1c develop -> origin/develop
* [new branch] some-feature -> origin/some-feature
```

这些新远程分支的提交在下图中显示为方形而不是圆形。正如你所看到的，`git fetch` 可以让你访问另一个仓库的整个分支结构。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-fetch-2x.png" style="zoom:50%;" />

要查看上游`main`分支添加了哪些提交，可以使用 `origin/main` 作为过滤器运行 git 日志：  

```bash
git log --oneline main..origin/main
```

要批准更改并将其合并到本地主分支，请使用以下命令：

```bash
git checkout main
git log origin/main
```

然后我们就可以使用 `git merge origin/main`：

```bash
git merge origin/main
```

`origin/main` 分支和`main`分支现在指向同一个提交，你就能与上游开发同步了。

### Summary

`git fetch` 与 `git remote`、`git branch`、`git checkout` 和 `git reset` 配合使用，可将本地仓库更新为远程仓库的状态。`git fetch` 命令是 git 协作工作流程中的一个重要部分。`git fetch` 的行为与 `git pull` 相似，但 `git fetch` 可被视为更安全的非破坏性版本。

---------

## git push

`git push`命令用于将本地仓库内容上传到远程仓库。`push`是将提交从本地仓库转移到远程仓库的方式。它与 `git fetch` 相对应，但 `fetch` 是将提交导入本地分支，而 `push` 则是将提交导出到远程分支。使用 `git remote` 命令可以配置远程分支。推送有可能会覆盖改动，因此在推送时应小心谨慎。下文将讨论这些问题。

### git push 用法

```bash
git push <remote> <branch>
```

将指定的分支连同所有必要的提交和内部对象推送到远程指定分支。这会在目标仓库创建一个本地分支。为了防止覆盖提交，Git 不会允许你在目标版本库中进行非快速合并的推送。

```bash
git push <remote> --force
```

与上述命令相同，但即使结果是非快进合并，也会强制推送。除非你确信知道自己在做什么，否则不要使用 `--force` 标志。

将所有本地分支推送到指定的远程分支。

```bash
git push <remote> --tags
```

推送分支或使用 `--all` 选项时，标签不会自动推送。`--tags`标记会将所有本地标记发送到远程版本库。

### git push讨论

`git push` 最常用于发布本地修改并上传到中央仓库。本地版本库修改后，执行推送以与远程团队成员共享修改内容。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-before-push-2x.png" style="zoom:50%;" />

-----------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-after-push-2x.png" style="zoom:50%;" />

上图显示的是当本地主仓库的进度超过中心仓库的主仓库时，您通过运行 `git push origin main` 发布改动的情况。请注意，`git push` 与在远程仓库中运行 `git merge main` 本质上是一样的。

`git push` 是整个 Git "同步 "过程中使用的众多命令之一。`git push` 可视为 "上传 "命令，而 `git fetch` 和 `git pull` 可视为 "下载 "命令。通过下载或上传移动变更集后，可在目的地执行 `git merge`以整合变更。

### 强制push

为了防止覆盖中央仓库的历史，Git 会在推送请求导致非快速合并时拒绝推送请求。因此，如果远程历史与本地历史有偏差，就需要拉取远程分支并将其合并到本地分支，然后再尝试推送。这类似于 SVN 让你在提交变更集之前通过 svn update 与中心版本库同步。

`--force`标志会覆盖这一行为，并使远程版本库的分支与本地分支相匹配，同时删除上次推送后可能发生的任何上游变更。唯一需要强制推送的情况是，当你意识到刚刚共享的提交并不完全正确，并用 `git commit --amend` 或交互式 `rebase` 进行了修正。不过，在使用 `--force` 选项之前，您必须绝对确定没有任何队友拉取过这些提交。

### 例子

**默认 git push**

下面的示例描述了向中央版本库发布本地贡献的标准方法之一。首先，它通过获取中央版本库的副本并在其上重置你的改动，确保你的本地`main`分支是最新的。交互式变基也是在分享之前清理提交的好机会。然后，`git push` 命令会将本地主仓库的所有提交发送到中央仓库。

```bash
git checkout main
git fetch origin main
git rebase -i origin/main
# Squash commits, fix up commit messages etc.
git push origin main
```

由于我们已经确保了本地`main`分支是最新的，这应该会导致快进合并，而 `git push` 也不会抱怨上面讨论过的任何非快进问题。

**修改后的强制推送**

`git commit` 命令接受一个 `--amend` 选项，它将更新之前的提交。修改提交通常是为了更新提交信息或添加新的改动。一旦提交被修改，git 推送就会失败，因为 Git 会把修改后的提交和远程提交视为不同的内容。要推送修改过的提交，必须使用 `--force` 选项。

```bash
# make changes to a repo and git add
git commit --amend
# update the existing commit message
git push --force origin main
```

上面的示例假定是在一个有提交历史的现有版本库中执行的。`git commit --amend` 用于更新之前的提交。然后使用   `--force` 选项强制推送修正后的提交。

**删除远程分支或标签**

有时，出于记账或组织目的，需要清理分支。要完全删除分支，必须在本地和远程删除。

```bash
git branch -D branch_name
git push origin :branch_name
```

上面的命令将删除名为 `branch_name` 的远程分支，向 `git push` 传递一个以冒号为前缀的分支名称将删除该远程分支。

------

## git pull

`git pull` 命令用于从远程仓库获取和下载内容，并立即更新本地仓库以匹配这些内容。在基于 Git 的协作工作流程中，将远程上游改动合并到本地仓库是一项常见任务。`git pull` 命令实际上是其他两个命令的组合，即 `git fetch` 和 `git merge`。在操作的第一阶段，`git pull` 会对 HEAD 指向的本地分支执行 `git fetch`。下载完成后，`git pull` 将进入合并工作流程。会创建一个新的合并提交，并更新 HEAD 以指向新的提交。

### How it works

`git pull` 命令首先运行 `git fetch`，从指定的远程仓库下载内容。然后执行 `git merge`，将远程内容的引用和头部合并到一个新的本地合并提交中。为了更好地演示拉取和合并过程，让我们看看下面的例子。假设我们有一个包含主分支和远程起源的仓库。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-pull-usage-2x.png" style="zoom:50%;" />

在这种情况下，`git pul`l 会下载本地提交和主提交分歧点上的所有改动。在本例中，分歧点是 E。`git pull` 会获取分歧的远程提交，即 A-B-C。然后，拉取过程会创建一个新的本地合并提交，其中包含新的远程分歧提交的内容。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-fetch-and-merge-2x.png" style="zoom:50%;" />

在上图中，我们可以看到新的提交 H。该提交是一个新的合并提交，包含了远程 A-B-C 提交的内容，并有一条合并日志信息。这个例子是 `git pull` 合并策略中的一种。`git pull` 可以通过 `--rebase` 选项来使用 `rebase` 合并策略，而不是`merge`提交。下一个例子将演示 `rebase pull` 的工作原理。假设我们在第一个图的起点执行了 `git pull --rebase` 命令。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-pull-rebase-2x.png" style="zoom:50%;" />

从图中我们可以看到，`rebase` 拉取并没有创建新的 H 提交。相反，`rebase` 复制了远程提交 A--B--C，并改写了本地提交 E--F--G，使其出现在本地`origin/main`提交历史记录中。

### 常用选项

```bash
git pull <remote>
```

获取当前分支的指定远程副本，并立即将其合并到本地副本中。这等同于`git fetch ＜remote＞` 然后 `git merge origin/＜current-branch＞`。

```bash
git pull --no-commit <remote>
```

与默认调用类似，获取远程内容，但不会创建新的合并提交。

```bash
git pull --rebase <remote>
```

与上一次的拉取相同，并非使用 `git merge` 将远程分支与本地分支整合，而是使用 `git rebase`。

```bash
git pull --verbose
```

在拉取过程中提供详细输出，显示正在下载的内容和合并细节。

### 讨论

你可以把 `git pull` 想象成 Git 版本的 `svn update`。它是同步本地仓库和上游改动的简便方法。下图解释了拉取过程的每一步。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-pull-step1-2x.png" style="zoom:50%;" />

------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-pull-step2-2x.png" style="zoom: 33%;" />

---------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-pull-step3-2x.png" style="zoom: 33%;" />

您一开始以为您的版本库已经同步，但 `git fetch` 发现自从您上次检查之后，`origin` 的 `main` 版本有了新的进展。然后 `git merge` 立即将远程的 `main` 整合到本地的 `main` 中。

`git pull`是许多负责 "同步 "远程内容的命令之一。`git remote`命令用于指定同步命令将在哪些远程端点上运行。`git push`命令用于将内容上传到远程仓库。

`git fetch`命令可能会与 `git pull` 命令混淆。它们都用于下载远程内容。`git fetch`被视为 "安全 "选项，而`git pull`则被视为不安全选项。或者，`git pull`会下载远程内容，并立即尝试改变本地状态以匹配该内容。这可能会无意中导致本地仓库处于冲突状态。

### Pulling via Rebase

使用 `--rebase` 选项可以防止不必要的合并提交，从而确保线性历史。相比合并，许多开发者更喜欢重定向，因为这就像在说："我想把我的改动放在其他人的改动之上"。从这个意义上说，使用带有 `--rebase` 标志的 `git pull` 甚至比普通的 `git pull` 更像 svn update。

事实上，使用 `--rebase` 拉取是一个很常见的工作流程，以至于有专门的配置选项：

```bash
git config --global branch.autosetuprebase always
```

 运行该命令后，所有 `git pull` 命令都将通过 `git rebase` 而不是 `git merge` 进行整合。

### Git Pull Examples

以下示例演示了如何在常见情况下使用 `git pull`：

```bash
git pull
```

执行 `git pull` 的默认调用相当于 `git fetch origin HEAD` 和 `git merge HEAD`，其中 `HEAD` 是指向当前分支的 ref。

#### Git pull on remotes

```bash
git checkout new_feature
git pull <remote repo>
```

本例首先执行签出一个新分支并切换到分支。然后，通过传递执行 `git pull`。这将隐式地从 `<remote repo>` 下载完成后，将启动 `git merge`。

#### Git pull rebase instead of merge

下面的示例演示了如何使用 `rebase` 与中央版本库的主分支同步：

```bash
git checkout main
git pull --rebase origin
```

这只是将你的本地修改移到其他人已经贡献的内容之上。

-------------------

# 使用分支

## Git Branch

本文将深入评述 git 分支命令，并讨论 Git 的整体分支模型。分支是大多数现代版本控制系统都具备的功能。在其他版本控制系统中，分支操作会耗费大量时间和磁盘空间。在 Git 中，分支是日常开发流程的一部分。

Git 分支实际上就是您所做修改的快照指针。当你想添加一个新功能或修复一个错误时，无论大小，你都要创建一个新的分支来封装你的改动。这样一来，不稳定的代码就很难被合并到主代码库中，而且在合并到主分支之前，你还有机会清理未来的历史。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-branchs-2x.png" style="zoom:50%;" />

上图展示了一个包含两条独立开发线的版本库，一条用于开发一个小功能，另一条用于开发一个运行时间较长的功能。通过在分支中开发这两条线，不仅可以并行处理这两条线，还能保证主分支不被有问题的代码干扰。

与其他版本控制系统模式相比，Git 分支的实现要轻便得多。Git 并不是把文件从一个目录复制到另一个目录，而是把分支作为提交的引用来存储。从这个意义上说，分支代表了一系列提交的顶端，而不是提交的容器。分支的历史是通过提交关系推断出来的。

在阅读过程中，请记住 Git 分支与 SVN 分支不同。SVN 分支只在偶尔的大规模开发工作中使用，而 Git 分支则是日常工作流程中不可或缺的一部分。下面的内容将详细介绍 Git 分支的内部架构。

### How it works

一个分支代表一条独立的开发线路。分支是`edit/stage/commit`流程的抽象。你可以把它们看作是申请全新工作目录、暂存区域和项目历史的一种方式。新的提交会被记录在当前分支的历史中，从而在项目历史中形成一个分叉。

git 分支命令允许你创建、列出、重命名和删除分支。但它不能在分支间切换，也不能把分叉的历史记录重新组合起来。因此，`git branch` 与 `git checkout` 和 `git merge` 命令紧密结合。

### Common options

```bash
git branch
```

列出版本库中的所有分支。这与 `git branch --list` 同义。

```bash
git branch <branch>
```

创建一个名为 `＜branch＞` 的新分支。这并不签出新分支。

```bash
git branch -d <branch>
```

删除指定的分支。这是一个 "安全 "的操作，因为如果分支有未合并的更改，Git 会阻止你删除该分支。

```bash
git branch -D <branch>
```

强制删除指定的分支，即使它还有未合并的改动。如果你想永久删除与某一行开发相关的所有提交，可以使用这条命令。

```bash
git branch -m <branch>
```

将当前分支重命名为 `＜branch＞`。

```bash
git branch -a
```

列出所有远程分支。

### 创建分支

要知道，分支只是提交的指针。创建分支时，Git 需要做的只是创建一个新指针，不会以任何其他方式改变版本库。如果你一开始创建的版本库是这样的

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-branch-state-2x.png" style="zoom:50%;" />

然后，使用以下命令创建一个分支：

```bash
git branch crazy-experiment
```

版本库历史保持不变。你得到的只是一个指向当前提交的新指针：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-branch-create-branch-2x.png" style="zoom:50%;" />

注意，这只会创建新分支。要开始向其添加提交，需要用 `git checkout` 选中它，然后使用标准的 `git add` 和 `git commit` 命令。

### 创建远程分支

到目前为止，这些例子都演示了本地分支的操作。`git branch` 命令同样适用于远程分支。要在远程分支上运行，必须先配置远程 repo 并将其添加到本地 repo 配置中。

```bash
$ git remote add new-remote-repo https://bitbucket.com/user/repo.git
# Add remote repo to local repo config
$ git push <new-remote-repo> crazy-experiment~
# pushes the crazy-experiment branch to new-remote-repo
```

这条命令会将本地分支 `crazy-experiment` 的副本推送到远程仓库 `＜remote＞`。

### 删除分支

一旦你完成了某个分支的工作，并将其合并到主代码库中，你就可以自由删除该分支，而不会丢失任何历史记录：

```bash
git branch -d crazy-experiment
```

但是，如果分支尚未合并，上述命令将输出错误信息：

```
error: The branch 'crazy-experiment' is not fully merged. If you are sure you want to delete it, run 'git branch -D crazy-experiment'.
```

这样就不会失去对整个开发线的访问权限。如果你真的想删除该分支（例如，它是一个失败的实验），可以使用大写的 -D 标志：

```bash
git branch -D crazy-experiment
```

该命令会删除分支，无论其状态如何，且不会发出警告，因此请谨慎使用。

前面的命令将删除分支的本地副本。该分支可能仍然存在于远程版本库中。要删除远程分支，请执行以下命令。

```bash
git push origin --delete crazy-experiment
```

或

```bash
git push origin :crazy-experiment
```

这将向远程源代码库推送一个删除信号，触发远程 `crazy-experiment` 分支的删除。

### Summary

本文将讨论 Git 的分支行为和 git 分支命令。git 分支命令的主要功能是创建、列出、重命名和删除分支。为了进一步操作生成的分支，该命令通常与其他命令（如 `git checkout`）一起使用。有关 `git checkout` 分支操作的更多信息，如切换分支和合并分支，请参阅 `git checkout` 页面。

与其他 VCS 相比，Git 的分支操作既便宜又常用。这种灵活性使得 Git 工作流程的定制功能非常强大。

-------

## git checkout

本页将介绍 `git checkout` 命令。它将涵盖使用示例和边缘案例。在 Git 术语中，"checkout"是在目标实体的不同版本之间切换的行为。`git checkout` 命令针对三个不同的实体：文件、提交和分支。除了 "`checkout` "的定义，"checking out "也常用来表示执行 `git checkout` 命令的行为。在 "撤销修改 "主题中，我们看到了如何使用 git 签出查看旧提交。本文的大部分内容将集中在对分支的签出操作上。

签出分支与签出旧提交和文件类似，工作目录会被更新以匹配所选的分支/修订版本；不过，新的改动会被保存到项目历史中，也就是说，这不是一个只读操作。

### Checking out branches

使用 `git checkout` 命令，你可以在 git 分支创建的各个分支之间切换。签出分支会更新工作目录中的文件，使之与该分支中的版本相匹配，并让 Git 记录该分支上的所有新提交。可以把它想象成一种选择开发方向的方式。

为每个新功能建立一个专用分支，是对传统 SVN 工作流程的巨大转变。它让尝试新功能变得非常容易，而不必担心破坏现有功能，也让同时开发许多不相关的功能成为可能。此外，分支还为多个协作工作流程提供了便利。

`git checkout` 命令有时会与 `git clone` 混淆。这两个命令的区别在于，`clone` 的作用是从远程仓库获取代码，而 `checkout` 的作用则是在本地系统已经存在的代码版本之间切换。

### 使用已存在的分支

如果您正在使用的软件仓库包含已有的分支，您可以使用 `git checkout` 在这些分支间切换。要了解有哪些可用分支以及当前分支的名称，请执行 `git branch`。

```bash
$＞ git branch 
main 
another_branch 
feature_inprogress_branch 
$＞ git checkout feature_inprogress_branch
```

上面的例子演示了如何通过执行 `git branch` 命令查看可用分支列表，并切换到指定分支，在本例中就是 `feature_inprogress_branch`。

### 新分支

`git checkout`与 `git branch`是并行的。`git branch`命令可以用来创建一个新分支。当你想创建一个新功能时，可以用 `git branch new_branch`在主分支之外创建一个新分支。创建完成后，就可以使用 `git checkout new_branch` 切换到该分支。此外，`git checkout` 命令还接受一个 `-b` 参数，作为一种方便的方法，它会创建新的分支并立即切换到该分支。通过使用 `git checkout`，您可以在一个仓库中的多个特性之间进行切换。

```bash
git checkout -b ＜new-branch＞
```

上面的例子同时创建和签出了`＜new-branch＞`。`-b`选项是一个方便的标志，它告诉 Git 在运行`git checkout ＜new-branch＞` 之前先运行`git branch`。

```bash
git checkout -b ＜new-branch＞ ＜existing-branch＞
```

默认情况下，`git checkout -b` 会以当前 `HEAD` 为基础创建新分支。`git checkout` 可以传递一个可选的附加分支参数。在上面的例子中，传递了`＜existing-branch＞`，新分支就会基于现有分支，而不是当前的 `HEAD`。

### 切换分支

切换分支是一个简单明了的操作。执行以下命令将把 `HEAD` 指向`＜branchname＞`的顶端。

```bash
git checkout ＜branchname＞
```

Git 会在 reflog 中记录检出操作的历史。你可以执行`git reflog`查看历史记录。

### git checkout远程分支

与团队协作时，通常会使用远程资源库。这些版本库可能是托管和共享的，也可能是其他同事的本地副本。每个远程版本库都包含自己的分支集。要检出一个远程分支，必须先获取该分支的内容。

```bash
git fetch --all
```

在现代版本的 Git 中，你可以像签出本地分支一样签出远程分支。

```bash
git checkout ＜remotebranch＞
```

旧版本的 Git 需要在远程分支的基础上创建一个新分支。

```bash
git checkout -b ＜remotebranch＞ origin/＜remotebranch＞
```

此外，你还可以签出一个新的本地分支，并将其重置为远程分支的最后一次提交。

```bash
git checkout -b ＜branchname＞
git reset --hard origin/＜branchname＞
```

### 分离的 HEAD

既然我们已经了解了 `git checkout` 在分支上的三种主要用途，那么讨论一下 "分离的 HEAD "状态就很重要了。请记住，`HEAD` 是 Git 表示当前快照的方式。在内部，`git checkout` 命令只是更新 `HEAD`，使其指向指定的分支或提交。当它指向一个分支时，Git 不会抱怨，但当你签出一个提交时，它就会切换到 `“detached HEAD”`状态。

这是一个警告，告诉你你正在做的一切都与项目的其他开发 "分离 "了。如果你在分离 HEAD 状态下开始开发某个功能，就不会有任何分支允许你返回到该功能。当你不可避免地签出另一个分支（例如，将你的功能合并到其中）时，你将无法引用你的功能：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/detached-head-2x.png" style="zoom:50%;" />

------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/attached-head-2x.png" style="zoom:50%;" />

重点是，你的开发应该始终在分支上进行，而不是在分离的 HEAD 上。这样才能确保你的新提交始终有一个引用。不过，如果你只是在查看旧提交，那么是否处于分离的 HEAD 状态并不重要。

### Summary

本页主要介绍 `git checkout` 命令在更改分支时的用法。总的来说，在分支上使用 `git checkout` 会改变 `HEAD ref` 的目标。它可以用来创建分支、切换分支和签出远程分支。`git checkout` 命令是标准 Git 操作的基本工具。它是 `git merge` 的对应命令。`git checkout` 和 `git merge` 命令是实现 Git 工作流程的关键工具。

---------

## git merge

合并是 Git 将已分叉的历史重新组合起来的一种方式。通过 `git merge` 命令，你可以将 `git branch`创建的独立开发分支整合到一个分支中。

请注意，下面介绍的所有命令都会合并到当前分支。当前分支将被更新以反映合并，但目标分支将完全不受影响。这再次说明，`git merge` 经常与 `git checkout` 和 `git branch -d` 结合使用，前者用于选择当前分支，后者用于删除过时的目标分支。

### How it works

`git merge`会将多个提交序列合并成一个统一的历史。在最常见的使用案例中，`git merge` 被用来合并两个分支。本文档的以下示例将重点介绍这种分支合并模式。在这些情况下，git 合并会使用两个提交指针（通常是分支提示），并在它们之间找到一个共同的基本提交。一旦 Git 找到了共同的基本提交，它就会创建一个新的 "合并提交"，把每个排队合并的提交序列的改动合并在一起。

假设我们有一个基于主分支的新特性分支。现在我们想把这个特性分支合并到主干分支中。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-merge-2x.png" style="zoom:50%;" />

调用该命令会将指定的分支特性合并到当前分支，我们假设是主分支。Git 会自动决定合并算法（下文将讨论）。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-merge-feature-2x.png" style="zoom:50%;" />

与其他提交相比，合并提交有两个父提交。在创建合并提交时，Git 会尝试自动神奇地合并两个不同的历史。如果 Git 遇到两个历史中都有改动的数据，它就无法自动合并它们。这种情况属于版本控制冲突，Git 需要用户干预才能继续。

### 准备合并

在执行合并之前，有几个准备步骤要做，以确保合并顺利进行。

#### 确认接收分支

执行 `git status` 以确保 `HEAD` 指向正确的合并接收分支。如果需要，执行 `git checkout` 切换到接收分支。在我们的例子中，我们将执行 `git checkout main`。

#### 获取最新的远程提交

确保接收分支和合并分支都有最新的远程变更。执行 `git fetch` 提取最新的远程提交。获取完成后，执行 `git pull` 确保主分支有最新更新。

#### 合并

一旦完成了之前讨论过的 "准备合并 "步骤，就可以执行 `git merge` 开始合并了。

### 快进合并

当从当前分支顶端到目标分支之间存在一条线性路径时，就会发生快进合并。Git 不需要 "实际 "合并分支，只需移动（即 "快进"）当前分支顶端到目标分支顶端，就能整合历史。这就有效地合并了历史，因为目标分支的所有提交现在都可以通过当前分支获得。例如，将 `some-feature` 快速合并到 `main` 分支的过程如下：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/before-merge-2x.png" style="zoom:50%;" />

--------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/after-merge-2x.png" style="zoom:50%;" />

不过，如果分支已经分叉，则无法进行快进合并。如果没有通往目标分支的线性路径，Git 只能通过三向合并来合并它们。三向合并使用专门的提交来连接两个历史分支。Git 使用三个提交来生成合并提交：两个分支的顶端和它们的共同祖先。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/three-way-merge-before.png" style="zoom:50%;" />

---------

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/three-way-merge-after.png" style="zoom:50%;" />

虽然您可以使用这两种合并策略中的任何一种，但许多开发人员喜欢使用快进合并（通过`rebase`来实现）来处理小功能或错误修复，而保留三向合并来整合运行时间较长的功能。在后一种情况下，合并提交的结果就是两个分支的象征性连接。

我们的第一个例子演示了快进合并。下面的代码创建了一个新分支，向其添加了两次提交，然后通过快进合并将其整合到主线中。

```bash
# Start a new feature
git checkout -b new-feature main
# Edit some files
git add <file>
git commit -m "Start a feature"
# Edit some files
git add <file>
git commit -m "Finish a feature"
# Merge in the new-feature branch
git checkout main
git merge new-feature
git branch -d new-feature
```

这是短生命周期特性分支的常见工作流程，这些分支更多是作为独立开发使用，而不是作为长期运行特性的组织工具。

另外要注意的是，由于 new-feature 现在可以从主分支访问，因此 Git 不会抱怨 `git branch -d` 的问题。

如果在快进合并过程中需要合并提交以保存记录，可以使用 `--no-ff` 选项执行 `git merge`。

```bash
git merge --no-ff <branch>
```

该命令将指定的分支合并到当前分支，但始终会生成合并提交（即使是快进合并）。这对记录版本库中发生的所有合并很有用。

### 3 向合并

```bash
Start a new feature
git checkout -b new-feature main
# Edit some files
git add <file>
git commit -m "Start a feature"
# Edit some files
git add <file>
git commit -m "Finish a feature"
# Develop the main branch
git checkout main
# Edit some files
git add <file>
git commit -m "Make some super-stable changes to main"
# Merge in the new-feature branch
git merge new-feature
git branch -d new-feature
```

请注意，Git 不可能执行快进合并，因为没有办法在不回溯的情况下将 main 上移到 new-feature。

对于大多数工作流程来说，new-feature 会是一个更大的特性分支，需要很长时间才能开发完成，这也是新提交会同时出现在 main 上的原因。如果您的特性分支和上面例子中的分支一样小，您最好将其重定向到主分支，然后进行快进合并。这样可以防止多余的合并提交扰乱项目历史。

### 解决冲突

如果要合并的两个分支都修改了同一个文件的相同部分，Git 就无法判断该使用哪个版本。这种情况下，Git 会在合并提交前停止，让你手动解决冲突。

Git 合并流程的最大优点在于，它使用我们熟悉的编辑/阶段/提交工作流程来解决合并冲突。遇到合并冲突时，运行 `git status` 命令就会显示哪些文件需要解决。例如，如果两个分支都修改了 hello.py 的同一个部分，就会出现类似下面的情况：

```bash
On branch main
Unmerged paths:
(use "git add/rm ..." as appropriate to mark resolution)
both modified: hello.py
```

### 冲突如何呈现

当 Git 在合并过程中遇到冲突时，它会编辑受影响文件的内容，并在冲突内容的两侧标上可视化标记。这些视觉标记是 `<<<<<<<`、`=======` 和 `>>>>>>>`。在合并过程中，搜索项目中的这些标记，有助于找到需要解决冲突的地方。

```bash
here is some content not affected by the conflict
<<<<<<< main
this is conflicted text from main
=======
this is conflicted text from feature branch
>>>>>>> feature branch;
```

一般来说，`=======` 标记之前的内容是接收分支，之后的部分是合并分支。

一般来说，`=======` 标记之前的内容是接收分支，之后的部分是合并分支。

一旦确定了有冲突的部分，就可以按照自己的喜好进行修改。准备完成合并时，只需在有冲突的文件上运行 `git add`，告诉 Git 它们已经解决了。然后，再运行正常的 `git commit`，生成合并提交。这与提交普通快照的过程完全相同，因此普通开发者也能轻松管理自己的合并。

请注意，合并冲突只会发生在三方合并的情况下。快进合并中不可能出现冲突的变更。

### Summary

本文档概述了`git merge`命令。在使用 Git 时，合并是一个必不可少的过程。我们讨论了合并背后的内部机制，以及快速合并和三方真正合并之间的区别。一些主要收获如下

1. Git 合并将提交序列合并为一个统一的提交历史。

2. Git 有两种主要的合并方式： 快进和3向合并方式

3. Git 可以自动合并提交，除非两个提交序列中的改动有冲突。

------

## Git merge conflicts

版本控制系统主要是管理多个分布式作者（通常是开发人员）之间的贡献。有时，多个开发人员可能会尝试编辑相同的内容。如果开发人员 A 试图编辑开发人员 B 正在编辑的代码，就可能发生冲突。为了减少冲突的发生，开发者会在独立的分支中工作。`git merge`命令的主要职责就是合并不同的分支，并解决任何冲突的编辑。

### 理解合并冲突

一般来说，当两个人在一个文件中修改了相同的行，或者一个开发者删除了一个文件，而另一个开发者正在修改该文件时，就会产生冲突。在这种情况下，Git 无法自动判断哪个是正确的。冲突只会影响到进行合并的开发者，团队的其他成员并不知晓。Git 会将文件标记为冲突文件，并停止合并进程。解决冲突是开发人员的责任。

### 合并冲突的类型

合并可能在两个不同的时间点进入冲突状态。开始合并时和合并过程中。下面将讨论如何解决这些冲突情况。

#### Git 启动合并失败

当 Git 发现当前项目的工作目录或暂存区域有改动时，合并就会失败。Git 无法启动合并是因为这些待处理的改动可能会被正在合并的提交所覆盖。发生这种情况时，不是因为与其他开发者的提交冲突，而是与本地的待处理变更冲突。本地状态需要使用 `git stash`、`git checkout`、`git commit` 或 `git reset` 来稳定。启动时合并失败会输出以下错误信息：

```
error: Entry '<fileName>' not uptodate. Cannot merge. (Changes in working directory)
```

#### Git 在合并过程中失败

合并失败表示当前本地分支与正在合并的分支之间存在冲突。这表示与另一位开发者的代码有冲突。Git 会尽最大努力合并文件，但会把冲突文件中的问题留给你手动解决。中途合并失败会输出以下错误信息：

```
error: Entry '<fileName>' would be overwritten by merge. Cannot merge. (Changes in staging area)
```

### 创建合并冲突

为了真正熟悉合并冲突，下一节将模拟一个冲突，以便稍后检查和解决。本示例将使用类似 Unix 的 Git 命令行界面来执行模拟示例。

```bash
$ mkdir git-merge-test
$ cd git-merge-test
$ git init .
$ echo "this is some content to mess with" > merge.txt
$ git add merge.txt
$ git commit -am"we are commiting the inital content"
[main (root-commit) d48e74c] we are commiting the inital content
1 file changed, 1 insertion(+)
create mode 100644 merge.txt
```

此代码示例将执行一系列命令，完成以下工作。

- 新建一个名为 git-merge-test 的目录，并将其初始化为一个新的 Git 仓库。
- 新建一个文本文件 merge.txt，并在其中添加一些内容。 
- 将 merge.txt 添加到 repo 并提交。

现在我们有了一个新的软件仓库，其中有一个主分支和一个包含内容的 merge.txt 文件。接下来，我们将创建一个新分支，作为冲突合并使用。

```bash
$ git checkout -b new_branch_to_merge_later
$ echo "totally different content to merge later" > merge.txt
$ git commit -am"edited the content of merge.txt to cause a conflict"
[new_branch_to_merge_later 6282319] edited the content of merge.txt to cause a conflict
1 file changed, 1 insertion(+), 1 deletion(-)
```

接下来的命令序列可实现以下功能：

- 创建并签出名为 new_branch_ttoo_merge_later 的新分支
- 覆盖 merge.txt 中的内容  
- 提交新内容

通过这个新分支：new_branch_to_merge_later，我们创建了一个覆盖 merge.txt 内容的提交。

```bash
git checkout main
Switched to branch 'main'
echo "content to append" >> merge.txt
git commit -am"appended content to merge.txt"
[main 24fbe3c] appended content to merge.tx
1 file changed, 1 insertion(+)
```

这一系列命令会检查主分支，将内容添加到 merge.txt 中，然后提交。这样，我们的示例仓库就有了 2 条新提交。一个在主分支，一个在 new_branch_too_merge_later 分支。现在让我们用 git 合并 new_branch_to_merge_later，看看会发生什么！

```bash
$ git merge new_branch_to_merge_later
Auto-merging merge.txt
CONFLICT (content): Merge conflict in merge.txt
Automatic merge failed; fix conflicts and then commit the result.
```

BOOM 💥. 冲突出现了。感谢 Git 让我们了解到这一点！

### 如何确定合并冲突

正如我们在下面的例子中所体验到的，Git 会产生一些描述性输出，让我们知道发生了冲突。我们可以通过运行 `git status` 命令进一步了解情况

```bash
$ git status
On branch main
You have unmerged paths.
(fix conflicts and run "git commit")
(use "git merge --abort" to abort the merge)

Unmerged paths:
(use "git add <file>..." to mark resolution)

both modified:   merge.txt
```

`git status` 的输出显示，由于冲突，存在未合并的路径。现在，merge.text 文件处于修改状态。让我们检查一下文件，看看修改了什么。

```bash
$ cat merge.txt
<<<<<<< HEAD
this is some content to mess with
content to append
=======
totally different content to merge later
>>>>>>> new_branch_to_merge_later
```

在这里，我们使用 cat 命令输出了 merge.txt 文件的内容。我们可以看到一些奇怪的新内容

- `<<<<<<< HEAD`
- `=======`
- `>>>>>>> new_branch_to_merge_later`

将这些新线视为 "冲突分界线"。======= 行是冲突的 "中心"。中心和 <<<<<<< HEAD 行之间的所有内容都是 HEAD ref 指向的当前分支 main 中存在的内容。或者，中心线和 >>>>>>> new_branch_too_merge_later 之间的所有内容都是合并分支中的内容。

### 如何使用命令行解决合并冲突

解决合并冲突的最直接方法是编辑冲突文件。用你喜欢的编辑器打开 merge.txt 文件。在我们的例子中，让我们简单地删除所有冲突分隔符。修改后的 merge.txt 内容应如下所示：

```bash
this is some content to mess with
content to append
totally different content to merge later
```

文件编辑完成后，使用 `git add merge.txt` 将新合并的内容放入阶段。要最终完成合并，执行以下命令创建一个新提交

```bash
git commit -m "merged and resolved the conflict in merge.txt"
```

Git 会发现冲突已被解决，并创建一个新的合并提交来最终完成合并。

### 有助于解决合并冲突的 Git 命令

```bash
git status
```

在使用 Git 时，`status` 命令经常被使用，在合并过程中，它可以帮助识别冲突文件。

```bash
git log --merge
```

在 `git log`命令中传递 `--merge` 参数，会生成一个包含合并分支间冲突提交列表的日志。

```bash
git diff
```

`diff`可帮助查找版本库/文件状态之间的差异。这对预测和防止合并冲突非常有用。

### 当 git 启动合并失败时的工具

```bash
git checkout
```

`checkout` 可用于撤销对文件的更改，或更改分支

```bash
git reset --mixed
```

`reset`可用于撤销对工作目录和暂存区域的更改。

### 合并过程中出现 git 冲突时的工具

```bash
git merge --abort
```

使用 `--abort` 选项执行 `git merge` 会退出合并过程，并将分支恢复到合并开始前的状态。

```bash
git reset
```

`git reset`可在合并冲突期间使用，将冲突文件重置为已知的良好状态

### 总结

合并冲突是一种令人生畏的经历。幸运的是，Git 提供了功能强大的工具来帮助浏览和解决冲突。Git 具备自动合并功能，可以自行处理大部分合并工作。当两个不同的分支对文件的同一行进行了编辑，或者一个分支删除了文件，而另一个分支却编辑了文件时，就会产生冲突。在团队环境中工作时，冲突最有可能发生。

有很多工具可以帮助解决合并冲突。我们在这里讨论的 Git 命令行工具就有很多。有关这些工具的详细信息，请访问 git log、git reset、git status、git checkout 和 git reset 的独立页面。除了 Git 之外，许多第三方工具也提供简化的合并冲突支持功能。

------

## merge strategy

当一项工作完成、经过测试并准备并回开发主线时，你的团队需要做出一些策略选择。你有哪些合并策略选择？在本文中，我们将探讨各种可能性，然后提供一些有关 Atlassian 运行方式的说明。希望最后你能掌握一些工具来决定什么最适合你的团队。

### Git merge strategies

合并两个分支时。Git 会获取两个（或更多）提交指针，并试图在它们之间找到一个共同的基础提交。Git 有几种不同的方法来寻找基础提交，这些方法被称为 "合并策略"。一旦 Git 找到了共同的基础提交，它就会创建一个新的 "合并提交"，把指定合并提交的改动合并在一起。从技术上讲，合并提交是一个普通提交，只是恰好有两个父提交。

除非明确指定，否则`git merge`会自动选择合并策略。`git merge` 和 `git pull` 命令可以通过一个`-s`（策略）选项。`-s`选项可以附加所需的合并策略名称。如果没有明确指定，Git 会根据提供的分支选择最合适的合并策略。以下是可用的

#### Recursive

```bash
git merge -s recursive branch1 branch2
```

这是在两个分支上进行的操作。在拉取或合并一个分支时，递归是默认的合并策略。此外，它还能检测并处理涉及重命名的合并，但目前还不能使用检测到的副本。在拉取或合并一个分支时，这是默认的合并策略。

#### Resolve

```bash
git merge -s resolve branch1 branch2
```

这只能使用三向合并算法解决两个`head`的问题。它能仔细地检测出交叉合并的模糊之处，一般被认为是安全和快速的。

#### Octopus

```bash
git merge -s octopus branch1 branch2 branch3 branchN
```

两个以上分支的默认合并策略。如果合并中出现需要手动解决的冲突，`octopus`将拒绝合并尝试。它主要用于将类似功能的分支头捆绑在一起。

#### Ours

```bash
git merge -s ours branch1 branch2 branchN
```

我们的策略在多个 N 个分支上运行。输出的合并结果总是当前分支 HEAD 的结果。我们的 "`Ours`"一词意味着优先选择当前分支而有效忽略所有其他分支的所有更改。它的目的是用于合并相似特性分支的历史记录。

#### Subtree

```bash
git merge -s subtree branchA branchB
```

这是递归策略的扩展。在合并 A 和 B 时，如果 B 是 A 的子树，则首先更新 B 以反映 A 的树结构，同时也更新 A 和 B 共享的共同祖先树。

### 递归的 git merge策略选项

上文介绍的 "递归 "策略有自己的附加操作选项子集。

```
ours
```

不要与 "`Ours`"合并策略混淆。该选项通过偏向'`our`'版本来自动解决冲突。如果不冲突，'`theirs`'一方的改动会自动合并。

```
theirs
```

与 "`Ours`"战略相反，"`theirs`"方案在解决冲突时更倾向于外部合并分支。

```
patience
```

此选项花费额外的时间来避免不重要的匹配行的错误合并。当要合并的分支极度分歧时，最好使用此选项。

```
diff-algorithim
```

```bash
ignore-*

    ignore-space-change
    ignore-all-space
    ignore-space-at-eol
    ignore-cr-at-eol
```

一组针对空白字符的选项。任何与所传选项子集匹配的行都将被忽略。

```
renormalize
```

该选项在解决三向合并的同时，对所有 git 树进行签出和签入。该选项适用于合并不同签入/签出状态的分支。

```
no-normalize
```

禁用`renormalize`一化选项。这将覆盖`merge.renormalize`配置变量。

```bash
no-renames
```

该选项将在合并过程中忽略重命名的文件。

```
find-renames=n
```

这是默认行为。递归合并将遵循文件重命名。该`n`参数可用于传递重命名相似性的阈值。默认`n`值为`100%.`

```
subtree
```

该选项借鉴了 `subtree` 策略。该策略对两棵树进行操作，并修改如何使它们在共享祖先上匹配，而该选项则对树的路径元数据进行操作，使它们匹配。

### 总结

强烈倾向于使用显式合并。原因很简单：显式合并可为被合并的特性提供很好的可追溯性和上下文。在共享特性分支供审核之前进行本地历史清理重置是绝对值得鼓励的，但这根本不会改变政策。它只是对政策的补充。

# 比较 Git 工作流程

Git 是当今最常用的版本控制系统。Git 工作流程是如何使用 Git 以一致、高效的方式完成工作的秘诀或建议。Git 工作流程鼓励开发人员和 DevOps 团队有效、一致地使用 Git。Git 为用户管理变更提供了很大的灵活性。鉴于 Git 注重灵活性，因此在如何与 Git 互动方面并没有标准化的流程。在与团队合作开发 Git 管理的项目时，确保团队就如何应用变更流程达成一致非常重要。为确保团队意见一致，应制定或选择一个约定俗成的 Git 工作流程。有几种公开的 Git 工作流程可能很适合您的团队。在此，我们将讨论其中一些 Git 工作流程选项。

在工作场所实施 Git 时，可能会因为工作流程繁多而不知从何下手。本页通过调查软件团队最常见的 Git 工作流程，为您提供一个起点。

在阅读过程中，请记住这些工作流程只是指南，而非具体规则。我们希望向你展示各种可能，因此你可以根据自己的需求，混合搭配不同工作流程的各个环节。

## 什么是成功的 Git 工作流？

在为团队评估工作流程时，最重要的是考虑团队文化。您希望工作流程能提高团队效率，而不是成为限制工作效率的负担。评估 Git 工作流程时需要考虑以下几点

- 该工作流程是否可根据团队规模进行扩展？
- 该工作流程是否容易撤销错误？
- 该工作流程是否会给团队带来新的不必要的认知开销？

## 集中式工作流程

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/git-centralized-workflow-2x.png" style="zoom:50%;" />

对于从 SVN 过渡而来的团队来说，集中式工作流是一个很好的 Git 工作流。与 Subversion 类似，集中式工作流使用一个中央仓库作为项目所有变更的单一入口。默认的开发分支不是`trunk`，而是`main`，所有变更都提交到该分支。除`main`外，该工作流程不需要其他分支。

过渡到分布式版本控制系统看似是一项艰巨的任务，但你不必改变现有的工作流程，就能充分利用 Git 的优势。你的团队可以像使用 Subversion 一样开发项目。

不过，与 SVN 相比，使用 Git 支持开发工作流程有一些优势。首先，它为每个开发人员提供了整个项目的本地副本。这种隔离的环境让每个开发人员都能独立工作，不受项目中所有其他改动的影响--他们可以将提交添加到本地存储库，并完全忘记上游开发，直到对他们来说方便为止。

其次，它还能让你使用 Git 强大的分支和合并模型。与 SVN 不同，Git 分支的设计是一种安全可靠的机制，用于整合代码并在不同版本库之间共享变更。集中式工作流与其他工作流类似，都是利用远程服务器端托管的仓库，由开发人员推拉形成。与其他工作流相比，集中式工作流没有定义拉取请求或分叉模式。集中式工作流通常更适合从 SVN 迁移到 Git 的团队和规模较小的团队。

## How it works

开发人员从克隆中央版本库开始。在他们自己的项目本地副本中，他们编辑文件并提交更改，就像使用 SVN 一样；不过，这些新提交的内容都存储在本地，与中心版本库完全隔离。这样，开发人员就可以推迟向上游同步，直到达到方便的中断点。

要向正式项目发布变更，开发人员需要将本地主分支 "推送 "到中央版本库。这就相当于 `svn commit`，只不过它添加的是中心主分支中还没有的所有本地提交。

### 初始化中央存储库

首先，需要有人在服务器上创建中央资源库。如果是新项目，可以初始化一个空仓库。否则，就需要导入现有的 Git 或 SVN 仓库。

中心仓库应始终是裸仓库（不应有工作目录），创建方法如下：

```bash
ssh user@host git init --bare /path/to/repo.git
```

用户名请务必使用有效的 SSH 用户名，主机请使用服务器的域名或 IP 地址，`/path/to/repo.git` 请使用存储 `repo` 的位置。请注意，`.git` 扩展名通常会附加到版本库名称上，以表明这是一个裸版本库。

### 托管中央存储库

中心仓库通常是通过第三方 Git 托管服务（如 Bitbucket Cloud）创建的。托管服务会为你处理上述初始化裸仓库的过程。托管服务会为中央仓库提供一个地址，以便从本地仓库访问。

### 克隆中央存储库

接下来，每个开发人员都要创建整个项目的本地副本。这是通过 `git clone` 命令完成的：

```bash
git clone ssh://user@host/path/to/repo.git
```

当你克隆一个仓库时，Git 会自动添加一个名为 `origin` 的快捷方式，该快捷方式会指向 "父 "仓库，前提是你还想继续与它交互。

### 更改并提交

仓库克隆到本地后，开发人员就可以使用标准的 Git 提交流程进行修改：编辑、暂存和提交。如果你对暂存区还不熟悉，它是一种准备提交的方法，而不必包含工作目录中的所有改动。这样，即使本地改动很多，也能创建高度集中的提交。

```bash
git status # View the state of the repo
git add <some-file> # Stage a file
git commit # Commit a file</some-file>
```

请记住，由于这些命令创建的是本地提交，所以 John 可以随意重复这个过程，而不必担心中央版本库中的情况。这对于需要分解成更简单、更原子化的大功能来说非常有用。

### 将新提交推送到中央版本库

一旦本地版本库提交了新的变更。这些变更需要推送给项目中的其他开发人员共享。

```bash
git push origin main
```

该命令将把新提交的变更推送到中央版本库。在向中央仓库推送变更时，可能会出现之前推送的其他开发者的更新包含的代码与计划推送的更新冲突的情况。Git 会输出一条信息来说明这种冲突。在这种情况下，首先需要执行 `git pull`。这种冲突情况将在下一节中详述。

### 管理冲突

中央仓库代表着正式项目，因此它的提交历史应该被视为神圣不可侵犯的。如果开发者的本地提交与中央仓库有出入，Git 会拒绝推送他们的改动，因为这会覆盖官方提交。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/manage-confilct-2x.png" style="zoom:50%;" />

开发人员在发布自己的功能之前，需要获取更新后的中央提交，并将自己的更改重新加载到中央提交之上。这就好比在说："我想把我的改动添加到其他人已经完成的改动中"。结果就是一个完美的线性历史，就像传统的 SVN 工作流程一样。

如果本地改动与上游提交直接冲突，Git 会暂停重定向过程，给你一个手动解决冲突的机会。Git 的好处在于，它使用相同的 `git status` 和 `git add` 命令来生成提交和解决合并冲突。这让新开发者可以轻松管理自己的合并。此外，如果他们遇到麻烦，Git 还能让他们很容易地中止整个重置过程，然后再试一次（或者去寻求帮助）。

---------

## Example

让我们以一个典型的小型团队如何使用此工作流程进行协作为例。我们将看到两个开发人员，John和Mary，如何在不同的功能上工作，并通过一个集中的资源库共享他们的贡献。

### John致力于他的特色功能

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/john-and-his-feature.png" style="zoom:50%;" />

在他的本地存储库中，John 可以使用标准 Git 提交流程来开发功能：编辑、暂存和提交。

请记住，由于这些命令创建本地提交，John 可以根据需要多次重复此过程，而不必担心中央存储库中发生的情况。

### Mary致力于她的特色功能

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/marry-and-his-feature.png" style="zoom:50%;" />

与此同时，玛丽正在自己的本地版本库中使用相同的编辑/阶段/提交流程开发自己的功能。和约翰一样，她并不关心中央版本库中发生了什么，她也真的不关心约翰在本地版本库中做了什么，因为所有的本地版本库都是私有的。

### John推送他的工作内容

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/john-push-feature-2x.png" style="zoom:50%;" />

一旦约翰完成了他的功能，他就应该把本地提交发布到中央仓库，这样其他团队成员就可以访问它了。他可以使用 `git push` 命令这样做：

```bash
git push origin main
```

记住，`origin` 是约翰克隆中央仓库时 Git 创建的与中央仓库的远程连接。`main` 参数告诉 Git 尽量让 `origin` 的主分支看起来像他本地的主分支。由于中心仓库在约翰克隆后没有更新过，这不会导致任何冲突，推送也会如期进行。

### Mary推送他的工作内容

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/marry-push-fail-2x.png" style="zoom:50%;" />

让我们看看如果 Mary 在 John 成功将其更改发布到中心版本库后尝试推送她的功能会发生什么。她可以使用完全相同的推送命令：

```bash
git push origin main
```

但是，由于她的本地历史已经偏离了中央仓库，Git 会拒绝该请求，并给出一条相当冗长的错误信息：

```bash
error: failed to push some refs to '/path/to/repo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

这就防止了 Mary 覆盖官方提交。她需要将 John 的更新拉入自己的版本库，与本地变更整合，然后再试一次。

### Mary在John提交的基础上进行Rebase

Mary 可以使用 `git pull` 将上游的改动整合到自己的版本库中。这个命令有点像 svn update--它会把整个上游提交历史拉入 Mary 的本地仓库，并尝试将其与本地提交整合在一起：

```bash
git pull --rebase origin main
```

如下所示，`--rebase` 选项会告诉 Git 将 Mary 的所有提交与中心仓库的改动同步后移到主分支的顶端：

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-git-pull-rebase-2x.png" style="zoom:50%;" />

如果忘记了这个选项，`pull`仍然可以正常工作，但每次有人需要与中央版本库同步时，就会出现一个多余的“merge commit”。在这种工作流程中，最好是`rebase`而不是生成merge commit。

### Mary解决了合并冲突

`Rebasing`的工作原理是将每个本地提交逐一转移到更新后的主分支。这意味着你可以逐个提交地捕捉合并冲突，而不是在一次大规模的合并提交中解决所有冲突。这样就能让提交尽可能集中，使项目历史更加清晰。反过来，这也更容易找出引入错误的地方，并在必要时回滚更改，将对项目的影响降到最低。

重置的工作原理是将每个本地提交逐一转移到更新后的主分支。这意味着你可以逐个提交地捕捉合并冲突，而不是在一次大规模的合并提交中解决所有冲突。这样就能让提交尽可能集中，使项目历史更加清晰。反过来，这也更容易找出引入错误的地方，并在必要时回滚更改，将对项目的影响降到最低。

如果 Mary 和 John 正在开发不相关的功能，`Rebasing`过程不太可能产生冲突。但如果发生冲突，Git 会在当前提交处暂停重置，并输出以下信息和相关说明：

```
CONFLICT (content): Merge conflict in <some-file>
```

Git 的好处在于，任何人都可以解决自己的合并冲突。在我们的例子中，Mary 只需运行 `git status` 就能看到问题所在。有冲突的文件会出现在未合并路径部分：

```bash
# Unmerged paths:
# (use "git reset HEAD <some-file>..." to unstage)
# (use "git add/rm <some-file>..." as appropriate to mark resolution)
#
# both modified: <some-file>
```

然后，她会按照自己的喜好编辑文件。一旦她对结果感到满意，就可以按照通常的方式对文件进行暂存，然后让 `git rebase` 完成剩下的工作：

```bash
git add <some-file>
git rebase --continue
```

仅此而已。Git 会继续下一次提交，并对产生冲突的其他提交重复上述过程。

如果到了这一步，你发现自己完全不知道发生了什么，也不用惊慌。只要执行以下命令，就能回到起点：

```bash
git rebase --abort
```

#### Mary成功推送他的特色功能

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-success-push-2x.png" style="zoom:50%;" />

完成与中央版本库的同步后，玛丽就能成功发布她的更改：

```bash
git push origin main
```

## 何去何从？

如你所见，只需使用少量 Git 命令，就能复制传统的 Subversion 开发环境。这对于从 SVN 过渡到 Git 的团队来说是个不错的选择，但它并不能充分利用 Git 的分布式特性。

集中式工作流程非常适合小型团队。上文详述的冲突解决流程会随着团队规模的扩大而形成瓶颈。如果你的团队对集中式工作流程感到满意，但又想简化协作流程，那就绝对值得探索特性分支工作流程的好处。通过为每个功能指定一个独立的分支，可以在将新添加功能整合到正式项目之前发起深入讨论。

## 其他常见工作流程

集中式工作流本质上是其他 Git 工作流的构件。大多数流行的 Git 工作流程都会有某种集中式仓库，供开发人员推拉使用。下面我们将简要讨论一些其他流行的 Git 工作流程。这些扩展工作流在管理功能开发、热修复和最终发布的分支方面提供了更专业的模式。

### 功能分支

功能分支是集中式工作流程的逻辑延伸。特性分支工作流背后的核心理念是，所有特性开发都应在专用分支而非主分支中进行。这种封装方式可以让多个开发人员在不影响主代码库的情况下，轻松完成某个特定功能的开发。这也意味着主分支永远不会包含损坏的代码，这对于持续集成环境来说是一个巨大的优势。

### Gitflow 工作流程

Gitflow 工作流最早发表于 nvie 的 Vincent Driessen 于 2010 年发表的一篇备受推崇的博文中。Gitflow 工作流定义了一种严格的分支模型，以项目发布为中心进行设计。除了特性分支工作流所需的内容外，该工作流没有添加任何新的概念或命令。相反，它为不同的分支分配了非常具体的角色，并定义了它们之间的交互方式和时间。

### 分叉工作流程

分叉工作流与本教程中讨论的其他工作流有本质区别。它不使用单一的服务器端仓库作为 "中心 "代码库，而是为每个开发者提供一个服务器端仓库。这意味着每个贡献者拥有的不是一个，而是两个 Git 仓库：一个私有的本地仓库和一个公共的服务器端仓库。

## 指南

没有放之四海而皆准的 Git 工作流程。如前所述，开发一套能提高团队工作效率的 Git 工作流程非常重要。除了团队文化，工作流程还应该与企业文化相辅相成。分支和标签等 Git 功能应与企业的发布计划相辅相成。如果您的团队正在使用任务跟踪项目管理软件，您可能希望使用与进行中的任务相对应的分支。此外，在决定工作流程时还应考虑以下准则：

### 短期分支

分支与生产分支分离的时间越长，合并冲突和部署挑战的风险就越高。生命周期较短的分支能促进更简洁的合并和部署。

### 最大限度减少和简化还原

重要的是要有一个工作流程，帮助主动防止合并后必须进行还原。在将分支合并到主分支之前对其进行测试的工作流程就是一个例子。然而，意外总会发生。尽管如此，拥有一个允许轻松还原且不会扰乱其他团队成员工作流程的工作流程还是很有好处的。

### 与发布时间计划相匹配

工作流程应与企业的软件开发发布周期相辅相成。如果您计划每天发布多次版本，就需要保持主分支的稳定。如果发布频率较低，则可以考虑使用 Git 标签将分支标记为版本。

----------

# Git 功能分支工作流

特性分支工作流程的核心理念是，所有特性开发都应在专用分支而非main中进行。这种封装方式可以让多个开发人员在不影响主代码库的情况下，轻松完成某个特定功能的开发。这也意味着`main`永远不会包含损坏的代码，这对于持续集成环境来说是一个巨大的优势。

封装功能开发还可以利用拉取请求，这是一种围绕分支发起讨论的方式。拉取请求是围绕分支发起讨论的一种方式，它让其他开发人员有机会在功能集成到正式项目之前对其进行签字确认。或者，如果你被某个功能卡住了，也可以打开拉取请求，征求同事的建议。关键是，拉取请求能让团队成员非常容易地对彼此的工作发表评论。

Git 功能分支工作流是一个可组合的工作流，可被其他高级 Git 工作流利用。我们在 Git 工作流程概览页面讨论了其他 Git 工作流程。Git 特性分支工作流专注于分支模型，也就是说，它是一个管理和创建分支的指导框架。其他工作流程则更侧重于仓库。Git 特性分支工作流可以并入其他工作流。传统上，Gitflow 和 Git f分叉工作流都使用 Git 特性分支工作流作为分支模型。

## How it works

功能分支工作流程假定有一个中央存储库，主分支代表正式的项目历史。开发人员每次开始开发新功能时，都会创建一个新的分支，而不是直接在本地主分支上提交。特性分支应有描述性的名称，如 animated-menu-items 或 issue-#1061。这样做的目的是让每个分支都有一个清晰、高度集中的目的。Git 在技术上不区分主分支和特性分支，因此开发者可以对特性分支进行编辑、阶段化和提交修改。


此外，特性分支还可以（也应该）推送到中心仓库。这样，开发人员就可以在不接触任何正式代码的情况下与其他开发人员共享某个特性。由于主分支是唯一的 "特殊 "分支，因此在中心版本库中存储多个特性分支不会造成任何问题。当然，这也是备份每个人的本地提交的便捷方法。下面是一个特性分支的生命周期。

### 从主分支开始

所有特性分支都是根据项目的最新代码状态创建的。本指南假定这是在主分支中维护和更新的。

```bash
git checkout main
git fetch origin 
git reset --hard origin/main
```

### 创建存储库

这会将 repo 切换到主分支，提取最新提交，并重置 repo 的本地 main 副本以匹配最新版本。

### 创建新分支

为每个功能或问题创建一个单独的分支。创建分支后，在本地签出它，这样你所做的任何更改都会在该分支上进行。

```bash
git checkout -b new-feature
```

这会基于 main 检查出一个名为 new-feature 的分支，而 `-b` 标志会告诉 Git 如果该分支还不存在，就创建它。

### 更新、添加、提交和推送更改

在该分支上，按常规方式编辑、暂存和提交修改，根据需要提交尽可能多的改动。像任何时候使用 Git 一样，对功能进行修改和提交。准备就绪后，推送您的提交，更新 Bitbucket 上的功能分支。

```bash
git status
git add <some-file>
git commit
```

### 向远程推送功能分支

将特性分支推送到中央版本库是个好主意。这可以作为一个方便的备份，在与其他开发人员协作时，可以让他们查看对新分支的提交。

```bash
git push -u origin new-feature
```

该命令会将 new-feature 推送到远程中央仓库（`origin`），而 `-u` 标志会将其添加为远程跟踪分支。设置好跟踪分支后，就可以调用 `git push`，无需任何参数，自动将 new-feature 分支推送到中心仓库。要获得对新特性分支的反馈，可在 Bitbucket Cloud 或 Bitbucket Data Center 等版本库管理解决方案中创建一个拉取请求。在那里，你可以添加审核员，并在合并前确保一切顺利。

### 解决反馈

现在，队友们可以评论并批准推送的提交。在本地解决他们的意见，提交并将建议的修改推送到 Bitbucket。您的更新就会出现在拉取请求中。

### 合并您的拉取请求

在合并之前，如果其他人对 repo 进行了修改，你可能需要解决合并冲突。当你的拉取请求被批准且无冲突后，你就可以把代码添加到主分支中了。从 Bitbucket 中的拉取请求进行合并。

除了隔离功能开发外，分支还能通过拉取请求讨论变更。一旦有人完成了一项功能，他们不会立即将其合并到主分支中。相反，他们会将功能分支推送到中心服务器，并提交拉取请求，要求将他们添加的内容合并到主分支中。这样，其他开发人员就有机会在修改成为主代码库的一部分之前对其进行审查。

代码审查是拉取请求的一个主要优点，但实际上拉取请求的设计初衷是作为一种谈论代码的通用方式。你可以把拉取请求看作是专门针对某个分支的讨论。这意味着拉取请求也可以在开发过程的更早阶段使用。例如，如果开发人员在某个功能上需要帮助，他们只需提交拉取请求即可。相关人员会自动收到通知，并能在相关提交旁边看到问题。

一旦拉取请求被接受，发布功能的实际操作与集中式工作流程中的操作大致相同。首先，你需要确保本地主目录与上游主目录同步。然后，将特性分支合并到主版本中，并将更新后的主版本推送回中央版本库。

Bitbucket Cloud 或 Bitbucket Server 等产品仓库管理解决方案可为拉取请求提供便利。查看 Bitbucket Server 拉取请求文档，了解示例。

## 示例

下面是一个使用功能分支工作流程的示例。该场景是一个团队围绕新功能拉取请求进行代码审查。这只是该模型多种用途中的一个例子。

### 玛丽开始了一项新功能

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-new-feature-2x.png" style="zoom:50%;" />

在开始开发功能之前，Mary 需要一个独立的分支来工作。她可以使用以下命令申请一个新分支：

```bash
git checkout -b marys-feature main
```

这样就在 main 分支的基础上签出了一个名为 marys-feature 的分支，而 -b 标志则告诉 Git 如果该分支还不存在，就创建它。在这个分支上，Mary 按常规方式进行编辑、暂存和提交，并根据需要提交尽可能多的改动：

```bash
git status
git add <some-file>
git commit
```

### 玛丽去吃午饭

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-go-to-lauch-2x.png" style="zoom:50%;" />

玛丽在一上午的时间里为她的功能添加了一些提交。在她离开去吃午饭之前，最好把她的功能分支推送到中央仓库。这可以作为方便的备份，但如果 Mary 与其他开发人员协作，这也会让他们访问她的初始提交。

```bash
git push -u origin marys-feature
```

这条命令会将 marys-feature 推送到中心仓库（origin），而 `-u` 标志会将其添加为远程跟踪分支。设置好跟踪分支后，Mary 可以不加任何参数地调用 `git push` 来推送她的功能。

### 玛丽完成她的特色分支

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-finish-feature-2x.png" style="zoom:50%;" />

当 Mary 吃完午饭回来时，她完成了她的功能。在将其合并到主仓库之前，她需要提交一个拉取请求，让团队其他成员知道她已经完成了。但首先，她要确保中央仓库中有她的最新提交：

```bash
git push
```

然后，她在 Git GUI 中提交拉取请求，要求将 marys-feature 合并到 main 中，团队成员就会自动收到通知。拉取请求的好处在于，它们会在相关提交的旁边显示注释，因此很容易就能提出有关特定变更集的问题。

### Bill收到拉取请求

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/bill-pull-request-2x.png" style="zoom:50%;" />

比尔收到`pull`请求并查看了 marys-feature。他决定在将其整合到正式项目之前做一些修改，于是他和玛丽通过`pull`请求进行了一些来回交流。

### 玛丽进行更改

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/mary-makes-changes-2x.png" style="zoom:50%;" />

为了进行更改，Mary 采用了与创建第一个功能迭代完全相同的流程。她进行编辑、暂存、提交，并将更新推送到中央版本库。她的所有活动都会显示在pull请求中，而Bill仍然可以在整个过程中发表评论。

如果Bill愿意，他可以将 marys-feature 拉入他的本地版本库，然后自己继续工作。他添加的任何提交也会显示在pull请求中。

### 玛丽发布特色功能

一旦Bill准备好接受`pull`请求，就需要有人将该功能合并到稳定项目中（这可以由Bill或Mary完成）：

```bash
git checkout main
git pull
git pull origin marys-feature
git push
```

这一过程通常会导致合并提交。有些开发人员喜欢这样做，因为这就像是将特性与代码库的其他部分象征性地结合在一起。不过，如果你偏爱线性历史，也可以在执行合并之前将特性重置到 `main` 的顶端，从而实现快进合并。

有些图形用户界面会自动执行拉取请求接受流程，只需点击 "`Accept` "按钮即可运行所有这些命令。如果你的 GUI 没有这样做，它至少能在特性分支合并到主分支时自动关闭拉取请求。

与此同时，John也在做着同样的事情。

当Mary和Bill在研究 marys-feature 并在她的拉取请求中进行讨论时，John正在他自己的特性分支中做着完全相同的事情。通过将特性功能隔离到不同的分支中，每个人都可以独立工作，但在必要时与其他开发人员共享变更仍是轻而易举的事。

## Summary

在本文档中，我们讨论了 Git 功能分支工作流程。该工作流有助于组织和跟踪专注于业务领域功能集的分支。其他 Git 工作流，如 Git Forking 工作流和 Gitflow 工作流，都是以 repo 为中心的，可以利用 Git 功能分支工作流来管理它们的分支模型。本文档演示了用于实现 Git 功能分支工作流的高级代码示例和虚构示例。与功能分支工作流程建立的一些关键关联是：

-  专注于分支模式
- 可被其他面向仓库的工作流程利用
- 通过拉取请求和合并审查促进与团队成员的协作

在功能分支的审核和合并阶段使用`git rebase`，可以创建一个有凝聚力的 Git 历史记录。功能分支模型是促进团队协作的绝佳工具。

----------

# Gitflow 工作流

`Gitflow` 是一种传统的 Git 工作流程，最初是一种管理 Git 分支的颠覆性新策略。如今，`Gitflow` 已不再受欢迎，而基于主干的工作流程已被视为现代持续软件开发和 `DevOps` 实践的最佳实践。在 `CI/CD` 中使用 `Gitflow` 也很有挑战性。本文章将详细介绍 `Gitflow` 的历史。

## 什么是 Gitflow？

`Gitflow` 是另一种 Git 分支模式，包括使用特性分支和多个主分支。它由 nvie 网站的 Vincent Driessen 首次发布并流行开来。与基于主干的开发模式相比，`Gitflow` 的分支数量多、寿命长、提交量大。在这种模式下，开发人员创建一个特性分支，并推迟将其合并到主干分支，直到特性完成。这些生命周期较长的特性分支需要更多协作才能合并，偏离主干分支的风险也更高。它们还可能带来相互冲突的更新。

`Gitflow` 可用于有计划发布周期的项目和 `DevOps` 最佳实践--持续交付。除特性分支工作流程外，该工作流程不添加任何新概念或命令。相反，它为不同的分支分配了非常具体的角色，并定义了它们应该如何以及何时进行交互。除了功能分支，它还使用单个分支来准备、维护和记录发布。当然，您还可以利用特性分支工作流的所有优点：拉取请求、隔离实验和更高效的协作。

## How it works

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/gitflow-workflow-2x.png" style="zoom:50%;" />

### 开发和主要分支

这种工作流程使用两个分支来记录项目的历史，而不是一个`main`分支。`main`分支存储正式发布的历史，而开发分支则作为功能的集成分支。在`main`分支中的所有提交都标上版本号也很方便。

第一步是用 `develop` 分支来补充默认的 `main` 分支。一个简单的方法是由一名开发人员在本地创建一个空的 `develop` 分支，然后推送到服务器：

```bash
git branch develop
git push -u origin develop
```

该分支将包含项目的完整历史，而主分支将包含简略版本。其他开发者现在应该克隆中央仓库，并为 `develop` 创建一个跟踪分支。

使用 git-flow 扩展库时，在现有仓库上执行 `git flow init` 将创建 `develop` 分支：

```bash
$ git flow init


Initialized empty Git repository in ~/project/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [main]
Branch name for "next release" development: [develop]


How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []


$ git branch
* develop
 main
```

## 功能分支

### 步骤 1. 创建存储库

每个新功能都应该有自己的分支，可以推送到中央仓库进行备份/协作。但是， `feature` 分支不是从`main`分支中分离出来，而是将 `develop` 分支作为其父分支。当特性完成后，它会被合并回`develop` 分支。功能分支绝不能直接与`main`分支交互。

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/gitflow-feature-banch-2x.png" style="zoom:50%;" />

请注意， `feature` 分支与 `develop` 分支的结合，就是功能分支工作流程。但是，`Gitflow` 的工作流程并不止于此。

 `feature` 分支通常是根据最新的 `develop` 分支创建的。

### 创建功能分支

**不使用 git-flow 扩展**

```bash
git checkout develop
git checkout -b feature_branch
```

**使用 git-flow 扩展时**

```bash
git flow feature start feature_branch
```

继续工作，像平常一样使用 Git。

### 完成功能分支

完成功能分支的开发工作后，下一步就是把 `feature_branch` 合并到 `develop` 中。

不使用 git-flow 扩展

```bash
git checkout develop
git merge feature_branch
```

使用 git-flow 扩展

```bash
git flow feature finish feature_branch
```

### 发布分支

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/release-banches.png" style="zoom:50%;" />

一旦`develop`分支完成了足够的功能等待发布（或者预定的发布日期即将到来），就从 `develop`分支中分叉出一个 `release` 分支。创建该分支会启动下一个发布周期，因此在此之后就不能再添加新功能了，只有错误修复、文档生成和其他面向发布的任务应放在该分支中。一旦准备好发布，`release`分支就会被合并`main` 分支并标注版本号。此外，还应将其合并回`develop`分支，因为`develop`分支在发布分支后可能已经取得了进展。

使用专用分支来准备发布，可以让一个团队在完善当前发布的同时，让另一个团队继续为下一个发布开发功能。它还能创建定义明确的开发阶段（例如，可以轻松地说："本周我们要准备 4.0 版"，并在版本库的结构中实际看到它）。

创建`release`分支是另一种直接的分支操作。与 `feature` 分支一样，`release`分支也基于 `develop` 分支。可以使用以下方法创建新的`release`分支。

不使用 git-flow 扩展：

```bash
git checkout develop
git checkout -b release/0.1.0
```

使用 git-flow 扩展时：

```bash
$ git flow release start 0.1.0
Switched to a new branch 'release/0.1.0'
```

一旦`release`版本准备就绪，就会将其合并到`main` 和`develop`分支中，然后删除`release`分支。合并回开发分支很重要，因为关键更新可能已添加到发布分支，它们需要能访问新功能。如果你的组织强调代码审查，这里将是拉取请求的理想位置。

要完成分支发布，可使用以下方法：

不使用 git-flow 扩展：

```bash
git checkout main
git merge release/0.1.0
```

或者使用 git-flow 扩展：

```bash
git flow release finish '0.1.0'
```

## 热修复分支

<img src="https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/2024/git/hotfix-banches-2x.png" style="zoom:50%;" />

维护或 "热修复 "分支用于快速修补生产版本。Hotfix 分支与 `release` 分支和 `feature` 分支很相似，只是它们基于 `main` 分支而不是 `develop`分支。这是唯一应该直接从`main`分支分叉出来的分支。修复完成后，应立即将其并入`main`分支和 `develop`分支（或当前的`release` 分支），并在`main`分支上标注更新的版本号。

为错误修复设置专门的开发线，可以让团队在不影响其他工作流程或等待下一个发布周期的情况下解决问题。你可以把维护分支看作是直接与主分支协同工作的临时发布分支。可以使用以下方法创建热修复分支：

不使用 git-flow 扩展：

```bash
git checkout main
git checkout -b hotfix_branch
```

使用 git-flow 扩展时：

```bash
$ git flow hotfix start hotfix_branch
```

与完成`release`分支类似，`hotfix`分支会合并到`main` 分支和`develop`分支

```bash
git checkout main
git merge hotfix_branch
git checkout develop
git merge hotfix_branch
git branch -D hotfix_branch
```

```bash
$ git flow hotfix finish hotfix_branch
```

## Example

演示功能分支流程的完整示例如下。假设我们有一个包含`main`分支的版本库。

```bash
git checkout main
git checkout -b develop
git checkout -b feature_branch
# work happens on feature branch
git checkout develop
git merge feature_branch
git checkout main
git merge develop
git branch -d feature_branch
```

除功能分支和发布分支流程外，热修复示例如下：

```bash
git checkout main
git checkout -b hotfix_branch
# work is done commits are added to the hotfix_branch
git checkout develop
git merge hotfix_branch
git checkout main
git merge hotfix_branch
```

这里我们讨论的是 Gitflow 工作流。Gitflow 是您和您的团队可以使用的多种 Git 工作流程之一。

了解 Gitflow 的一些要点如下:

- 该工作流非常适合基于发布的软件工作流。
- Gitflow 为热修复到生产提供了专用通道。

Gitflow 的整体流程是:

1. 从 `main`分支创建一个 `develop` 分支

2. 从 `develop`分支创建 `release` 分支

3. 从 `develop`分支创建 `Feature` 分支

4.  `feature` 完成后，合并到 `develop` 分支中

5.  `release` 分支完成后，合并到 `develop` 分支和 `main`分支中

6. 如果在 `main`分支中发现问题，就会从 `main`分支创建 `hotfix` 分支

7. 一旦完成热修复，它就会被合并到 `develop` 分支和 `main`分支中

------

# Forking工作流

`Forking`工作流与其他流行的 Git 工作流有本质区别。它不使用单一的服务器端仓库作为 "中央 "代码库，而是让每个开发者都拥有自己的服务器端仓库。这意味着每个贡献者拥有的不是一个，而是两个 Git 仓库：一个私有的本地仓库和一个公共的服务器端仓库。`Forking Workflow`最常见于公共开源项目。

 `Forking`工作流的主要优势在于，无需每个人都推送到一个中央仓库，就能整合贡献。开发人员推送到自己的服务器端版本库，只有项目维护者才能推送到官方版本库。这样，维护者就可以接受任何开发者的提交，而无需给予他们写入官方代码库的权限。

Forking工作流程通常遵循基于 Gitflow 工作流程的分支模型。这意味着完整的功能分支将被合并到原始项目维护者的仓库中。这样就形成了一个分布式工作流程，为大型有机团队（包括不受信任的第三方）提供了一种灵活的安全协作方式。这也使其成为开源项目的理想工作流程。 

## How it works

与其他 Git 工作流程一样，Forking Workflow也是从服务器上存储的官方公共仓库开始的。但当新的开发者想开始项目工作时，他们不会直接克隆官方仓库。

相反，他们会分叉官方版本库，在服务器上创建一个副本。这个新副本将作为他们的个人公共仓库--其他开发者不得向其推送，但他们可以从中提取修改（稍后我们将了解这一点的重要性）。创建完服务器端副本后，开发人员会执行`git clone`将其复制到本地计算机上。就像其他工作流程一样，这也是他们的私人开发环境。

当他们准备好发布本地提交时，就会把提交推送到自己的公共仓库--而不是官方仓库。然后，他们向主版本库提交拉取请求，让项目维护者知道更新已准备好集成。如果贡献的代码有问题，拉取请求还可以作为方便的讨论线程。下面是这一工作流程的分步示例。

1. 开发者 "forks"一个 "官方"服务器端版本库。这将创建他们自己的服务器端副本
2. 将新的服务器端副本克隆到他们的本地系统
3. 将 "官方 "仓库的 Git 远程路径添加到本地克隆中
4. 创建新的本地特性分支
5. 开发人员对新分支进行修改
6. 为更改创建新提交
7.  分支会被推送到开发者自己的服务器端副本
8. 开发人员从新分支向 "官方 "版本库打开拉取请求
9. 拉取请求被批准合并，并被合并到原来的服务器端版本库中

要将功能集成到正式代码库中，维护者会将贡献者的修改拉入本地版本库，检查确保不会破坏项目，将其合并到本地主分支，然后将主分支推送到服务器上的正式版本库。现在，该贡献已成为项目的一部分，其他开发人员应从官方版本库中提取，以同步他们的本地版本库。

重要的是要明白，在Forking工作流程中，"官方 "版本库的概念只是一种约定。事实上，官方版本库之所以如此官方，是因为它是项目维护者的公共版本库。

## Forking vs cloning

需要注意的是，"forked"仓库和"forking"都不是特殊的操作。Forked仓库是使用标准的`git clone`命令创建的。分叉仓库一般是 "服务器端克隆"，通常由第三方 Git 服务（如 Bitbucket）管理和托管。创建分叉仓库没有唯一的 Git 命令。克隆操作本质上是复制一个仓库及其历史。

## Forking 工作流程中的分支

所有这些个人公共仓库实际上只是一种与其他开发者共享分支的便捷方式。就像特性分支工作流和 Gitflow 工作流一样，每个人都应该继续使用分支来隔离单个特性。唯一的区别在于如何共享这些分支。在Forking工作流中，它们会被拉入其他开发者的本地版本库，而在特性分支和 Gitflow 工作流中，它们会被推送到官方版本库。

## Fork a repository

所有加入 Forking Workflow 项目的新开发者都需要 fork 官方仓库。如前所述，fork只是一个标准的`git clone`操作。可以通过 SSH 登录服务器，然后运行`git clone`将其复制到服务器上的另一个位置。流行的 Git 托管服务（如 Bitbucket）提供的版本库fork功能可自动完成这一步骤。

## Clone your fork

假设使用 Bitbucket 托管这些版本库，项目中的开发人员就应该拥有自己的 Bitbucket 账户，并克隆他们fork的版本库副本：

```bash
git clone https://user@bitbucket.org/user/repo.git
```

## Adding a remote

其他 Git 工作流使用一个指向中央仓库的 `origin` 远程地址，而分叉工作流则需要两个远程地址--一个指向官方仓库，另一个指向开发者的个人服务器端仓库。虽然你可以随心所欲地调用这些远程地址，但常见的惯例是使用 `origin` 作为分叉仓库的远程地址（运行 `git clone` 时会自动创建），而使用 `upstream` 作为官方仓库的远程地址。

```bash
git remote add upstream https://bitbucket.org/maintainer/repo
```

您需要使用上述命令自行创建`upstream`远程仓库。这样你就能轻松地随着官方项目的进展更新本地版本库。请注意，如果你的`upstream`版本库启用了身份验证（即它不是开源的），你就需要提供一个用户名，就像这样：

```bash
git remote add upstream https://user@bitbucket.org/maintainer/repo.git
```

这要求用户在克隆或拉取官方代码库之前提供有效的密码。

## Working in a branch: making & pushing changes

在开发人员的分叉存储库的本地副本中，他们可以编辑代码、提交更改并创建分支，就像在其他 Git 工作流程中一样：

```bash
git checkout -b some-feature # Edit some code git commit -a -m "Add first draft of some feature"
```

他们的所有更改都将完全私有，直到他们将其推送到公共存储库为止。而且，如果官方项目已经向前推进，他们可以使用 `git pull` 访问新的提交：

```bash
git pull upstream main
```

由于开发人员应该在专用的功能分支中工作，因此这通常会导致快进合并。

## Making a pull request

一旦开发人员准备好分享他们的新功能，他们需要做两件事。首先，他们必须将自己的贡献推送到公共仓库，让其他开发者也能访问。他们的远程源应该已经设置好了，所以他们只需做以下几件事

```bash
git push origin feature-branch
```

这与其他工作流程的不同之处在于，origin remote指向开发者的个人服务器端版本库，而不是主代码库。

其次，他们需要通知项目维护者，他们想把自己的功能合并到官方代码库中。Bitbucket 提供了一个 “pull request”按钮，点击后会弹出一个表单，要求你指定要将哪个分支合并到正式版本库中。通常情况下，你会希望将你的功能分支整合到上游远程的`main`分支中。

## Summary

简而言之，`Forking`工作流程常用于公共开源项目。`Forking`是在项目 repo 的服务器副本上执行的`git clone`操作。`Forking`工作流程通常与 Bitbucket 等 Git 托管服务结合使用。`Forking`工作流程的一个高级示例是

1. 您想为一个托管在 bitbucket.org/userA/open-project 的开源库作出贡献

2. 使用 Bitbucket 创建一个 repo 的 fork 到 bitbucket.org/YourName/open-project

3. 在本地系统上执行 git clone on https://bitbucket.org/YourName/open-project 以获得该 repo 的本地副本

4. 在本地仓库中创建一个新的 `feature` 分支

5. 完成新功能的工作，并执行`git commit`保存更改

6. 然后将新 `feature` 分支推送到远程forked仓库

7. 使用 Bitbucket，在 bitbucket.org/userA/open-project 上针对原 repo 打开新分支的拉取请求。

Forking工作流能帮助项目维护者向任何开发者开放版本库，而无需手动管理每个贡献者的授权设置。这就为维护者提供了更多 "pull"式的工作流程。Forking工作流最常用于开源项目，也可用于私有业务工作流，以便对合并到发布版本中的内容进行更权威的控制。这对于有部署经理或严格发布周期的团队非常有用。
