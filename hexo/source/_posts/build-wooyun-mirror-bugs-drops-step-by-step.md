---
title: 一步一步搭建Wooyun镜像站并用Nginx映射到外网IP
tags: [wooyun镜像,nginx,乌云知识库]
---

### 0x00：下载Wooyun镜像
- [镜像仓库](https://github.com/hanc00l/wooyun_public)

### 0x01：下载并安装VMWare虚拟机

### 0x02：解压并用VMware虚拟机打开解压后的镜像
 - 将虚拟机重启后重新获取IP地址
 - 输入用户名hancool和密码qwe123

### 0x03：运行Elasticsearch全文检索
进入虚拟机`elasticsearch-2.3.4/bin`目录下运行`./elasticsearch -d`

### 0x04：运行Wooyun主程序
进入`wooyun_public/flask`目录，运行`./app.py`

### 0x05：使用Wooyun镜像
打开浏览器，输入http://ip:5000，ip为虚拟机的网卡地址(使用`ifconfig eth0`查看)
- ![Wooyun](/images/wooyun-mirror.png)

### 0x06：映射到外网
外网Nginx配置
```
server {
  listen 外网端口;
  location / {
    proxy_pass http://192.168.36.128:5000/;
  }
}
```
访问`http://外网IP:外网端口`即可

### 0xFF：常见错误

#### 安装后搜索报500
 - 进入`elasticsearch-2.3.4/bin`目录下运行`./elasticsearch -d` (-d表示以后台方式运行)

#### 搜索结果超过500页报500错误
 - elasticsearch默认的最大分页数是10000条记录(也就是500页)，因此超过500页会报错。
 - 修改默认最大分页记录
 ``` bash
 curl -XPUT "http://localhost:9200/wooyun/_settings" -d '{ "index" : { "max_result_window" : 500000 } }'
 ```

 ## 致敬Wooyun！