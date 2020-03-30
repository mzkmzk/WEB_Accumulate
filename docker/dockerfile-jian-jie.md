# Dockerfile简介

# Dockerfile的作用

创建一个容器时所需的配置条件, 例如

- 镜像从哪获取
- 获取到了镜像后需要执行哪些shell命令
- 设置哪些环境变量
- 需要copy哪些文件进容器中
- 创建完容器后执行哪些shell命令

# 简单DEMO

```bash
# 获取基础镜像
FROM python:3.7-alpine
# 设置工作目录
WORKDIR /code
# 设置环境变量 FLASK_APP
ENV FLASK_APP app.py
# 设置环境变量 FLASK_RUN_HOST
ENV FLASK_RUN_HOST 0.0.0.0
# 创建容器时安装依赖
RUN apk add --no-cache gcc musl-dev linux-headers
# 从宿主机拷贝文件到容器中
COPY requirements.txt requirements.txt
# 安装python依赖
RUN pip install -r requirements.txt
# 宿主中的.复制到容器中工作目录的.
COPY . .
# 创建完容器之后执行的命令 
CMD ["flask", "run"]
```


