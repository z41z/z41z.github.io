---
title: Windows环境下编译FFmpeg
tags: [FFmpeg,MinGW,SDL,x264,yasm]
---

### 0x00: FFmpeg概述
FFmpeg 是一个自由软件，可以运行音频和视频多种格式的录影、转换、流功能，包含了libavcodec(这是一个用于多个项目中音频和视频的解码器库)，以及libavformat(一个音频与视频格式转换库)。 “FFmpeg”这个单词中的“FF”指的是“Fast Forward”。 via [维基百科](https://zh.wikipedia.org/zh-cn/FFmpeg) 

### 0x01：下载及准备
- [FFmpeg](https://ffmpeg.org/download.html)(https://ffmpeg.org/download.html)
- [yasm](http://yasm.tortall.net/Download.html)(http://yasm.tortall.net/Download.html)
- [SDL](http://www.libsdl.org/download-1.2.php)(http://www.libsdl.org/download-1.2.php)
- [x264](https://www.videolan.org/developers/x264.html)(https://www.videolan.org/developers/x264.html)
- [MinGW](https://osdn.net/projects/mingw/releases/68260)(https://osdn.net/projects/mingw/releases/68260)

### 0x02：环境说明
- 操作系统：Windows 10家庭版64位

### 0x03：下载并安装[MinGW](https://osdn.net/projects/mingw/releases/68260)
![下载MinGW](/images/download-mingw.png)

### 0x04：运行MinGW并安装基础包
![运行MinGW并安装基础包](/images/mingw-basic-setup.png)
![运行MinGW并安装基础包](/images/mingw-basic-setup-apply.png)

>Tips:安装错误继续即可，然后再重新“Apply Changes”，或者退出再安装未安装的包。


### 0x05：下载[yasm](http://yasm.tortall.net/Download.html)
![下载yasm](/images/download-yasm.png)

### 0x06：将下载完成的yasm可执行文件文件名修改为为yasm并放入MinGW安装目录下的bin子文件夹下
![0x04：将下载完成的yasm可执行文件文件名修改为为yasm并放入MinGW安装目录下的bin子文件夹下](/images/rename-yasm.png)


### 0x07：打开MinGW安装目录下的msys\1.0\msys.bat

### 0x08：编译[x264](https://www.videolan.org/developers/x264.html)
 - #### 解压下载完成的x264文件
 - #### 在msys终端中切换到解压的目录并编译
  ``` bash
    ./configure --enable-shared --disable-asm --prefix=填写输出目录
  ```
  ``` bash
  make
  ```
  ``` bash
    make install
  ```

### 0x09：编译[SDL](http://www.libsdl.org/download-1.2.php)
 - #### 解压下载完成的SDL文件
 - #### 在msys终端中切换到解压的目录并编译
  ``` bash
    ./configure --prefix=填写输出目录
  ```
  ``` bash
    make
  ```
  ``` bash
    make install
  ```

### 0x10：将编译好的x264和SDL中bin、lib、include内所有文件拷贝到MinGW对应的文件夹下

### 0x11：在msys终端中切换到解压的目录

### 0x12：下载[FFmpeg](https://ffmpeg.org/download.html)源代码并解压到指定位置
![下载FFmpeg](/images/download-ffmpeg.png)

### 0x13：编译FFmpeg

``` bash
  ./configure --enable-shared --disable-static --enable-libx264 --enable-sdl2 --enable-gpl --enable-nonfree --prefix=填写输出目录
```
成功后执行命令
``` bash
  make
```
> Tips:可能会发生报错， error: 'ERROR_NOT_ENOUGH_MEMORY' undeclared (first use in this function)，打开`libavformat/os_support.h`, 添加`#include <winerror.h>`再执行`./configure --enable-shared --disable-static --enable-libx264 --enable-sdl2 --enable-gpl --enable-nonfree --prefix=填写输出目录`

最后执行命令
```
make install
```

### 0xFF：参考
- [FFmpeg README](https://ffmpeg.zeranoe.com/builds/readme/win64/static/ffmpeg-20190727-47b6ca0-win64-static-readme.txt)
- [专题：Windows编译x264、SDL、faac、ffmpeg过程](https://www.easydarwin.org/article/Streaming/40.html)