---
layout: post
title: Linux可视化及远程连接项目实践
slug: my-first-awesome-post
date: 2020-02-03 12:00
status: publish
author: 雨眠纸
categories: 
  - Maverick
tags: 
  - 博客
  - GitHub
  - Linux
excerpt: 在Linux服务器上启动可视化服务
---

## 项目概括
**在Linux服务器上启动可视化服务并提供vnc远程连接**

## 基本内容

* Linux上安装服务及配置
* 远程连接及使用
* 客户端安装

#### Linux安装

> centos 7 使用yum -y install vnc4server 提示没有这个软件包，使用apt-get install vnc4server提示没有apt-get命令

> 需要安装图形界面yum groups install "GNOME Desktop"或"X Window System"

* 防火墙策略
```
firewall-cmd --permanent --add-service vnc-server
systemctl restart firewalld.service
```
* 可视化桌面
```
yum groups install "GNOME Desktop"
```
* 安装vncserver
```
yum -y install tigervnc-server
```

#### 更改配置

* 复制文件
```
cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@\:1.service
vim /etc/systemd/system/vncserver@\:1.service
```
* 定位到
```
ExecStart=/usr/sbin/runuser -l <USER> -c "/usr/bin/vncserver %i"
PIDFile=/home/<USER>/.vnc/%H%i.pid
```
* 修改为登陆账户（比如root）
```
ExecStart=/usr/sbin/runuser -l root -c "/usr/bin/vncserver %i"
PIDFile=/root/.vnc/%H%i.pid
```
* 设置密码
```
vncpasswd
```
* 启动服务
```
systemctl enable vncserver@:1.service 
systemctl start vncserver@:1.service #或者 vncserver :1
```
* 查看是否正常启动
```
ps -ef | grep vnc
```
* 查看端口号
```
vncserver -list    #X DISPLAY 表示连接的端口号
```

* 关闭服务
```
systemctl stop vncserver@:1.service  #或者 vncserver -kill :1
```
#### 客户端安装
自行前往https://www.realvnc.com/en/connect/download/viewer/

#### 其他问题：
* 防火墙问题（centos 7下）

原方法中采用关闭防火墙的方式，现在采用对防火墙中加入服务
```
firewall-cmd --permanent --add-service vnc-server
systemctl restart firewalld.service
```

* 分辨率更改问题（未解决）

编辑配置文件
```
vim /etc/systemd/system/vncserver@\:1.service
ExecStart=/sbin/runuser -l root -c "/usr/bin/vncserver %i -geometry 800x600" #会导致黑屏
```
* 重启服务
```
systemctl daemon-reload 
```
* 增加用户问题
```
cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:2.service
systemctl daemon-reload
```
* 启动服务
```
systemctl start vncserver@:2.service #或者 vncserver :2
```
* 关闭服务
```
systemctl stop vncserver@:2.service  #或者 vncserver -kill :2
```


<u>文章使用 GitHub 管理，并通过 GitHub Actions 自动构建与发布！</u>
