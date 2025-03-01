---
title: Nginx常用代理场景及常用配置
tags: [nginx,proxy_pass,minio,client_max_body_size,upstream,SPA,rewrite,动态代理,大文件上传,反向代理,负载均衡,单页应用,禅道,Github,Gitlab,V2EX,Google,Websocket,WSS,WS]
---

#### 0x00: 常见代理
  - 代理允许跨域
  ``` nginx
    location /api_cros {
      proxy_set_header 'Access-Control-Allow-Origin' '*';
      proxy_pass http://127.0.0.1:8080/;
    }
  ```
  - 代理minio
  ``` nginx
    location /api_minio {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header Host 127.0.0.1:9000;
      rewrite ^/api_minio/(.*)$ /$1 break;
      proxy_pass http://127.0.0.1:9000/;
    }
  ```
  - 代理百度地图Web接口
  ``` nginx
    location ^~/api_baidu_map {
      rewrite  ^/api_baidu_map/(.*)$ /$1 break;
      proxy_pass http://api.map.baidu.com;
    }
  ```
  - 动态代理IP类接口
  ``` nginx
    location /{
      proxy_set_header Host $http_host;
      proxy_redirect off;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Scheme $scheme;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      if ( $args ~ ^url\=(.*) ) {
        set $url $1;
        proxy_pass $url;
      }
    }
  ```
    调用：http://127.0.0.1/api?url=http://127.0.0.1:8080/api/getList?pageSize=10&pageNum=1

  - 代理超图3D
  ``` nginx
    location / {    
      proxy_hide_header X-Frame-Options;
      add_header X-Frame-Options "";    
      proxy_pass http://127.0.0.1:8090/;
    }
  ```

  - 代理Auth2
  ``` nginx
		location /api {
		 if ($request_method = 'OPTIONS') {
          add_header Access-Control-Allow-Origin $http_origin;
          add_header Access-Control-Allow-Headers $http_access_control_request_headers;
          return 200;
        } 
        proxy_pass http://127.0.0.1:8082/;
    }
  ```

  - 代理MySQL
  ``` nginx
		stream {
      server {
        listen 13306;
        proxy_pass 114.114.114.114:3306;
      }
    }
  ```

  - 代理科达地图
  ``` nginx
		location / {
			proxy_set_header Accept-Encoding "" ;
			sub_filter_once off;
			sub_filter_types *;
			sub_filter 'KDMAP_SERVER_ADDRESS' 'http://127.0.0.1:44444';
			proxy_pass KDMAP_SERVER_ADDRESS;
		}
  ```

  - 代理禅道
  ``` nginx
    location / {
      proxy_set_header Host $host:$server_port;
      proxy_set_header Referer $http_referer;
      proxy_pass 禅道地址;
      proxy_set_header Accept-Encoding "" ;
      sub_filter_once on;
      sub_filter_types *;
      sub_filter '</body>' '</body><script>document.querySelector("#zentao")&&document.querySelector("#zentao").click()</script>';
    }
  ```

  - 代理Gitlab
  ``` nginx
    location / {
      proxy_set_header Accept-Encoding "" ;
      sub_filter_once off;
      sub_filter_types *;
      sub_filter 'Gitlab地址' '新地址';
      proxy_pass Gitlab地址;
    }
  ```
  - 代理海康威视ISC
  ``` nginx
    server {
      listen 443;
      server_name hikvision.test.com;
      add_header Access-Control-Allow-Origin *;
      ssl_certificate /etc/letsencrypt/hikvision.test.com.pem;
      ssl_certificate_key /etc/letsencrypt/hikvision.test.com.key;
      ssl_session_timeout 5m;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
      ssl_prefer_server_ciphers on;
      location / {
        proxy_set_header Host 172.26.197.197;
        proxy_pass https://172.26.197.197:2443;
      }

      location /media {
        proxy_pass http://172.26.197.197:559/media;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header Host 172.26.197.197:559;
      }
    }
  ```

  - Github加速
  ``` nginx
    location / {
      root html;
      index index.html index.htm;
      proxy_set_header Host github.com;
      proxy_hide_header Strict-Transport-Security;
      proxy_set_header Accept-Encoding "" ;
      proxy_hide_header Content-Security-Policy;
      sub_filter_once off;
      sub_filter_types *;
      sub_filter 'github.githubassets.com' '新地址';
      rewrite ^/(.*) /$1 break;
      proxy_pass https://www.github.com;
    }
  ```

  - Google加速并屏蔽CSDN
  ``` nginx
    location / {
      proxy_set_header Accept-Encoding "";
      proxy_set_header cookie "";
      sub_filter_once off;
      sub_filter_types *;
      sub_filter 'blog.csdn.net' '新地址/404';
      sub_filter 'onmousedown' 'onmousedowncancel';
      proxy_pass https://www.google.com;
    }
  ```

  - V2EX
  ``` nginx
    location / {
      proxy_set_header Accept-Encoding "" ;
      sub_filter_once off;
      sub_filter_types *;
      sub_filter 'top.location.href' 'v2ex.com';
      sub_filter 'https://cdn.v2ex.com' '/v2excdn';
      proxy_pass https://www.v2ex.com;
    }
    location /v2excdn {
      proxy_pass https://cdn.v2ex.com/;
    }
  ```

  - 代理WS并将协议升级WSS
  ``` nginx
    http {
      map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
      }
      server {
        listen 443 ssl http2;
        server_name 域名;
        ssl_certificate 证书.pem;
        ssl_certificate_key 证书.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
        ssl_prefer_server_ciphers on;

        location /ws {
          # 原Websocket地址为 ws://127.0.0.1:60018
          proxy_pass http://127.0.0.1:60018;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection $connection_upgrade;
          proxy_set_header Host $host;
        }
      }
    }
  ```

