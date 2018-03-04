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
            describe: 'select type by copy project',//说明
            choices: ["React", "Web"]//仅限可通过的值
        })
        .option('out',{
            alias: 'o',
            describe: 'copy to the path, default is "./" ',
            default: './' //默认值,
        })
        .demandOption(['type'],'Please select type by copy project ')//必选的参数,如果没满足的说明
        .help()//自带help命令
        .locale('en')//自定说明文案的语言
        .argv;//获取参数
console.log(args);
```

输出

```bash
//提示语
~/Learning/apache_sites/K-Cli/ k_cli
Usage /usr/local/bin/k_cli -t {type} -out [path]

Options:
  --type, -t  select type by copy project   [required] [choices: "React", "Web"]
  --out, -o   copy to the path, default is "./"    [default: "./"]
  --help      Show help                                                [boolean]

Missing required argument: type
Please select type by copy project

//argv参数
 ~/Learning/apache_sites/K-Cli/ k_cli --type web --out ./
{ _: [],
  help: false,
  type: 'web',
  t: 'web',
  out: './',
  o: './',
  '$0': '/usr/local/bin/k_cli' }
```

问题: 如果 只说明参数没有指定值,默认为true,像上面的 

`k_cli -t React  -o`

则argv.o非default的'./'而是true

更完全的文档可见: https://github.com/yargs/yargs# Nodejs命令行开发

# 可执行脚本

1. 创建文件,并以`/usr/bind/env node`开头
2. 修改权限: chmod 755 文件名
3. 将命令放入PATH中: 在package.json中新增

    ```javascript
    {
        ...
        "bin": {
            "hello": "hello"
        }
    }
    ```
    执行npm link命令

