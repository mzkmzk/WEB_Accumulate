# docker-compose 简介

# 入门

建议把官网demo跑一下 就可以把大概流程想明白了

https://docs.docker.com/compose/gettingstarted/

# 基础DEMO

做一个简单的redis访问计数器服务

- 创建主要的python计数代码 `app.py`

这段代码看不懂没关系 直接过

```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)


def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
```

- 在`requirements.txt`写上pyton的安装依赖 

```
flask
redis
```

- `Dockerfile`里写构建容器的信息

```bash
# 拉取pytonh3.7的镜像
FROM python:3.7-alpine
# 设置工作目录在code
WORKDIR /code
# 设置环境变量 FLASH_APP = app.py
ENV FLASK_APP app.py
# 设置环境变量 FLASK_RUN_HOST = '0.0.0.0'
ENV FLASK_RUN_HOST 0.0.0.0
# 执行apk下载依赖
RUN apk add --no-cache gcc musl-dev linux-headers
# 复制当前目录requirements.txt 到 容器中的/code/requirements.txt
COPY requirements.txt requirements.txt
# 安装pyton依赖
RUN pip install -r requirements.txt
# 将当前目录 都拷贝到 /code
COPY . .
# 设置容器默认的命令为 flash run
CMD ["flask", "run"]
```

- 定义`docker-compser.yml`文件

此文件主要配合docker声明

- 当前容器需要的服务依赖
- 容器占用属主机的端口
- 容器占用属主机的volumes


```bash
# 主要跟属主机的docker-composer有关 详情见https://docs.docker.com/compose/compose-file/
version: '3'
# 声明有web服务和redis服务
# web服务将容器的5000端口映射到外网的5000端口
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

- 启动容器服务

```bash
$ docker-compose up
```

此时打开`127.0.0.1:5000`即可看到计数器页面了

- 检查服务状态

```bash
$ docker-compose ps
       Name                      Command               State           Ports
-------------------------------------------------------------------------------------
hello-world_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp
hello-world_web_1     flask run                        Up      0.0.0.0:5000->5000/tcp
```

- 更新容器代码

例如要更新app.py的文案

那么就需要

```bash
$ docker-compose build
$ docker-compose up 
```

- 暂停容器服务

```bash
# 这个不会影响redis计数器的数据保存
$ docker-compose stop
```

- 卸载容器服务

```bash
# redis计数器的数据会被清除
$ docker-compose down
```

