# document.documentElement.clientHeight 与几px

# 背景

有一个canvas 想实现铺满全屏

# 问题

把`document.documentElement.clientWidth`

与

`document.documentElement.clientHeight`

赋值给canvas,但是会出现横竖都有滚动条

# 解决尝试

## 宽高度改为100%

现象: 

chrome下还是会有竖滚动条,但横的滚动条能消失,body的height会比canvas多2px

firefox下还是会有竖滚动条,但横的滚动条能消失,body的height会比canvas多4px

## canvas的postion改为absolute

无滚动条,body和canvas的框高度一致

# 遗留问题

仍然不知道为啥body会多几px