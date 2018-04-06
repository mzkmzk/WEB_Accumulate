# 键盘事件

# 简介

javascript主要涉及到的键盘事件的顺序为 keydown -> keypress -> keyup

 情况/事件| keydown | keypress | keyup
---- | --- | ---
按住某键后按其他健是否会继续触发 | true | false | false
忽略系统功能键(回退、TAB、shift等) |  false | true | false
区分大小写 | false | true | false
this.value可以包含刚按下的字符 | false | false | true

tips:

1. keyboard事件都会有shiftkey ctrlkey altkey matakey 代表这几个键是否被按住, matakey在window中代表win键, mac中代表command键, IE不支持matakey
2. keydown和keyup区分不出大小写, keycode都是大写的ASCII

# 组合键问题

# 中文输入法问题

# 常用例子

### 输入只能是整数



# 兼容性

### 存放ASCII地方

1. IE9上event对象charCode 保存按键的ASCII
2. 高级浏览器只有在触发keypress使用包含charCode, 并且值和keycode一致
3. mac的firefox keydown和keyup用keyCode存放ASCII, keypress用charCode存放ASCII

所以建议这样取ASCII

```javascript
getCharCode: function(event) {
    if ( event.charCode) {
        return event.charCode;
    } else {
        return event.keyCode;
    }
}
```