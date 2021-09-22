---
title: 解决Mysql5.7一直重启Restarting(1)
date: 2021-09-22 16:16:44
tags: 
- mysql
- vagrant
- linux
categories: mysql
---

# 发现问题

优于长时间没进Linux连接mysql，今天进入Linux连接mysql，使用`docker ps`，mysql的状态一直是Restarting (1) x seconds ago，mysql一直在重启无法连接

查看日志

```sh
docker logs mysql
```

发现是内存不足

```asciiarmor
......
2021-06-14T11:41:26.576589Z 0 [ERROR] InnoDB: Error number 28 means 'No space left on device'
......
```

使用 df -h 查看 发现是 /var/docker/overlay2里面的东西占了很大的空间

![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3mysql%E4%B8%80%E7%9B%B4%E9%87%8D%E5%90%AF/linuxdfh.png)

检查内存占用情况，发现vagrant占用36G，一路找下去，发现到application data目录无线循环，上网搜索发现了问题所在。

**ls查看发现里的东西很多和我本机C盘里的文件一模一样 AppData之类的 为什么这些东西会在虚拟机里 虚拟机同步了C盘里的内容 百度发现 vagrant提供了将本机**

**目录挂载到虚拟机目录下的功能，默认是将vagrant配置文件所在目录挂载到虚拟机/vagrant目录下。**

**也就是说 我的VagrantFile 是 C/user/username/VagrantFile 那么所有和VagrantFile同级的 C/user/username/里面的内容会全部同步到vagrant中 导致虚拟机内存爆满**

# 解决办法

1. 在和Vagrantfile同级目录（C:\Users\Administrator\）创建一个自己的文件夹,我的叫VagrantSyncFolder

2. 然后打开目录到C:\Users\Administrator\.vagrant.d\boxes\centos-VAGRANTSLASH-7\2004.01\virtualbox

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3mysql%E4%B8%80%E7%9B%B4%E9%87%8D%E5%90%AF/vagrantfile.png)

3. 打开Vagrantfile，

   config.vm.synced_folder ".", "/vagrant", type: "rsync"

   修改为

   config.vm.synced_folder "./vagrantCache", "/vagrant", type: "rsync"

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3mysql%E4%B8%80%E7%9B%B4%E9%87%8D%E5%90%AF/changevagrantfile.png)

4. 修改完这个之后,vagrant reload发现还是不行,猜测可能只有vagrant成功重启之后修改的这个映射才能生效,但是已经占用100%不能成功重启,没办法,只能删东

   西了

   查看了一下vagrant目录里文件的大小

   ```bash
   du -sh *
   ```

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3mysql%E4%B8%80%E7%9B%B4%E9%87%8D%E5%90%AF/cdvagrant.png)

   其中AppData很大

   试着删了一下3D Objects文件里的一些东西,发现文件里的文件并没有影响,ok,直接将AppData删了,释放了很大的空间，然后exit退出

   vagrant reload重启，大功告成！

   ![](https://myblob-pics.oss-cn-hangzhou.aliyuncs.com/%E8%A7%A3%E5%86%B3mysql%E4%B8%80%E7%9B%B4%E9%87%8D%E5%90%AF/vagrantreload.png)



其实这种情况，可能在使用vagrant  up命令的时候就会报Rsync 错误，可以参考这篇文章，其问题和上方的一样

[Windows 10 上的 CentOS 7 Vagrant 框出现 Rsync 错误](https://www.dissmeyer.com/2020/02/11/issue-with-centos-7-vagrant-boxes-on-windows-10/)