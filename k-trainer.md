# K-Trainer

# 背景

有很多时候,我们需要手动的去完成一些任务显得很繁琐

例如

1. webpack生成完文件后,需要提交到内部机子上
2. 线上服务器不断的自动拉取master分支

找到了源代码,加以修改,然后原来忘记了开启webpack,然后又得cd到对应路径,然后webpack,再上传到内测机子

# 愿景

尽可能的减少不必要动作

# ftp提交

1. 检查文件改动: 依赖`https://www.npmjs.com/package/gaze
` 
2. 查找sftp-config.json
3. 上传文件


查找了下,可以根据下面的库进行改造

`https://github.com/dyz1990/node-ftpsync`

经研究后,发现代码和思路与笔者不符,弃之

# 中途折腾点

## 文件更改监听

```javascript
var fs = require('fs');

fs.watch('./*', {encoding: 'buffer'}, (eventType, filename) => {
    console.log(filename);
    console.log(eventType);
});
```

这个方法注意点有

1. 不支持子目录监听
2. 当新建目录或文件时,eventType为rname





