# kubectl入门

# 简介

kubectl是用来操作kus资源的

# 句法

```
kubectl [command] [TYPE] [NAME] [flags]
```

## command

command制定对资源执行的操作, 例如`create`, `get`, `describe`, `delete`

## type

type为k8s里的资源类型具体描述见[链接](https://kubernetes.io/docs/reference/kubectl/overview/#resource-types)

常用的有`pods`、`services`、`deployments`、`events`, `ingresses`

# 参考链接

- [k8s官方文档](https://kubernetes.io/docs/reference/kubectl/overview/#resource-types)