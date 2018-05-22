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
2. 通过select库来模拟用户选中dom 具体参考可见 https://github.com/zenorocha/select/blob/master/src/select.js
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



### 兼容性

1. IE9+ 但是copy时 浏览器会弹个框 问用户是否同意访问剪贴板
2. safari10+ : 即ios系统10及其以上
3. chrome42+

但是在https://caniuse.com/#search=execCommand 中看到

android的 5-6.x 中的chromium66 是不支持的 但是笔者找了个小米的机器 android版本为7.11 都能支持 所以这个caniuse的说明 我暂时还没理解是什么机型

tips: execCommand 其实一直是个野娃娃 W3C标准并不承认它 它的文档也是非官方的 https://w3c.github.io/editing/execCommand.html 所以它到后面可能是会被浏览器抛弃的



### 实现复制功能的核心代码

主要参考clipboard库 

# FLASH 方案

这里就不说了 逐渐抛弃FLASH