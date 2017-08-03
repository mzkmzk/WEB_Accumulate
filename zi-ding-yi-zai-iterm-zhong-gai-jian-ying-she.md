# 自定义在iterm中改建映射

# 需求

因为使用例如tmux等工具的时候 其命令及其烦人

例如新建一个窗口是 ctrl+b 然后 再按c

mac上按ctrl+b 本来就惨绝人寰....

# 步骤

1. item2->performance->keys->+
2. keyboard shortcut里写你想改成啥快捷键
3. 选择action为 Send Hex code
4. 最后一栏去查询[查询键盘和16进制Hex转换的查询表](http://www.cisco.com/c/en/us/td/docs/ios/12_2/configfun/command/reference/ffun_r/frf019.pdf "查询键盘和16进制Hex转换的查询表")

# 例子

例如 我想改ctrl+b 然后按c 改成直接按 command+shift+c

1. 那么我在keyboard Shortcut 先按下command+shift+c
2. 选择action为 Send Hex code
3. 最后一栏改为 0x02 0x63