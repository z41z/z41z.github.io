---
title: 通过Windows10注册表修改系统服务自启动方式
tags: [Windows Defender,regedit,注册表,WinDefend]
---

#### 0x00: 打开注册表
`Win`+`R`输入`regedit`

#### 0x01: 注册表路径
``` bash
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
```

#### 0x02: 设置Windows Defender启动方式
 - 打开`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WinDefend`
 - 修改`Start`值为对应的启动方式
  > Start的值设置为0，则驱动由启动引导器加载，应该跟“随着开机，最先启动”是同一回事；
  Start的值设置为1，则驱动由操作系统的I/O子系统加载，即在系统内核初始化时加载；
  Start的值设置为2，则驱动/服务在启动后自动加载；
  Start的值设置为3，则驱动/服务就是按需手动加载；
  Start的值设置为4，驱动/服务就是被禁用的状态