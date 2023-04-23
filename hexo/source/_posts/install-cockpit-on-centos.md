---
title: CentOS安装Cockpit管理主机
tags: [CentOS,Cockpit,Docker]
---

#### 0x00: 介绍
[Cockpit](https://cockpit-project.org/)是一个免费且开源的基于web的管理工具，系统管理员可以执行诸如存储管理、网络配置、检查日志、管理容器等任务。 通过Cockpit提供的友好的Web 前端界面可以轻松地管理我们的GNU/Linux 服务器，非常轻量级，Web 界面也非常简单易用。 更重要的是通过Cockpit可以实现集中式管理。
#### 0x01: 安装
  ``` bsah
  sudo yum install cockpit
  ```
#### 0x02: 启动
  ``` bsah
  sudo systemctl enable --now cockpit.socket
  ```
#### 0x03: 防火墙设置
  ``` bsah
  sudo firewall-cmd --permanent --zone=public --add-service=cockpit
  sudo firewall-cmd --reload
  ```
#### 0x04: 查看
打开浏览器访问`https://服务器IP地址:9090`,输入服务器的用户名和密码即可。

#### 0x05: 插件
##### 安装所有插件(懒人办法)
``` bash
  yum -y install cockpit-*
```
##### Docker插件
``` bash
  yum install cockpit-docker -y
```
##### 管理磁盘
``` bash
  yum install -y cockpit-storaged
```
##### 管理多台主机
``` bash
  yum install -y cockpit-dashboard
```
#### 0x06: 参考
- [Running Cockpit](https://cockpit-project.org/running.html)
- [Github](https://github.com/cockpit-project/cockpit)