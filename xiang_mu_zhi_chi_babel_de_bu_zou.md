# 项目支持Babel转JSX的步骤

## 1. PhpStorm支持JSX

为了在打码时,IDE不报错.(对项目运行无影响,只不过IDE支持JSX编写)

设置`PhpStorm->Preferences->Languages & Frameworks->JavaScript` 

选择Javascript language version 为JSX Harmony即可

## 2. npm安装babel-cli

1. cd 到项目根目录,执行`sudo npm install --save-dev babel-cli`