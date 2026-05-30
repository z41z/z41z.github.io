---
title: CentOS上通过fdisk分区并挂载硬盘
tags: [CentOS,fdisk,分区,CentOS挂载硬盘,腾讯云挂载硬盘,腾讯轻量云挂载]
---

  #### 0x00：查看新增的数据盘
  ``` bash
  fdisk -l
  ```
  <div align=left>![查看新增的数据盘](/images/20230422/fdisk-l.png)
  `/dev/vdb`为新增的数据盘
  ##### 0x01：分区
  ``` bash
  fdisk /dev/vdb
  n
  p
  1
  2048
  41943039
  p
  w
  ```
  也可以依次输入`n`、`回车`、`回车`、`回车`、`回车`、`w`直接使用默认值。
<div align=left>![分区](/images/20230422/fdisk-n.png)
#### 0x02：同步分区
  ``` bash
  partprobe
  ```
  <div align=left>![同步分区](/images/20230422/partprobe.png)
#### 0x03：设置文件系统
  ``` bash
  mkfs -t ext4 /dev/vdb1
  ```
  <div align=left>![设置文件系统](/images/20230422/mkfs.png)
#### 0x04： 挂载目录
  ``` bash
  mkdir /disk
  mount /dev/vdb1 /disk
  ```
  <div align=left>![挂载目录](/images/20230422/mount.png)
#### 0x05：参考
- [腾讯轻量云挂载硬盘](https://cloud.tencent.com/document/product/1207/81981)