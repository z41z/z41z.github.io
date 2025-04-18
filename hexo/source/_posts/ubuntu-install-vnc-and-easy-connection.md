---
title: Ubuntu16.04安装VNC和EasyConnect
tags: [Ubuntu,VNC,EasyConnect,Xlib XInputExtension]
---

### 更新软件源
#### 0x00： 备份软件源
``` bash
  sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak 
```
#### 0x02：修改软件源
  ``` bash
  sudo vim /etc/apt/sources.list
  ```
  将文件内容替换为
  ``` bash
  deb-src http://archive.ubuntu.com/ubuntu xenial main restricted
  deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial multiverse
  deb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse
  deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
  deb http://archive.canonical.com/ubuntu xenial partner
  deb-src http://archive.canonical.com/ubuntu xenial partner
  deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted
  deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
  deb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse
  ```
#### 0x03：更新
  ``` bash
  sudo apt-get update && sudo apt-get upgrade
  ```

### 安装VNC
  #### 0x00：安装vnc服务端
  ``` bash
  sudo apt-get install vnc4server
  ```
  安装完成后输入`vncserver`设置VNC密码
  ``` bash
  vncserver
  ```
  #### 0x01：连接VNC
  用[VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/windows/)连接VNC，默认端口`5901`
  ![连接VNC](/images/vnc-viewer-connect.png)

  #### 0x02：修改VNC端口
  查看`vncserver`路径
  ``` bash
  which vncserver
  ```
  ![which-vncserver](/images/which-vncserver.png)
  查找vnc配置,一直输入`file`命令直到显示`/usr/bin/vnc4server: a /usr/bin/env perl script, ASCII text executable`
  ![which-vncserver](/images/vnc-file.png)
  查找端口配置的行序号
  ``` bash
  grep "59" /usr/bin/vnc4server -n
  ```
  ![vnc-find-port](/images/vnc-find-port.png)
  我这里是第`212`和`468`行
  编辑`vnc`配置对应的行,将`5900`修改为自己的端口(我这里设置为`6660`)。
  ``` bash
  sudo vi /usr/bin/vnc4server
  ```
  ![vnc-find-port](/images/vnc-change-port.png)
  重启VNC
  ``` bash
  vncserver -kill :1
  ```
  ``` bash
  vncserver :1
  ```
  用[VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/windows/)再次连接VNC
  ![vnc-new-port.png](/images/vnc-new-port.png)

  #### 0x03：安装桌面环境
  安装桌面基础
  ``` bash
  sudo apt-get install x-window-system-core
  ```
  安装登陆管理器
  ``` bash
  sudo apt-get install gdm
  ```
  安装Ubuntu的桌面
  ``` bash
  sudo apt-get install ubuntu-desktop
  ```
  安装gnome配套
  ``` bash
  sudo apt-get install gnome-panel gnome-settings-daemon metacity nautilus gnome-terminal
  ```
  #### 0x04：修改VNC配置
  编辑`vim ~/.vnc/xstartup`,将内容修改为
  ``` bash
  #!/bin/sh
  # Uncomment the following two lines for normal desktop:
  export XKL_XMODMAP_DISABLE=1
  unset SESSION_MANAGER
  # exec /etc/X11/xinit/xinitrc
  unset DBUS_SESSION_BUS_ADDRESS
  gnome-panel &
  gnome-settings-daemon &
  metacity &
  nautilus &
  gnome-terminal &
  ```
  重启VNC
  ``` bash
  vncserver -kill :1
  ```
  ``` bash
  vncserver :1
  ```
  #### 0x05：连接VNC服务器
  用[VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/windows/)连接VNC
  ![vnc-gnome.png](/images/vnc-gnome.png)

### 安装EasyConnect
#### 下载安装包
下载地址:[EasyConnect_x64_7_6_7_3.deb](http://uploads.shybey.com/EasyConnect_x64_7_6_7_3.deb)
#### 安装EasyConnect
``` bash
# 默认安装位置/usr/share/sangfor/EasyConnect/EasyConnect
sudo dpkg -i EasyConnect_x64_7_6_7_3.deb
```
#### 运行EasyConnect
``` bash
cd /usr/share/sangfor/EasyConnect/EasyConnect
./EasyConnect
```

#### Xlib XInputExtension错误解决
``` bash
# 备份
sudo cp /usr/lib/x86_64-linux-gnu/libxcb.so.1 libxcb.so.1.bk
# 写入内容
sudo sed -i 's/BIG-REQUESTS/_IG-REQUESTS/' libxcb.so.1
```

#### 运行并登陆EasyConnect
``` bash
cd /usr/share/sangfor/EasyConnect/EasyConnect
./EasyConnect
```
![vnc-gnome.png](/images/easyconnect-login.png)
