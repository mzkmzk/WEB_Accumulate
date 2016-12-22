# Event

#API

和Event相关的API巨多

```javascript
.bind() //不建议使用 和.on类似
.blur() //绑定或触发失去焦点
.change() //改变值事件
.click() //点击
.contextmenu() //右键
.dbclick() //双击
.delegate() // 弃用 和on类似
.die() //弃用 移除了用live绑定的事件
.error()// 一般用于img的error
event.currentTarget //和 this一样 绑定时匹配的元素
event.data //给event绑定data
event.delegateTarget //$( ".box" ).on( "click", "button" 中匹配到的`.box`
event.isDefaultPrevented()//是否执行了preventDefault
event.isImmediatePropagationStopped()//是否执行了stopImmediatePropagation 
event.isPropagationStopped() //是否阻止了事件冒泡
event.metaKey//boolean 是否按住了win/command
event.namespace //返回定义的命名空间
event.pageX // 距离document边缘的X轴距离
event.pageY // 距离document边缘的Y轴距离
event.preventDefault() //阻止默认事件
event.relatedTarget //刚有关的target,现在已经被更新
event.result // 同一元素,同一触发类型,的之前一个被触发时,return的值
event.stopImmediatePropagation()//stopPropagation+阻止同级的事件
event.stopPropagation()//阻止冒泡
event.target //真正被点击的元素
event.timeStamp //点击的时间戳
event.type //绑定时1类型
event.which //监听键盘或鼠标具体的操作值
.focus() //绑定或触发 获取焦点
.focusin() //绑定或触发 获取焦点
.focusout() //绑定或触发 失去焦点
.hover()
jQuery.proxy()
.keydown()
.keypress()
.keyup()
.mousedown()
.mouseenter()
.mouseleave()
.mousemove()
.mouseout()
.mouseover()
.mouseup()
.off()
.on()
.one()
.ready()
.resize()
.scroll()
.select()
.submit()
.toggle()
.trigger()
.triggerHandler()
.unbind()
.undelegate()

```

# 结构

```javascript
function on ( elem, types, selector, data, fn, one){}

jQuery.event = {
    global: {},
    //绑定一个或多个类型的事件监听函数
    add: function( elem, types, handler, data, selector ) {},
    //移除一个或多个类型的事件监听函数
    remove: function (elem, types, handler, selector, mappedTypes){},
    //分法事件,执行事件监听函数
    dispatch: function ( nativeEvent) {},
    //
    handlers: function( event, handles) {},
    //添加事件对象的公共属性
    addProp: function( name, hook){},
    //把原生的事件对象封装成jQuery对象,并修正不兼容属性
    fix: function( originalEvent ){},
    //事件修正对象集
    special: {
        load: {},
        focus: {
            tirgger: function(){},
            delegateType: "focusin"
        },//
        blur: {
            tirgger: function(){},
            delegateType: "focusout"
        },
        click: {
            trigger: function(){},
            _default: function ( event ){}
        },
        beforeunload: {
            postDispatch: function( event ){}
        }
    }
}

//移除监听函数
jQuery.removeEvent = function( elem, type, handle ) {}

//事件对象
jQuery.Event = function( src, props ) {}

//原型对象
jQuery.Event.prototype = {
	constructor: jQuery.Event,
	isDefaultPrevented: returnFalse,
	isPropagationStopped: returnFalse,
	isImmediatePropagationStopped: returnFalse,
	isSimulated: false,
	preventDefault: function() {},
	stopPropagation: function() {},
	stopImmediatePropagation: function() {}
}

// Includes all common event props including KeyEvent and MouseEvent specific props
jQuery.each( {
	altKey: true,
	bubbles: true,
	cancelable: true,
	changedTouches: true,
	ctrlKey: true,
	detail: true,
	eventPhase: true,
	metaKey: true,
	pageX: true,
	pageY: true,
	shiftKey: true,
	view: true,
	"char": true,
	charCode: true,
	key: true,
	keyCode: true,
	button: true,
	buttons: true,
	clientX: true,
	clientY: true,
	offsetX: true,
	offsetY: true,
	pointerId: true,
	pointerType: true,
	screenX: true,
	screenY: true,
	targetTouches: true,
	toElement: true,
	touches: true,

	which: function( event ) {
	    ...
	    return event.which;
	}
}, jQuery.event.addProp );

jQuery.each( {
	mouseenter: "mouseover",
	mouseleave: "mouseout",
	pointerenter: "pointerover",
	pointerleave: "pointerout"
}, function( orig, fix ) {
	jQuery.event.special[ orig ] = {
		delegateType: fix,
		bindType: fix,

		handle: function( event ) {}
	};
} );

jQuery.fn.extend( {

	on: function( types, selector, data, fn ) {
		return on( this, types, selector, data, fn );
	},
	one: function( types, selector, data, fn ) {
		return on( this, types, selector, data, fn, 1 );
	},
	off: function( types, selector, fn ) {}
	}
} );
```