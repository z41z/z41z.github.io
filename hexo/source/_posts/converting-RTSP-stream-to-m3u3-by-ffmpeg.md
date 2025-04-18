---
title: Windows环境下通过FFmpeg将RTSP视频流转换m3u8直播流
tags: [FFmpeg,RTSP,HLS,m3u8,videojs]
---

### 0x00：背景说明
监控摄像头很多都是都过RTSP视频流对外开放，并不能够很友好的支持浏览器网页播放，因此需通过第三方工具将RTSP转换成m3u8格式。最后通过[video.js](https://videojs.com/)并播放转换后的流数据。

### 0x01：转换
``` bash
ffmpeg.exe -fflags nobuffer -rtsp_transport tcp -i "RTSP视频流地址" -flags +cgop -g 30 -vf scale=-1:360 -hls_flags delete_segments 转换后的m3u8地址
```
>例如：ffmpeg -fflags nobuffer -rtsp_transport tcp -i "rtsp://test" -flags +cgop -g 30 -vf scale=-1:360 -hls_flags delete_segments test.m3u8

#### 参数说明
 - -fflags nobuffer 减少初始输入流分析期间缓冲引入的延迟。
 - -rtsp_transport tcp 转换成TCP协议
 - -i 要转换的视频流地址
 - -flags +cgop -g 30图像组
 - -vf scale=-1:360 设置分辨率360P
 - -hls_flags delete_segments 在段的持续时间加上播放列表的持续时间之后的一段时间之后删除从播放列表中删除的段文件。

### 0x02 使用video.js播放视频流

#### 前端代码

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>RTSP to m3u8</title>
    <link rel="stylesheet" href="./videojs.css">
    <style>
      body{
        display: flex;
      }
    </style>
  </head>
  <body>
    <video-js id=video width=600 height=400 class="vjs-default-skin" controls>
      <source src="/test.m3u8" type="application/x-mpegURL">
    </video-js>
    <script src="video.js"></script>
    <script src="videojs-http-streaming.min.js"></script>
    <script>
        var player = videojs('video');
        player.play();
    </script>
  </body>
</html>
```
>Tips:必须以`HTTP/HTTPS`的方式打开html文件。
### 0x03：注意事项

#### 花屏解决
- 下载[FFmpeg](https://ffmpeg.org/download.html)源代码，将`libavformat`目录下的`udp.c`中的`UDP_MAX_PKT_SIZE`值修改为`655360`
- [重新编译FFmpeg](/post/compiling-ffmpeg-on-windows10)

### 0xFF：参考及下载
- [FFmpeg参数文档](https://ffmpeg.org/ffmpeg-formats.html)
- [Demo源代码](/files/rtsp-to-m3u8.zip)
- [FFmpeg源代码](https://ffmpeg.org/download.html)
- [Windows环境下编译FFmpeg](/post/compiling-ffmpeg-on-windows10)
