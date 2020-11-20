---
title: hexo多设备管理
date: 2020-11-16 23:15:35
tags:
- 博客
- hexo
categories: 博客
---

### 如何在不同设备快速、高效的管理自己的博客？

这里采用分支的方法，在一个Github Pages项目上，实现不同终端的管理工作。

实现原理：
在项目上创建一个分支，暂定为hexo分支。
master分支和hexo分支互不干扰：

- master分支存放hexo编译后的文件，是用来生成网页的
- Hexo分支源文件

机制是这样的，由于`hexo d`上传部署到github的其实是hexo编译后的文件，是用来生成网页的，不包含源文件。也就是上传的是在本地目录里自动生成的`.deploy_git`里面。其他文件 ，包括我们写在source 里面的，和配置文件，主题文件，都没有上传到github。所以可以利用git的分支管理，将源文件上传到github的另一个分支即可。

### 上传分支

首先，先在github上新建一个hexo分支，然后在这个仓库的settings中，选择默认分支为hexo分支（这样每次同步的时候就不用指定分支，比较方便）。

然后在本地的任意目录下，打开git bash：

> ​    git clone git@github.com:wotzc/wotzc.github.io.git    

将其克隆到本地，因为默认分支已经设成了hexo，所以clone时只clone了hexo。

接下来在克隆到本地的`wotzc.github.io`中，把除了.git 文件夹外的所有文件都删掉，把之前我们写的博客源文件全部复制过来，除了`.deploy_git`。这里应该说一句，复制过来的源文件应该有一个`.gitignore`，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git：

```text
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

如果你之前克隆过theme中的主题文件，那么应该把将博客目录下 themes 文件夹下每个主题文件夹里面的 .git .gitignore 删掉。，因为git不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了。

cd 到博客目录，git add -A ，git commit -m "--"，git push origin hexo，将博客目录下所有文件更新到 hexo 分支。如果上一步没有删掉 .git .gitignore，主题文件夹下内容将传不上去。至此原电脑上的操作结束。

### 新设备上的操作

在新电脑上操作，先把新电脑上环境安装好一样的，跟之前的环境搭建一样。

- 安装git

```text
sudo apt-get install git
```

- 设置git全局邮箱和用户名

```text
git config --global user.name "yourgithubname"
git config --global user.email "yourgithubemail"
```

- 设置ssh key

```text
ssh-keygen -t rsa -C "youremail"
#生成后填到github和coding上（有coding平台的话）
#验证是否成功
ssh -T git@github.com
ssh -T git@git.coding.net #(有coding平台的话)
```

- 安装nodejs

```text
sudo apt-get install nodejs
sudo apt-get install npm
```

- 安装hexo

```text
sudo npm install hexo-cli -g
```

但是已经不需要初始化了，直接在任意文件夹下

```text
git clone git@github.com:wotzc/wotzc.github.io.git
```

然后进入克隆到的文件夹：

```text
cd xxx.github.io
npm install
npm install hexo-deployer-git --save
```

生成，部署：

```text
hexo g
hexo d
```

然后就可以开始写你的新博客了

```text
hexo new newpage
```

### 日常更新

以后无论在哪台电脑上，更新以及提交博客，依次执行，git pull，git add -A ，git commit -m "--"，git push origin hexo，hexo clean && hexo g && hexo d 即可。