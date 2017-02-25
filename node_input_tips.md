# 命令输入提示

# 库的选择 yargs 与 Inquirer.js

yargs:

![yargs](/assets/QQ20170225-0.png)

Inquier.js

![inquirer.js](/assets/QQ20170225-1.png)

可以说yargs比较像传统的命令行交互模式

而inquirer.js则会比较炫酷.

但是笔者还是喜欢选择yargs(可能老了)

当然他们两适合不一样的场景

# Yargs

# 简单API介绍

```javascript

args = require('yargs')
        .usage('Usage $0 -t {type} -out [path] ')//语法介绍
        .option('type',{ //对每个参数进行说明
            alias: 't', //简写
            describe: 'select type for ' + dir_array.join('|')//说明
        })
        .option('out',{
            alias: 'o',
            describe: 'copy to the path, default is "./" '
        })
        .demandOption(['type'],'Please select type by copy project ')//必选的参数,如果没满足的说明
        .help()//自带help命令
        .locale('en')//自定说明文案的语言
        .argv;//获取参数
console.log(args);
```

更完全的文档可见: https://github.com/yargs/yargs