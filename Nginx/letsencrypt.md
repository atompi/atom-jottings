# 让你的网站带点“绿”

## What's Let’s Encrypt ?

[Let’s Encrypt](https://letsencrypt.org/) 是由 [ISRG](https://letsencrypt.org/isrg/) (Internet Security Research Group) 提供的免费 SSL 项目，现由 [Linux 基金会](https://www.linuxfoundation.org/) 托管，他的来头很大，由 Mozilla 、思科、 Akamai 、 IdenTrust 和 EFF 等组织发起，现在已经得到 Google 、 Facebook 等大公司的支持和赞助，目的就是向网站免费签发和管理证书，并且通过其自身的自动化过程，消除了购买、安装证书的复杂性，只需几行命令，就可以完成证书的生成并投入使用，甚至十几分钟就可以让自己的 http 站点华丽转变成 https 站点。

## 安装 letsencrypt

Ubuntu

```
sudo apt install -y letsencrypt
```

CentOS / RHEL 7

```
sudo yum install -y letsencrypt
```

## 添加 nginx 配置文件

`nginx.conf` 文件末尾：

```
    include conf.d/*.conf;
}
```

`conf.d/letsencrypt.conf`

```
server {
  listen       80;
  server_name  atompi.com bbs.atompi.com;

  location / {
    root   /usr/local/nginx/html;
  }
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    root   /usr/local/nginx/html;
  }
}
```

## 生成证书

```
letsencrypt certonly --webroot -w /usr/local/nginx/html -d atompi.com -d bbs.atompi.com
```

生成的证书文件在 `/etc/letsencrypt/live/atompi.com/` 目录下

## 配置 nginx 支持全站 https

conf.d/atompi.conf

```
server {
    listen 443;
    server_name  atompi.com;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/atompi.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/atompi.com/privkey.pem;

    location / {
      	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
#        proxy_redirect off;
        proxy_pass http://127.0.0.1:8080/;
        #root /usr/local/nginx/html/;
        #index index.html;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/local/nginx/html;
    }

}

server {
    listen 80;
    server_name atompi.com;
    return 301 https://atompi.com$request_uri;
}
```