#### 0x01: 常用配置
  - 大文件上传
  ``` nginx
    client_max_body_size 800m;
  ```
  - gzip压缩
  ``` nginx
    gzip on;
    gzip_min_length 1000;
    gzip_buffers 4 32k;
    gzip_proxied any;
    gzip_types text/plain text/css text/javascript image/jpeg image/gif image/png application/json;
    gzip_vary on;
  ```
  - 单页应用(SPA)
  ``` nginx
    location / {
      index index.html;
      #单页路由刷新404
      try_files $uri $uri/ /index.html;
    }
  ```
  - 多服务实例(负载均衡)
  ``` nginx
    upstream API {
      ip_hash;
      server 127.0.0.1:8080 weight=7;
      server 127.0.0.1:8081;
      server 127.0.0.1:8082;
    }
  ```
  - 字符串替换
  ``` nginx
      sub_filter_once off;
      sub_filter_types *;
      sub_filter 'baidu.com' 'test.baidu.com';
  ```
  - rewrite
  ``` nginx
        location / {
          rewrite /order(.*)$ /order.php$1 last;
      };
  ```

  - SSL
  ``` nginx
    server {
      listen 443 ssl;
      server_name ssl.siping.one;
      access_log logs/ssl.siping.one.access.log;
      error_log logs/ssl.siping.one.error.log;
      root /frontend/mobile/next/dist;
      ssl_certificate /usr/local/nginx/cert/ssl.siping.one.crt;
      ssl_certificate_key /usr/local/nginx/cert/ssl.siping.one.key;
      ssl_session_timeout 5m;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
      ssl_prefer_server_ciphers on;
      location / {
        index index.html index.htm;
        try_files $uri $uri/ /index.html;
      }
    };
  ```
  - 启用Brotli压缩
  ``` nginx
      brotli on;
      brotli_comp_level 6;
      brotli_buffers 16 8k;
      brotli_min_length 20;
      brotli_types text/plain text/css application/json text/javascript image/jpeg image/png;
  ```
    参考：[Nginx编译`Brotli`](/post/compile-nginx-on-centos)
  - 重定向
  ``` nginx
    server {
      listen 80;
      server_name ssl.siping.one mobile.siping.one;
      rewrite ^/(.*) https://ssl.siping.one/$1 permanent;
    }
  ```
  - 文件包含
  ``` nginx
  http {
    include conf.d/*.conf;
  } 
  ```

  - 返回特定状态码及内容
  ``` nginx
  location / {
    default_type text/html;
    return 403 "403,Access Denied";
  }
  ```