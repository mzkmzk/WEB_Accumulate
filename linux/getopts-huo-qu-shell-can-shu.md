# getopts获取shell参数

# 简介

getopts主要用来获取执行shell命令的参数

# 基本用法

```bash
# 文件名before.sh
#!/bin/bash
while getopts 't:' OPT; do
    case $OPT in
        t)
            name="$OPTARG";;
    esac
done
```

输入`./before.sh -t aaa`

`$name`则为aaa

# 基础语法说明

> 参数说明

`while getopts 't:' OPT; do`

中的`t:`表示接收参数t, t参数接收值

假设t不带`:`的话, 就表明值只为true和false, 命令行带了`-t`则为true

> 遍历`getopts`时的内定变量

回顾我们上文的shell

```bash
while getopts 't:' OPT; do
    case $OPT in
        t)
            name="$OPTARG";;
    esac
done
```

- `$OPT`为当前参数的key
- `$OPTARG`为当前参数的value
- `$OPTIND`为当前命令行参数的位移

> 有缺省参数的警告提示

当我命令行为`./before.sh -c aaa`

参数c我是没定义的 默认控制台会输出警告

```bash
$ ./before.sh -c aaa
./before.sh: illegal option -- c
```

想规避这个问题的话需要在参数字符最前面加`:`

```bash
`while getopts ':t:' OPT; do`
```

这样缺省参数就不会告警

# 注意事项

- getopts不支持长参数用法`--version`, 只支持`-v`
- getopts不支持连续命令, 例如`-tab`, 这是不行的, 必须每个参数单独写
- getopts执行顺序为: 命令行前面开始处理, 遇到非-开头的参数就结束了, 如果中间有非`-`开头的参数, 后面参数则获取不到了

# 参考文章

- https://www.cnblogs.com/yxzfscg/p/5338775.html