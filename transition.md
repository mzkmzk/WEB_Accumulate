# Transition

# 简述

定义一个元素, 切换样式时, 的变化方式

# 简单语法

```css
transition-delay: 默认0, 切换动画延迟时间(可负数)
transition-duration: 默认0, 切换动画的持续时间
transition-property: 默认all, 起动画作用的样式(设置多个属性时, 逗号分隔)
    例如  background
         color
         margin-left等, 也可以为class
transition-timing-function: 默认ease, 动画的执行速度
    例如 ease 开始慢,后面会快
        ease-in, 开始慢, 后面加速
        ease-out, 开始快, 后面减速
        ease-in-out, 先慢, 中快, 后慢
        linear, 平滑
        step-start, 开始就到最终效果
        step-end, 最后才立马执行效果
        steps(4, end), 分几步(正整数)到end状态
    
```

# 混合写法

```css
/* Apply to 1 property */
/* property name | duration */
transition: margin-right 4s;

/* property name | duration | delay */
transition: margin-right 4s 1s;

/* property name | duration | timing function */
transition: margin-right 4s ease-in-out;

/* property name | duration | timing function | delay */
transition: margin-right 4s ease-in-out 1s;

/* Apply to 2 properties */
transition: margin-right 4s, color 1s;

/* Apply to all changed properties */
transition: all 0.5s ease-out;

/* Global values */
transition: inherit;
transition: initial;
transition: unset;
```

# 注意事项

1. transition只对block级元素生效
2. img标签的width,动画无效, 用scale

# 简单DEMO

http://demo.404mzk.com/css/transition/index.html

# 参考链接

1. 基本语法: https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition
2. 更多图片轮播: http://css3.bradshawenterprises.com/cfimg/