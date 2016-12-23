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
.hover() // 绑定或触发 悬浮
jQuery.proxy()
.keydown()//设置或触发键盘按下
.keypress()//设置或触发键盘按住
.keyup()//设置或触发键盘松开
.mousedown() //设置或触发鼠标左键按下
.mouseenter()//设置或触发鼠标左键按住,类似mouseover,但不冒泡
.mouseleave()//设置或触发鼠标左键松开,类似mouseout,但不冒泡
.mousemove()//设置或触发鼠标移动
.mouseout()//设置或触发鼠标离开区域
.mouseover()//设置或触发鼠标进入区域
.mouseup() //设置或触发鼠标右键松开
.off()//清除事件
.on()//绑定事件
.one()//对同一元素,只触发一次
.ready()//DOM加载完成时
.resize()//当窗口发生大小变化时
.scroll()//滚轮滚时
.select()//选择上内容时
.submit()//表单提交
.trigger()//主动触发
.triggerHandler()//和tirgger,但不会触发浏览器的默认行为
.unbind()//取消绑定
.undelegate()//和unbind类似

```

# 结构

```javascript
function on ( elem, types, selector, data, fn, one){
    ...
    return elem.each( function() {
		jQuery.event.add( this, types, fn, data, selector );
	} );
}

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

//事件对象 创建jQuery自己的Event
jQuery.Event = function( src, props ) {}

//jQuery自己的事件对象进行扩展
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

// 把所有有可能的原生event属性放到jquery.event中
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
	off: function( types, selector, fn ) {
	    ....
	    return this.each( function() {
			jQuery.event.remove( this, types, fn, selector );
		} );
	}
	}
} );
```

# 触发顺序

1. 浏览器触发事件: 主监听函数-> jQuery.event.dispatch()-> jQuery.exvent.fix()-> jQuery.Event()
2. 手动触发: 事件便捷方法()-> tigger()/triggerHandler-> Jquery.Event()

# 添加事件

```javascript
jQuery.event = {
    add: function( elem, types, handler, data, selector ) {
        ...
        elemData = dataPriv.get( elem )//获取缓存对象
        ...
        
        if ( !( events = elemData.events ) ) {
			events = elemData.events = {};
		}
		//取出或初始化监听函数
		if ( !( eventHandle = elemData.handle ) ) {
			eventHandle = elemData.handle = function( e ) {
				return typeof jQuery !== "undefined" && jQuery.event.triggered !== e.type ?
					jQuery.event.dispatch.apply( elem, arguments ) : undefined;
			};
		}
	    ...
	    types = ( types || "" ).match( rnothtmlwhite ) || [ "" ];
		t = types.length;
		while ( t-- ) {
            // handleObj is passed to all event handlers
			handleObj = jQuery.extend( {
				type: type,
				origType: origType,
				data: data,
				handler: handler,
				guid: handler.guid,
				selector: selector,
				needsContext: selector && jQuery.expr.match.needsContext.test( selector ),
				namespace: namespaces.join( "." )
			}, handleObjIn );

			// Init the event handler queue if we're the first
			if ( !( handlers = events[ type ] ) ) { //第一个event进来
				handlers = events[ type ] = [];
				handlers.delegateCount = 0;

				// Only use addEventListener if the special events handler returns false
				if ( !special.setup ||
					special.setup.call( elem, data, namespaces, eventHandle ) === false ) {

					if ( elem.addEventListener ) {
						elem.addEventListener( type, eventHandle );
					}
				}
			}
            ...
            if ( selector ) {
				handlers.splice( handlers.delegateCount++, 0, handleObj );
			} else {
				handlers.push( handleObj );
			}
        }
    }
}
```

原理是

1. 取出或初始化监听函数
2. 若之前没有监听过,则对主函数进行监听
3. 如果有主监听函数,则把现在的函数调入回调队列里

