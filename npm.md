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
6. `npm install [-g] 模块名|[git 地址]`: 安装本项目/全局模块, 一般全局模块只安装工具类,可以通过命令行调用.
7. 


##2. 其余命令

1. `npm help`: 获取npm帮助
2. `npm -l`: 获取npm的各种用法
3. `npm -v`: 查看npm版本
4. `npm config list -l`: npm配置
5. `npm install npm@latest -g`: 安装最新版的npm