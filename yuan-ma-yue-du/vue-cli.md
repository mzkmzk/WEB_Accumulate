# vue-cli 源码阅读

# 依赖包简述

### rimraf 多端删除命令

主要实现unix中的命令 rm -rf 方便兼容在window mac linux里跑

github地址: https://github.com/isaacs/rimraf#readme

### semver 版本号处理

```javascript
const semver = require('semver')

semver.valid('1.2.3') // '1.2.3'
semver.valid('a.b.c') // null
semver.clean('  =v1.2.3   ') // '1.2.3'
semver.satisfies('1.2.3', '1.x || >=2.5.0 || 5.0.0 - 7.2.3') // true
semver.gt('1.2.3', '9.8.7') // false
semver.lt('1.2.3', '9.8.7') // true
semver.valid(semver.coerce('v2')) // '2.0.0'
semver.valid(semver.coerce('42.6.7.9.3-alpha')) // '42.6.7'
```

看demo就比较好懂了

github地址: https://github.com/npm/node-semver

### commander 命令行插件

vue-cli 有非常多的命令行交互 

选择的是 commander https://github.com/tj/commander.js

而像webpack使用的是 yargs https://github.com/yargs/yargs

其实非常类似 但是commander 的start多些

# 学习到的技能

### jest设置初始化环境变量

在进行jest测试时 单元测试需要知道当前是测试环境

可在package.json中设置

```javascript
"jest": {
    "setupFiles": [
        "<rootDir>/scripts/testSetup.js"
    ],        
}
```

而在`/scripts/testSetup.js`中设置

```javascript
process.env.VUE_CLI_TEST = true
```