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

# 回单效果

现在要做的是小球做自由落体运动的效果

体验地址: http://demo.404mzk.com/css/animation/index.html

最终效果

![自由落体球效果](/assets/animation-demo-1.gif)

可以分为两部分

球向下运动是 加速运动 可以运用 ease-in

而球向上是 减速运动 可以运用 ease-out

首先大致的样式为

```css
@keyframes bounce {
    60%, 80%, to {
        transform: translateY(400px);
        animation-timing-function: ease-out;
    }
    70% { transform: translateY(300px); }
    90% { transform: translateY(360px); }
}

.ball {
    width: 0; height: 0; padding: 1.5em;
    border-radius: 50%;
    margin: auto;
    background: red radial-gradient(at 30% 30%, #fdd, red);
    animation: bounce 10s ease-in forwards;
}
```

分隔为就是 

0~60%: 默认的加速ease-in, 下降

60%~70%: 覆盖默认的 ease-out, 上升

70%~80%: 默认的ease-in, 下降

80%~90%:覆盖默认的ease-out, 上升

90%~100%: 默认的ease-in, 下降

而forwards则取最后一帧的样式



# 参考链接

1. 多值组合, 浏览器处理结果: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Animations/Using_CSS_animations#Setting_multiple_animation_property_values
2. css魔法的效果: http://dabblet.com/gist/1b37089310d0a5a2d8e6