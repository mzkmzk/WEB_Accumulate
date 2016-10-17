# 入门DEMO

# travis.yml

基本的包含元素

1. Install apt addons
2. before_install
3. install
4. before_script
5. script
6. after_success or after_failure
7. OPTIONAL before_deploy
8. OPTIONAL deploy
9. OPTIONAL after_deploy
10. after_script

可以在这里选择自己的语言进行了解<https://docs.travis-ci.com/user/customizing-the-build/#Specifying-Runtime-Versions>

在

# 在github上引用标识

只要在README.md上添加

```shell
[![Build Status](https://travis-ci.org/[YOUR_GITHUB_USERNAME]/[YOUR_PROJECT_NAME].png)](https://travis-ci.org/[YOUR_GITHUB_USERNAME]/[YOUR_PROJECT_NAME])
```
即可

例如我的<https://github.com/mzkmzk/K-Logging>

就写的是`[![Build Status](https://travis-ci.org/mzkmzk/K-Logging.png)](https://travis-ci.org/mzkmzk/K-Logging)
`





# 参考链接

1. travis.yml基本介绍: https://docs.travis-ci.com/user/customizing-the-build/
2. 