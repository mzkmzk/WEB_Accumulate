# 如何进入Docker容器

# 简介

进去docker容器的目的建议只有两个

- debug: 检查生成/修改的文件是否ok
- 熟悉此容器的环境

# 进入Docker容器的方法

### docker exec

```bash
$ docker exec -it f014fa57868e /bin/sh
```

