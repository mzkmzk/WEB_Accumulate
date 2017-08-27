# 入门

本文所在环境: Mac 10.10 chrome 60.0

chrome在我的机器上属主是root

解决: chown -R maizhikun /Users/maizhikun/Mac软件/Chrome/Google Chrome.app/

chrome没有在全局命令中 最好alias一下

```shell
vim ~/.bash_profile
...
alias chrome='/Users/maizhikun/Mac软件/Chrome/Google\ Chrome.app/Contents/MacOS/Google\ Chrome'

```


# 参考文档

1. https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md