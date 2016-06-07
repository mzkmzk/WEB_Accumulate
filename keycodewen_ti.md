# 获取方向键keyCode问题

# 1. 问题

需要获取按下键的keyCode

# 2. 研究

控制台中输入

```javascript
  document.onkeydown = function(e) {
      console.log(e.keyCode + "  " + String.fromCharCode(e.keyCode))
  }
```