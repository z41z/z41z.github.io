---
title: 利用Zerotier组网实现不同网络环境的主机互通互联
tags: [CentOS,Zerotier,组网,内网穿透]
---

#### 0x00：登录/注册
打开[ZeroTier](https://my.zerotier.com/)对应操作。
#### 0x01：进入个人中心
登录即可。
#### 0x02：创建虚拟网络
<div align=left>![创建虚拟网络](/images/20230422/create.png)
#### 0x03：安装客户端
<div align=left>![安装客户端](/images/20230422/install.png)
 - CentOS
 ``` bash
 curl -s https://install.zerotier.com | sudo bash
 ```
 - Windows
 `Windows`直接安装[下载](https://www.zerotier.com/download/)包。注意：`Windows 7`和`Server 2012`安装[ZeroTier 1.6.6](https://download.zerotier.com/RELEASES/1.6.6/dist/ZeroTier%20One.msi)

#### 0x04：加入虚拟网络
``` bash
sudo zerotier-cli join b607网络IDc65d2b09
```
`Windows`直接界面化操作即可。授权通过后`ifconfig`会绑定上对应的IP地址。
<div align=left>![加入虚拟网络](/images/20230422/join.png)

#### 0x05：授权
前面复选框选中即可授权通过。
<div align=left>![授](/images/20230422/manage.png)
另：还可以建立多个路由，为不同的设备设置固定的内网IP。

#### 0x06：测试连通性
<div align=left>![测试连通性](/images/20230422/ping.png)