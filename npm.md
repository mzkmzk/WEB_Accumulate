# npm

##1. 基本命令介绍

1. `npm init`: 创建新的package.json,加-f/-y(yes)可跳过
2. `npm set 变量名`: 设定环境变量,存在`~/.npmrc`文件
     
     1. init-author-name
     2. init-author-email
     3. init-author-url
     4. init-license
     5. npm save-exact true/false :记录准确版本
3. `npm info 模块名 [具体属性]`: 查看每个模块的具体信息 
4. `npm search <搜索词>`: 搜索仓库,可跟正则
5. `npm list [-global] [模块名]`: 显示当前目录的模块 / 显示全局模块 / 单个模块
6. `npm install [-g] 模块名|[git 地址] [-f]`: 安装本项目/全局模块, 一般全局模块只安装工具类,可以通过命令行调用.--force可以强制重新安装.

    `npm install`默认安装最新版本 模块,如果需要指定版本号可
    1. `npm install sax@latest`
    2. `npm install sax@0.11`
    3. `npm install sax@">=0.10 <0.2.0"`
    4. `npm install 模块名 --save-exact`: 会安装`package.json`指定的确切版本
    5. `npm install sax --save|-S`: 安装到dependencies中 
    6. `npm install sax --save-dev-D`: 安装到devDependencies中
    7. `npm install sax@beta`: 安装最新的beta版本.
    8. `npm install sax@1.3.1-beta.3`: 安装指定beta版本
    9. `npm install --production`: 只安装`dependencies`
7. `npm update|uninstall [-global] [package name]`: 更新/卸载模块.

    但是这个最会更新顶级模块,要想更新依赖其的模块可以`npm --depth 9999 update`
  
  

##2. 模块标签

1. `npm dist-tag add <pkg>@<version> [<tag>]`: 新建标签
2. `npm install<name>@<tag>`: 安装指定标签




##2. 其余命令

1. `npm help`: 获取npm帮助
2. `npm -l`: 获取npm的各种用法
3. `npm -v`: 查看npm版本
4. `npm config list -l`: npm配置
5. `npm install npm@latest -g`: 安装最新版的npm
6. `npm shrinkwrap [--dev]`: 锁定版本,会生成`npm-shrinkwrap.json`会保存当前项目的所有依赖的版本,用户下次install,版本完全根据此文件.
7. `npm prune [package name]`:此命令与`npm shrinkwrap`配套使用,使用`npm shrinkwrap`时,可能存在某个已安装模块不在`dependencies`的情况,这时`npm shrinkwrap`就会报错,`npm prune`可以移除所有不在`dependencies`字段里的模块.

##参考资料
<http://javascript.ruanyifeng.com/nodejs/npm.html>