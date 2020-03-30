# envsubst-替换配置项

# 简介 

主要用来替换配置文件的值

# 环境变量替换

> 环境变量替换

现在命令行中设定环境变量

```bash
export MY_NAME=MZK
```

模块文件`config.template`
```bash
user_name="${MY_NAME}"
```

执行命令

```bash
envsubst < config.template > config.yaml
```

则会生成文件`config.yaml`

```bash
user_name=MZK
```

# 参考链接

- https://blog.csdn.net/zh515858237/article/details/79218176
- https://www.jianshu.com/p/744895d06330