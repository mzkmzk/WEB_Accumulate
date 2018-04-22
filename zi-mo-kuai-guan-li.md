# 子模块管理

# 简介

为什么要用子模块管理

1. 主项目中需要使用团队的一些插件项目

# 导入插件项目

`git submodule add git@gitlab.x.cn:maizhikun/vip-vconsole.git vip-vconsole`

# 提交插件项目的修改

当子模块有文件处于工作区或者暂存区时, 主项目都无法提交子项目的更新

只有子模块commit到本地仓库时, 产生了新的commit id, 主项目才可以add commit 子模块的修改

# 克隆含有子模块的项目

正常在git clone xxx 里面默认只包含子模块目录, 但是里面没有任何文件 

> 选择一 先子模块init 后update

```bash
# 初始化本地配置文件
git submodule init
# 抓取子模块何时的提交
git submodule update
```

> 选择二 clone时则自动更新子模块

```bash
git clone --recursive xxx
```

# 更新远程子模块到本地

```bash
git submodule update --remote
```

# 删除子模块

```bash
git rm [submodule name]
```

# 参考链接

1. https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97





