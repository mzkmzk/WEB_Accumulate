# 关于H5复制那点事

在页面的需求当中 会遇到 点击按钮 复制指定文案的需求

# 可选的技术方案 

1. execCommand和Selection结合的方案 可选的类库有: https://clipboardjs.com/
2. Clipboard API方案 H5定义剪贴板方案 
3. FLASH方案 可选的类库有: https://github.com/patricklodder/jquery-zclip

# execCommand和Selection方案

### 实现的核心代码

具体的核心思路是

1. 创建一个不可见的textarea
2. 通过select库来模拟用户选中dom 
3. 通过execCommand来执行copy

主要参考clipboardjs里的源码

```javascript
const isRTL = document.documentElement.getAttribute('dir') == 'rtl';

this.removeFake();

this.fakeHandlerCallback = () => this.removeFake();
this.fakeHandler = this.container.addEventListener('click', this.fakeHandlerCallback) || true;

this.fakeElem = document.createElement('textarea');
// Prevent zooming on iOS
this.fakeElem.style.fontSize = '12pt';
// Reset box model
this.fakeElem.style.border = '0';
this.fakeElem.style.padding = '0';
this.fakeElem.style.margin = '0';
// Move element out of screen horizontally
this.fakeElem.style.position = 'absolute';
this.fakeElem.style[ isRTL ? 'right' : 'left' ] = '-9999px';
// Move element to the same position vertically
let yPosition = window.pageYOffset || document.documentElement.scrollTop;
this.fakeElem.style.top = `${yPosition}px`;

this.fakeElem.setAttribute('readonly', '');
this.fakeElem.value = this.text;

this.container.appendChild(this.fakeElem);

this.selectedText = select(this.fakeElem);

let succeeded;

try {
    succeeded = document.execCommand('copy');
}
catch (err) {
    succeeded = false;
}
```

如何模拟用户选择操作? 

具体参考可见 https://github.com/zenorocha/select/blob/master/src/select.js


```javascript
function select(element) {
    var selectedText;

    if (element.nodeName === 'SELECT') {
        element.focus();

        selectedText = element.value;
    }
    else if (element.nodeName === 'INPUT' || element.nodeName === 'TEXTAREA') {
        var isReadOnly = element.hasAttribute('readonly');

        if (!isReadOnly) {
            element.setAttribute('readonly', '');
        }

        element.select();
        element.setSelectionRange(0, element.value.length);

        if (!isReadOnly) {
            element.removeAttribute('readonly');
        }

        selectedText = element.value;
    }
    else {
        if (element.hasAttribute('contenteditable')) {
            element.focus();
        }

        var selection = window.getSelection();
        var range = document.createRange();

        range.selectNodeContents(element);
        selection.removeAllRanges();
        selection.addRange(range);

        selectedText = selection.toString();
    }

    return selectedText;
}

module.exports = select;
```

### 兼容性

1. IE9+ 但是copy时 浏览器会弹个框 问用户是否同意访问剪贴板
2. safari10+ : 即ios系统10及其以上
3. chrome42+

但是在https://caniuse.com/#search=execCommand 中看到

android的 5-6.x 中的chromium66 是不支持的 但是笔者找了个小米的机器 android版本为7.11 都能支持 所以这个caniuse的说明 我暂时还没理解是什么机型

tips: execCommand 其实一直是个野娃娃 W3C标准并不承认它 它的文档也是非官方的 https://w3c.github.io/editing/execCommand.html 所以它到后面可能是会被浏览器抛弃的

关于execCommand 更多的操作可访问: http://demo.404mzk.com/execCommands/

# Clipboard API方案

浏览器在写粘贴板时 一般都直接写进去了 而读粘贴板一般都会弹框询问用户

tips: 这个api必须是https域名 && 当前页面处于活跃状态 && 非控制台调试

demo地址: https://demo.404mzk.com/clipboard/clipboardapi.html

这个比较简单 复制到粘贴板

```javascript
 navigator.clipboard.writeText('要复制的文本')
    .then(() => {
      setTips('文本已经成功复制到剪切板')
     
    })
    .catch(err => {
      // This can happen if the user denies clipboard permissions:
      // 如果用户没有授权，则抛出异常
      setTips('无法复制此文本：', JSON.stringify(err))
     
    });
```

获取粘贴板内容 

```javascript
navigator.clipboard.readText()
    .then(text => {
      setTips('Pasted content: '+ text);
    })
    .catch(err => {
      setTips('Failed to read clipboard contents: ' + JSON.stringify(err));
    });
```
# FLASH 方案

这里就不说了 逐渐抛弃FLASH

# 参考资料

1. https://cloud.tencent.com/developer/article/1004671
2. https://developers.google.com/web/updates/2015/04/cut-and-copy-commands
3. 粘贴板: https://zhuanlan.zhihu.com/p/34698155