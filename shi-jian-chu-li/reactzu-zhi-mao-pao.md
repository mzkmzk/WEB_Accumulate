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

