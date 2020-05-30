# k8s部署

# gitlab打包发镜像

gitlab自身也提供docker-hub功能

所以在gitlab-ci中docker push镜像时 

需要在 `Settings->General->Permissions->Respository->Container registry`打开

否则会遇到错误

```bash
denied: requested access to the resource is denied
```

然后就可以在gitlab->项目->Registry看到自己提交的镜像

# 内网k8s

项目->选择自己的项目->发布新版本->