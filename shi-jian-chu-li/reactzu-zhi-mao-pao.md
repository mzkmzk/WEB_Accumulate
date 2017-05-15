# react阻止冒泡

# 背景

react中JSX中定义了

```javascript
<button onCLick = {this.a) class="j_button"} >
```

```javascript
a(event){
     event.stopPropagation()
}

$(document).on('click','.j_button', () => {alert(1)})
```

结果alert(1)还是会出现

# 为何

react和jquery类似, 自己维护自己一套事件系统

类似,内容引自react源码

```javascript
 * Overview of React and the event system:
 *
 * +------------+    .
 * |    DOM     |    .
 * +------------+    .
 *       |           .
 *       v           .
 * +------------+    .
 * | ReactEvent |    .
 * |  Listener  |    .
 * +------------+    .                         +-----------+
 *       |           .               +--------+|SimpleEvent|
 *       |           .               |         |Plugin     |
 * +-----|------+    .               v         +-----------+
 * |     |      |    .    +--------------+                    +------------+
 * |     +-----------.--->|EventPluginHub|                    |    Event   |
 * |            |    .    |              |     +-----------+  | Propagators|
 * | ReactEvent |    .    |              |     |TapEvent   |  |------------|
 * |  Emitter   |    .    |              |<---+|Plugin     |  |other plugin|
 * |            |    .    |              |     +-----------+  |  utilities |
 * |     +-----------.--->|              |                    +------------+
 * |     |      |    .    +--------------+
 * +-----|------+    .                ^        +-----------+
 *       |           .                |        |Enter/Leave|
 *       +           .                +-------+|Plugin     |
 * +-------------+   .                         +-----------+
 * | application |   .
 * |-------------|   .
 * |             |   .
 * |             |   .
 * +-------------+   .
 *                   .
 *    React Core     .  General Purpose Event Plugin System
```

react只是在最外层的document进行监听

而且react中的onClick 传递的event,是react的合成event

所以event.stopPropagation() 只会阻止react自己定义的事件冒泡

# 观察现象

那么react中就无法阻止原生的事件冒泡吗

非也

先看下结论

1. event.stopPropagation：在react事件回调函数中调用能阻止react合成事件的冒泡
2. event.nativeEvent.stopPropagation：基本无作用, 阻止的是react的根元素(例如body)的冒泡(不能阻止代理到document的事件)。document可能冒泡给谁?.window
3. event.nativeEvent.stopImmediatePropagation: 能阻止代理到document的事件

# 源码看看



# 参考链接

1. 事件冒泡的基本内容: http://echizen.github.io/tech/2017/04-02-reactjquery-event-system