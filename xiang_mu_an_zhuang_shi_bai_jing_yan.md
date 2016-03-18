# 项目安装失败经验

## 1. react-web 
git地址`git@github.com:taobaofed/react-web.git`

1. `npm install`
2. 运行项目: `npm run start` 

   但是报错
   
   ```javascript
   npm ERR! react-web@0.2.4 start: `node local-cli/cli.js start` webpack.config.js`
   npm ERR! Exit status 1
   npm ERR!
   npm ERR! Failed at the react-web@0.2.4 start script 'node local-cli/cli.js start webpack.config.js'.
   ```
   
   直接运行试试`node local-cli/cli.js start`试试,但是有提示错
   
   ```javascript
   /Users/maizhikun/Open_Source/react-web_/local-cli/server.js:21
  return new Promise((resolve, reject) => {
                                       ^^
   ```
3. 更新node 

    其实之前就觉得可能是某个模块版本不够新
    
    但是执行了`npm --global --depth 9 update`,node还是没有更新
    
    然后在`react-web`提了个issue<https://github.com/taobaofed/react-web/issues/76>
    
    作者提醒更新node,所以就google更新node
    
    1. `sudo npm install -g n`
    2. `n stable`

    参考<http://www.jb51.net/article/52409.htm>

4. `npm run start`: 就输入localhost:3000试试吧.

大概有的模块截图

![react-web](QQ20160318-0.png)
   
   
   