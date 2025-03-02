---
title: 利用FontSpider压缩网页字体静态文件
tags: [字体压缩,FontSpider]
---

### 0x00：安装FontSpider
``` bash
npm install font-spider -g
```
Tips:确保本地已安装[NodeJS](https://nodejs.org/zh-cn/download/current/)

### 0x01：新建HTML文件[myFont.html]
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <style>
    @font-face {
      font-family: 'myFont';
      src: url('/myFont.eot');
      src:
        url('./myFont.eot') format('embedded-opentype'),
        url('./myFont.woff') format('woff'),
        url('./myFont.ttf') format('truetype'),
        url('./myFont.svg') format('svg');
    }

    h1 {
      font-family: 'myFont';
      font-size: 40px;
      font-weight: bold;
    }
  </style>
</head>

<body>
  <h1>网页中存在的所有文字信息,网页中存在的所有文字信息</h1>
</body>

</html>
```
Tips：
- `@font-face` 中的 `src` 定义的 `.ttf` 文件必须存在，其余的格式将由工具自动生成。
- FontSpider会去除网页中`未使用`的文字以达到减小ttf文件大小的效果,而这部分去除的文字`只会`采用`本地`存在的字体`样式`。

### 0x02：运行压缩命令
``` node
  font-spider myFont.html
```
![结果](/images/font-spider.png)

### 0x03：参考
- [FontSpider](http://font-spider.org/)