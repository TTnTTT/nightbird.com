---
title: 我常用的Linux命令
date: 2020-5-12 12:38:51
updated: 2020-10-1
index_img: /img/blog_img.jpg
categories:
- [基础,计算机基础]
- [基础,Linux]
tags:
- 计算机基础
- Linux
---

# github搜索项目：
`language:java stars:>1000 pushed:>2019-10-1 size:>=5000 location:china 微服务 in:(name/readme/description) `
# git统计每个人增删行数：
> `git log --format='%aN' | sort -u | while read name;
 do echo -en "$name\t";
  git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; 
  done`
> git查看个人代码量：（改username）
git log --author="username" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -
'''
# MySQL
**在新版的mysql5.7的版本中，如果DISTINCT和order by一起使用将会报3065错误，sql语句无法执行。最新的mysql5.7版本语法比之前5.6版本语法更加严格导致的。DISTINCT和order by都会对数据进行排序操作，所以会产生冲突。**
*linux找不到你想要的目录时：1、man rpm 2、rpm -qa|grep 查找文件名3、 find / -name 查找文件名-print *

***

## 测试远程主机的端口是否在使用并且开放的：
telnet  122.51.6.249 8080（注意这个只能检测在使用的端口）

# 进程
## 一、常用命令总结 (Process Status)
   * ps -l   列出与本次登录有关的进程信息；
   * ps -aux   查询内存中进程信息；
   * ps -aux | grep ***   查询***进程的详细信息；
   * top   查看内存中进程的动态信息；
   * kill -9 pid   通过pid杀死进程。
## 找某个进程占用端口：
  * 先查看进程pid(如查看mysql)：ps -ef|grep mysql
  * 再找pid对应端口(如pid=15452)：netstat -anp | grep 15452
  * 查看具体某个端口是否占用(如8080)：netstat –apn | grep 8080
  * 查看所有端口开放情况：lsof -i : 端口号(如果没有任何输出则说明没有开启该端口号)
# 更改权限的两种方式
## 一、chmod u+x 文件名
身份： u=用户 g= 组 o=其他 a=所有身份
修改： +：添加 ， -：去掉
权限： r :读 w:写 x:执行
## 二、chmod 777
r=4
w=2
x=1

# 端口对应的几个状态的含义

1、**listening**状态：意思是FTP服务启动后du处于侦听状态，就是说该端口是开zhi放的，等待连接，但还没有被dao连接。

2、 **closing**状态：意思是端口人为或者放火墙使其关闭，也许服务被卸载。

3、**time wait**状态：意思是结束了这次连接。说明端口曾经有过访问，但访问结束了。

4、**established**状态：意思是建立连接。表示两台机器正在通信。处于ESTABLISHED状态的连接一定要格外注意，因为它也许不是个正常连接。

# npm为了加快下载依赖速度
```npm install --registry=https://registry.npm.taobao.org```

# apt和apt-get的区别
==apt = apt-get、apt-cache 和 apt-config 中最常用命令选项的集合。==
**apt和apt-get命令之间的区别**
虽然 apt 与 apt-get 有一些类似的命令选项，但它并不能完全向下兼容 apt-get 命令。也就是说，可以用 apt 替换部分 apt-get 系列命令，但不是全部。

|apt 命令|	取代的命令	|命令的功能|
|--|--|--|
apt install|	apt-get install	|安装软件包
apt remove|	apt-get remove	|移除软件包
apt purge	|apt-get purge	|移除软件包及配置文件
apt update|	apt-get update|	刷新存储库索引
apt upgrade	|apt-get upgrade|	升级所有可升级的软件包
apt autoremove|	apt-get autoremove|	自动删除不需要的包
apt full-upgrade|	apt-get dist-upgrade|	在升级软件包时自动处理依赖关系
apt search	|apt-cache search|	搜索应用程序
apt show|	apt-cache show|	显示安装细节

|新的apt命令|	命令的功能|
|--|--|
apt list|	列出包含条件的包（已安装，可升级等）
apt edit-sources|	编辑源列表
