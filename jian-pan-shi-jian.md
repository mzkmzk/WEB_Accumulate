# 键盘事件

# 简介

javascript主要涉及到的键盘事件的顺序为 keydown -> keypress -> keyup

 情况/事件| keydown | keypress | keyup
---- | --- | ---
按住某键后按其他健是否会继续触发 | true | false | false
忽略系统功能键(回退、TAB、shift等) |  false | true | false

tips:

1. mouse事件都会有shiftkey ctrlkey altkey matakey 代表这几个键是否被按住, matakey在window中代表win键, mac中代表command键, IE不支持matakey

# 组合键问题

# 中文输入法问题

