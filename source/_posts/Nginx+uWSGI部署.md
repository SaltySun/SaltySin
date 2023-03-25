---
title: Nginx+uWSGI部署
tags: 服务器
date: 2022-09-05 00:00:00
category: 服务器
---

Linux系统为Centos7

## 一、Nginx是什么
Nginx类似Apache，一样都是一种Web服务器，基于REST架构风格。
Apache的发展时期很长，而且是毫无争议的世界第一大服务器。它有着很多优点：稳定、开源、跨平台等等。它出现的时间太长了，它兴起的年代，互联网产业远远比不上现在。所以它被设计为一个重量级并且不支持高并发的服务器。在Apache上运行数以万计的并发访问，会导致服务器消耗大量内存。操作系统对其进行进程或线程间的切换也消耗了大量的CPU资源，导致HTTP请求的平均响应速度降低。这些都决定了Apache不可能成为高性能WEB服务器。因此，轻量级高并发服务器Nginx就应运而生了。

## 二、Nginx的作用
Nginx是一款自由的、开源的、高性能的HTTP服务器和反向代理服务器；同时也是一个IMAP、POP3、SMTP代理服务器；Nginx可以作为一个HTTP服务器进行网站的发布处理，另外Nginx可以作为反向代理进行负载均衡的实现。

## 三、关于代理
说到代理，首先我们要明确一个概念，所谓代理就是一个代表、一个渠道；
此时就涉及到两个角色，一个是被代理角色，一个是目标角色。被代理角色通过这个代理访问目标角色完成一些任务的过程称为代理操作过程。如同生活中租房子，房东把房子交给中介，租客让中介帮忙找房子，这个中介就是代理，被代理的角色就是房东，目标角色就是用户。

1. **正向代理**

比如我们要访问国外的网站：Google，我们需要挂VPN。当我们用VPN访问Google的时候，我们的请求会先走到VPN（代理服务器），VPN（代理服务器）去访问Google，然后将访问到的数据传递给我们。这个VPN就是起到了一个正向代理的作用。

2. **反向代理**

比如双11的时候我们访问淘宝，同时连接到网站的访问人数已经爆表，单个服务器远远不能满足人民日益增长的购买欲望了，此时就出现了：分布式部署。也就是通过部署多台服务器来解决访问人数限制的问题；淘宝网站中大部分功能也是直接使用Nginx进行反向代理实现的。

3. **负载均衡**

负载均衡建立在现有网络结构之上，它提供了一种廉价有效透明的方法扩展服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的灵活性和可用性。
负载均衡其意思就是分摊到多个操作单元上进行执行，例如Web服务器、FTP服务器等，从而共同完成工作任务。

## 四、安装 Nginx
```shell
sudo yum install -y nginx
```

安装成功后默认的配置文件为：`/etc/nginx/nginx.conf`

## 五、防火墙配置
打开防火墙可保护我们的服务器

- 查看防火墙状态
```shell
firewall-cmd --state
```

- 防火墙开机自启
```shell
systemctl enable firewalld.service
```

- 打开防火墙服务
```shell
systemctl start firewalld.service
```

- 查看防火墙开启的端口
```shell
firewall-cmd --list-ports
```

- 防火墙打开端口
```shell
firewall-cmd --add-port=80/tcp --add-port=443/tcp
```

注：部署新服务的同时，也需要在腾讯云或阿里云服务器后台的安全组打开端口

## 六、配置Nginx

1. 修改`/etc/nginx/nginx.conf`配置文件
```
# 工作进程数
worker_processes  4;

# 配置事件
events {
    # 最大连接数
    worker_connections  100000;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    # 开启上下文，提升性能
    sendfile        on;
    # 关闭请求成功日志
    access_log      off;
    # 长链接断开超时时间
    keepalive_timeout  65;

    # 配置后端服务
    server {
        # 监听80端口
        listen 80;
        # 站点，可配置ip+端口或域名
        server_name  xxxx.cn;
        charset utf-8;
        client_max_body_size 75M;
        # 访问80端口的根目录配置
        location / {
           include uwsgi_params;
           proxy_pass http://127.0.0.1:8001;  #端口要和uwsgi里配置的一样
           uwsgi_read_timeout 30;
        }
        location /static/ {
           alias /xx/xxxx/static/; #静态资源路径
        }
    }
    # 配置前端服务
    server {
        # 监听80端口
        listen 80;
        # 站点，可配置ip+端口或域名
        server_name  xxxx.cn;
        # 访问80端口的根目录配置
        location / {
            # 首页文件名
            index index.html;
            # 项目根目录
            root /xxx/xxx;
            try_files $uri $uri/ /index.html;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

## 七、操作 Nginx

- 启动Nginx
```shell
systemctl start nginx
```

- 停止Nginx
```shell
systemctl stop nginx
```

- 重启Nginx
```shell
systemctl restart nginx
```

- 查看Nginx状态
```shell
systemctl status nginx
```

- Nginx开机自启
```shell
systemctl enable nginx
```

- 禁用Nginx开机自启
```shell
systemctl disable nginx
```

- Nginx配置文件自检
```shell
nginx -t
```

## 八、uWSGI是什么
uWSGI是一个Web服务器，它实现了WSGI协议、uwsgi、http等协议。

## 九、配置uWSGI

- 在项目根目录下创建 `uwsgi.ini` 配置文件
```shell
touch uwsgi.ini
```

- uWSGI配置文件例子如下
```
[uwsgi]
# 同时打开http和socket协议
http-socket = 0.0.0.0:8001
chdir = 项目根目录
# 使进程在后台运行，并将日志打到指定的日志文件
daemonize = 日志路径
# 启动主进程，来管理其他进程
master = true
# 启动8个工作进程
processes = 8
# 启动4个工作线程
threads = 4
# 当服务器退出的时候自动删除unix socket文件和pid文件
vacuum = true
py-autoreload = 1
static-map = /static=/xx/xxxx/static
# 设置socket的监听队列大小（默认：100）
listen = 1000
# 一个请求花费的时间超过了该时间，当前工作进程自动重启
harakiri = 30
# 为每个工作进程设置请求数的上限
max-requests = 5000
# 设置在平滑重启（直到接收到的请求处理完才重启）
reload-mercy = 8
```

## 十、操作uWSGI

- 运行uWSGI服务
```shell
uwsgi --ini uwsgi.ini
```
