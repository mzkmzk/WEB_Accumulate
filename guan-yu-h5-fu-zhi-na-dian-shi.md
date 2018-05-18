# 关于H5复制那点事

在页面的需求当中 会遇到 点击按钮 复制指定文案的需求

# 可选的技术方案 

1. execCommand和Selection结合的方案 可选的类库有: https://clipboardjs.com/
2. Clipboard API方案 H5定义剪贴板方案 
3. FLASH方案 可选的类库有: https://github.com/patricklodder/jquery-zclip

# execCommand和Selection方案

tips: execCommand 其实一直是个野娃娃 W3C标准并不承认它 它的文档也是非官方的 https://w3c.github.io/editing/execCommand.html 

### 兼容性

### 实现复制功能的核心代码

主要参考clipboard库 

# FLASH 方案

这里就不说了 逐渐抛弃FLASH