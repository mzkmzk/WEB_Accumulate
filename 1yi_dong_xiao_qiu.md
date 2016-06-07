# 1_移动小球

# 1. 定义Canvas
```html
<canvas id="base_1"></canvas>
```
## 2. 初始化画布和准备清除画布函数

```javascript
var base_1 = document.getElementById('base_1');
base_1.width = document.body.scrollWidth;
base_1.height = document.body.scrollHeight;
var ctx = base_1.getContext('2d');

/**
 * 清除画布
 */
function clear_base_1 (){
    ctx.fillStyle = "black";
    ctx.fillRect(0, 0, base_1.width, base_1.height); //还原掉上次的白色球
}

clear_base_1();
```

这里主要定义canvas的宽度,和定义背景为黑色.

## 3. 小球数据结构

```javascript
vae Lead = {
    /**
     * x坐标
     */
    x: 15,
    /**
     * y坐标
     */
    y: document.body.scrollHeight - 15,
    /**
     * 半径
     */
    radius: 10,
    /**
     * 加速度
     */
    ACCELERATED_VELOCITY: 0.09,
    /**
     * 初始速度
     */
    velocity: {'x':0,'y':0},
    /**
     * 最大速度
     */
    MAX_VELOCITY: 3,
    /**
     * 记录wasd是否被按
     */
    more_direction: [],
    ...
}
```
