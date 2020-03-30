# Docker搭建nginx服务

# 前期工作

由于想把配置的文件放到属主机上

所以要先把nginx容器中的配置文件copy出来

并且要了解nginx容器里的目录结构

# 手动起docker服务

- 拉取nginx镜像: `docker pull nginx`
- 启动nginx容器: `docker run -d --name nginx nginx`
- 拷贝nginx配置文件: `docker cp a89b2c5f3dd1:/etc/nginx/nginx.conf $PWD/nginx.conf`
- 创建conf.d目录: `mkdir $PWD/conf.d`
- 拉取default.conf `docker cp a89b2c5f3dd1:/etc/nginx/conf.d/default.conf $PWD/conf.d/default.conf`
- 删除容器: `docker container rm a89b2c5f3dd1`

# 编写Dockerfile

```bash
FROM nginx:latest
CMD exec nginx -g 'daemon off;'
```

# 编写docker-compose文件

```bash
version: '3'
services:
  web:
    image: ./
    build: .
    volumes: 
      - ./conf.d:/etc/nginx/conf.d
      - /data:/data
      - /data/nginx/log:/var/log/nginx
    # 端口映射
    ports:
        - "80:80"
```

# 配置静态域名配置文件 

```bash
$ cd conf.d
$ touch www.404mzk.com.conf
```

编写`www.404mzk.com.conf`内容

```bash
server {
    listen       80;
    server_name  www.404mzk.com 404mzk.com;

    location / {
        root   /data/www.404mzk.com/public;
        index  index.html index.htm;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

# 启动服务

```bash
$ docker-compose up -d 
```

# 部署letsencrypt 证书

在Dockfile更改为以下内容

具体参考的部署证书命令为 https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx

```bash
FROM nginx:latest
RUN apt-get update 
RUN apt-get install software-properties-common
RUN add-apt-repository universe 
RUN add-apt-repository ppa:certbot/certbot
RUN apt-get update
RUN apt-get install certbot python-certbot-nginx
CMD exec nginx -g 'daemon off;'
```

然后在exec进容器里执行

```bash
# 生成证书
$ certbot --nginx
# 定期任务刷新证书有效性
$ certbot renew --dry-run
```

这里其实最后两个命令最好也可以做成自动化命令, 但笔者还没研究出来

# 遇到的问题

### volumes映射失败

通过`docker exec`到容器里 发现volumes并没有映射到容器中

后续加了build: .就ok了

```bash
web
    build: .
    volumes:
     - xxx
```

### 启动服务后一直卡在 Attaching to 404mzk-nginx_web_1

因为我启动服务用的是`docker-compose up`所以是当前控制台输出日志的

我以为一直还在启动中...其实显示到这个服务就已经启动了...

后面访问ningx的时候 才会会后续日志输出

所以这个不是问题.....

# 遇到的问题

### 404mzk-nginx_web_1 exited with code 0

执行完`docker-compose up`之后 控制台就输出了这句话 表名容器被关闭了

而且是正确关闭 因为code为0 

所以 我就在https://hub.docker.com/_/nginx 里找官方的镜像说明

在docker-compose.yaml里加`command`就ok

```bash
command: /bin/bash -c "exec nginx -g 'daemon off;'"
```

然后就ok了 