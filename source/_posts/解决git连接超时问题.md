---
title: 解决git连接超时问题
date: 2021-08-18 18:17:05
tags:
- git
- github
categories: git
---

 最近使用git连接github时一直报下面这个错：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/gitpushproblem.png)

git 连接远程仓库有两种协议：**ssh协议**和**https协议**。

根据英文可以看出，**ssh协议**连接超时，我们切换成 https协议连接github。

移除掉远程仓库的配置

```
$ git remote rm origin
```

重新添加新的远程仓库，以https的形式：

```
git remote add origin https://github.com/wotzc/wotzc.github.io.git
```

https的地址其实就是github上项目对应的地址，如下图：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/githubaddress.png)

再次尝试push代码，可以看到已经成功。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/gitpushsuccess.png)

但是我们在部署`hexo d`的时候输入账号密码后还是会报错，错误如下：

```
Password authentication is temporarily disabled as part of a brownout. Please use a personal access token instead [duplicate]
```

因为**2021年8月13日之后密码身份验证由 GitHub 禁用，不再支持。创建并使用个人访问令牌PAT而不是密码。**

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/gitauthentication.png)

此消息意味着您使用密码而不是个人访问令牌通过 HTTPS 访问 GitHub，这已不再允许。GitHub 禁用了密码身份验证，因为人们经常会意外泄露密码，虽然个人访问令牌可以限制损坏，但密码不能。

如果您没有在提示时明确输入密码，那么您很可能有一个凭据管理器来保存您的密码，并在不提示您的情况下发送密码。

所以我们需要使用个人访问令牌进行身份验证，步骤如下：

1. 在 Github 上创建个人访问令牌

   From your Github account, go to **Settings** => **Developer Settings** => **Personal Access Token** => **Generate New Token**  (Give your password) => **Fillup the form** => click **Generate token** => **Copy the generated Token**, it will be something like `ghp_sFhFsSHhTzMDreGRLjmks4Tzuzgthdvfsrta`

2. 对于Windows操作系统

   从**控制面板**中访问**凭证管理器**，选择**Windows凭据**，找到**git:https://github.com**，点击**编辑**，把上一步复制的密码粘贴到密码处。

3. 对于Mac操作系统

Click on the Spotlight icon (magnifying glass) on the right side of the menu bar. Type **Keychain access** then press the Enter key to launch the app => In Keychain Access, search for => Find the **internet password** entry for => Edit or delete the entry accordingly => You are done`github.com`

现在将给定记录存入计算机以记住令牌：

```
$ git config --global credential.helper cache
```

如果在进行`hexo d`操作时，还是提示下面这种错误：

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/hexoderror.png)

我们就修改根目录的_config.yml文件，把repo修改为https连接的地址

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3git%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6%E9%97%AE%E9%A2%98/configyml.png)

再次hexo d成功！