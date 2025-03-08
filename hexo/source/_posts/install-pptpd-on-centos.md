---
title: CentOS上安装PPTP代理
tags: [CentOS,PPTP,PPTPD,iptables,mtu]
---

#### 0x00：安装PPTP
#### 0x01：配置用户
#### 0x02：安装PPTPD
#### 0x03：设置时区
#### 0x04：连接
#### 0x05：参考
- [Centos7 搭建PPTP-VPN 服务](https://me.jinchuang.org/archives/517.html/comment-page-2)

netsh interface ipv4 show subinterfaces
netsh interface ipv4 set subinterface "连接名" mtu=值 store=persistent
netsh interface ipv4 set subinterface "连接名" mtu=值 store=persistent