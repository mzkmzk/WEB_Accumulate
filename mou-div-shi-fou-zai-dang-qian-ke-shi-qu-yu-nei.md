# 某DIV是否在当前可视区域内

```javascript
function isInViewport(el){
    var rect = el.getBoundingClientRect();

return (
    rect.bottom >= 0 && 
    rect.right >= 0 && 

    rect.top <= (="" window.innerheight="" ||="" document.documentelement.clientheight)="" &&="" rect.left="" <="(" window.innerwidth="" document.documentelement.clientwidth)="" );="" }="" code="">
```