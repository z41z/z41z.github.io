---
title: CentOS编译安装Nginx并启用Brotli压缩和HTTP2
tags: [nginx,CentOS,nginx编译,Brotli,HTTP2,SSL]
---

#### 0x00：编译并安装Nginx
##### 下载Nginx包
``` bash
wget http://nginx.org/download/nginx-1.21.0.tar.gz
```
##### 安装依赖
``` bash
yum -y install gcc gcc-c++ automake zlib zlib-devel \
openssl openssl-devel pcre pcre-devel
```
##### 解压
``` bash
tar -zxvf nginx-1.21.0.tar.gz
```

##### 生成Makefile
进入解压后的文件夹运行
``` bash
./configure
```

##### 编译安装
``` bash
make
make install
```
Tips:编译后的文件路径`/usr/local/nginx`
##### 添加软链
``` bash
ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```

#### 0x01：启用Brotli压缩和HTTP2

##### 安装Git
``` bash
yum -y install git
```
##### 安装OpenSSL
``` bash
yum -y install openssl openssl-devel
```

##### 下载Brotli模块
``` bash
git clone https://github.com/google/ngx_brotli.git
```

##### 编译Nginx并添加Brotli模块和http_ssl_module模块
``` bash
cd ./ngx_brotli && git submodule update --init && cd /usr/local/nginx-1.21.0
./configure --add-dynamic-module=./ngx_brotli --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_v2_module --with-stream --with-http_sub_module
make install
```

#### 修改nginx.conf并启用Brotli
``` nginx
# 配置文件中添加
worker_processes  1;
load_module modules/ngx_http_brotli_filter_module.so;
load_module modules/ngx_http_brotli_static_module.so;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    brotli on;
    brotli_comp_level 6;
    brotli_buffers 16 8k;
    brotli_min_length 20;
    brotli_types text/plain text/css application/json text/javascript image/jpeg image/png;

    server {
       # 启用HTTP2
       listen       443 ssl http2;
       server_name  localhost;
       ssl_certificate /usr/local/nginx-1.21.0/cert/test.crt;
       ssl_certificate_key /usr/local/nginx-1.21.0/cert/test.key;
       ssl_session_timeout 5m;
       ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
       ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
       ssl_prefer_server_ciphers on;

       location / {
           root   html;
           index  index.html index.htm;
       }
    }

}

```
####

##### 运行Nginx
``` bash
nginx
```

#### 0x02：结果
![ssl](/images/20210617/nginx-br-h2.png)