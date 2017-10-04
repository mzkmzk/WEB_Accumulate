# Animation

# 简单语法

```css
animation-delay: 延迟第一次执行动画的时间, 可s/ms
animation-direction: 动画顺序 normal|reverse|alternate(先顺序 然后反序)|alternate-reverse(先反序 后顺序), 可组合,分隔
animation-duration: 动画持续时间
animation-fill-mode: 动画执行之前和之后的样式选择哪一帧 none|forwards(最后一帧)|backwards(第一帧)|both 
animation-iteration-count: 执行次数 Integer|infinite
animation-name: 执行哪一些动画
animation-play-state: 动画是否执行 running|paused
animation-timing-function: ease|ease-in|ease-out|ease-in-out|linear|step-start|step-end|steps(4, end)
```

tips:

1. 最后一帧和第一帧是哪一帧取决于: animation-direction和 animation-iteration-count
2. animation-play-state: 如一开始就是paused, 则维持第一帧的样式, 而非默认样式
3. animation里animation-duration和animation-name是必须的

# 参考链接

1. 多值组合, 浏览器处理结果: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations#Setting_multiple_animation_property_values