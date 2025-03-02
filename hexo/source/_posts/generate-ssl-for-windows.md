---
title: 利用OpenSSL在Window10生成自签名证书
tags: [OpenSSL,nginx]
---

#### 0x00: 下载并安装OpenSSL
 - 下载[OpenSSL](https://slproweb.com/products/Win32OpenSSL.html)
    
    ![OpenSSL](/images/download-openssl.png)
 - 运行安装目录下`bin`目录下的`openssl.exe`

    ![RunOpenSSL](/images/run-openssl.png)

#### 0x01: 生成证书
  - 生成`.key`文件：
    - 输入`genrsa -des3 -out server.key 2048`后再输入密码,我这里设置的是`123456`
  - 生成`.csr`文件
    - 输入`req -new -key server.key -out server.csr`后再输入刚刚填写的`123456`密码
  - 填写基本信息
  
  ![GenerateSSL](/images/generate-ssl.png)

#### 0x02: 删除密钥对应的密码,防止nginx每次启动输入密码
  `rsa -in server.key -out test.com.key`

#### 0x03: 生成crt证书
  `x509 -req -days 365 -in server.csr -signkey test.com.key -out test.com.crt`
  ![ssl](/images/ssl.png)

#### 0x04: 配置hosts文件以及nginx
``` bash
  127.0.0.1       test.com
```
``` nginx
  server {
    listen 443;
    server_name  test.com;
    ssl on;
    # 证书crt路径
    ssl_certificate test.com.crt;
    # 证书key路径
    ssl_certificate_key test.com.key;
    ssl_session_timeout 5m;
    ssl_prefer_server_ciphers on;
    location / {
        root   html;
        index  index.html index.htm;
    }
  }
  ```