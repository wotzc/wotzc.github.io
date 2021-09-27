---
title: SpringBoot项目报端口冲突的错误
date: 2021-09-27 08:38:58
tags:
- Springboot
- 端口冲突
categories: SpringBoot
---

最近运行Springboot项目是，报了一个端口号被占用的错

```xml
Description:

Web server failed to start. Port 10000 was already in use.

Action:

Identify and stop the process that's listening on port 10000 or configure this application to listen on another port.
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/springbooterr.png)

报错原因：端口被占用。

解决办法

1.使用cmd命令查看端口号占用情况，例如查看端口10000，可以看出进程号为5372；

```
netstat -ano | findstr 10000
```

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/findport.png)

2.关闭该进程

方法一： 使用命令关闭

命令：

```bash
taskkill /F /pid 5372 
```

如果是非管理员用户可能报拒绝访问的错误

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/confusetoaccess.png)

重新以管理员的身份打开cmd窗口，重新运行以上的关闭进程命令

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/opencmd.png)

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/killtaskpidZ.png)

方法二：使用任务管理器关闭：

菜单栏 -> 右键 - > 任务管理器 -> 详细信息，根据PID排序找到PID为5372的进程，选择后点击结束任务。

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/Springboot%E7%AB%AF%E5%8F%A3%E5%86%B2%E7%AA%81/killtask.png)