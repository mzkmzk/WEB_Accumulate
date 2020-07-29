# Gitlab-Runner 缓存机制

# 简介

`Cache`主要用作CI任务里用于

- 不同job的文件交流
- 缓存依赖包, 不用每次跑CI都下载依赖包

# 最常见用法

```
# gitlab-ci.yaml
cache:
  untracked: true
```

这里缓存未被git跟踪的文件

很多场景, 这样用就够了

这里拿前端项目来说,

前端项目除了源码本身, 在打包过程

# 设置缓存key

说到缓存, 那就一定存在一个缓存key, 默认的缓存key就叫`default`

如何自定义缓存key, 例如以分支名为key

```
cache:
  untracked: true
  key: ${CI_COMMIT_REF_NAME}
```

`CI_COMMIT_REF_NAME`为分支名, 例如master, develop, 更多变量详见: https://docs.gitlab.com/ce/ci/variables/README.html

# Gitlab-Runner的缓存路径

在`shell`类型的runner中, 如何查看被缓存的信息

```bash
/home/gitlab-runner/cache/xx_vipweb/act.vip.xxx.com.pages
[root@aaa act.vip.xxx.com.pages]# ll
总用量 16
drwx------  2 gitlab-runner gitlab-runner 4096 2020-06-12 20:55:40 0eb08c291ffe9c5c23031d6d4df6f901eafff202
drwx------  2 gitlab-runner gitlab-runner 4096 2020-06-12 21:23:40 bc6b80d2dd37ed39b590ab37cb565167fa2a5601
drwx------. 2 gitlab-runner gitlab-runner 4096 2020-06-21 12:11:14 default
drwx------  2 gitlab-runner gitlab-runner 4096 2020-06-18 17:36:42 develop
```

这个项目的cache缓存key为换过三种

- 默认的default
- 以`CI_COMMIT_REF_NAME`为key
- 以`CI_COMMIT_SHA`为key

所以就有了上面的缓存路径 

# 设置缓存路径

上文说了 一般来说, 我们缓存非git跟踪就能覆盖到很多场景了

但有些时候我们需要指定缓存路径, 就需要这样设置

```bash
cache:
  untracked: true
  #key: "$CI_COMMIT_SHA"
  paths:
    - node_modules/
    - dist/
    - .nuxt/
```

# Cache设置优先级

一般设置cache都是设置全局cache, 

但有时候想单独一个job设定跟全局不一样的cache机制如何操作?

```bash

cache:
  untracked: true
  #key: "$CI_COMMIT_SHA"
  paths:
    - node_modules/
    - dist/
    - .nuxt/
job1:
  stage: xxx
  cache:
    - node_modules/
```

这样`job1`就会只会缓存`node_modules`, 而忽略全局的配置

# Cache的pull和push

默认情况下, 假设设置了全局的缓存机制

```bash
cache:
  untracked: true
```

这样每个job都会在执行前, 先下载cache(pull), 

job执行完毕后, 又重新上传cache(push)

如果某些job只是使用了cache, 没有更新cache

那么我们就可以只pull, 不push

```bash
job1:
  stage: xxx
  cache:
    policy: pull
```

# 设置某些job不使用缓存

```bash
job1:
  stage: xxx
  cache: {}
```

# Cache的继承

像上文所说我们只想pull cache, 这样为某个job单独配置的话, 但大部分配置又相同的情况下

就建议使用继承了

```bash
cache: &global_cache
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
    policy: pull-push
job1:
  cache:
    # 继承全局配置
    <<: *global_cache
    # 覆盖 policy
    policy: pull
```

# 业务中遇到的问题

### CI执行打包后, 文件不生效

背景: 

项目是

- 通过`job_build`来打包项目
- 通过`job_deploy`进行部署打包后的项目到指定机器上

但有一天, 发现CI打包后, `job_deploy`都是旧的打包项目, 

原因是因为同学A在修改CI服务器的时间, 例如为1周后, 

这个时候同学B进行了CI打包 , 导致有个cache包最后的更新时间为一周后

同学A把服务器时间改为正常

同学B再进行CI打包, 发现`job_deploy`部署的都是修改时间为一周后的打包项目

这个估计是因为runner在push,cache的时候, 如果准备向上传的cache包, 比已存在的cache包时间要早

那么就进行不了覆盖




# 参加链接

- gitlabe-runner缓存机制说明: https://zhuanlan.zhihu.com/p/106971627
- gitlab-runenr cache官网: https://docs.gitlab.com/ee/ci/yaml/#cache