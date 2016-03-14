# Package.json

`package.json`主要用在npm的应用.

##1. 标签说明 
现在逐一解析它的标签

1. `script`: 指定执行命令的缩写.
     
     ```javascript
     "script": {
       "start": "node index.js"
       ...
     }
     ```
     
     运行`npm run start` 等价于 `node index.js`
2. `dependencies`: 项目运行依赖模块

    
3. `devDependencies`: 项目开发需要模块
4. `peerDependencies`: 用来声明插件指定所需的主软件的版本,注意npm 3以后,不会默认安装`peerDependencies`的模块.
5. `main`: 指定加载该模块的入门文件,默认问根目录的inedex.js
6. `config`: 向config字段向环境变量输出值

 ```javascript
 {
     "config": {"port": "8080"}
 }
 ```
  也可这样更改值
  
  `npm config set foo:port 80`
  
在指定模块版本的时候有3种情况

1. 直接指定版本号
2. ~1.2.2: 安装不低于1.2.2且不高于1.3的版本
3. ^1.2.2: 安装不低于1.2.2且不高于2.x的版本
4. latest: 安装最新版本

##2. 命令参考

1. `npm init`:可生成空的package.json文件
2. `npm install` 安装目录中声明的模块
3. `npm install express --save`: 单独安装express模块,并写入dependencies属性中
4. `npm install express --save-dev`,同上,但写入的是devDependencies属性
##参考链接

<http://javascript.ruanyifeng.com/nodejs/packagejson.html>