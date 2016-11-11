# access: 从集合中设置或获取value

# 作用

对集合进行设置key value循环处理

或

获取集合的第一个元素的key值

# 用处

```javascript

jQuery.fn.extend( {
  	attr: function( name, value ) {
 		return access( this, jQuery.attr, name, value, arguments.length > 1 );
  	},
    
    
jQuery.fn.extend( {
	css: function( name, value ) {
		return access( this, function( elem, name, value ) {
			var styles, len,
				map = {},
				i = 0;

			if ( jQuery.isArray( name ) ) {
				styles = getStyles( elem );
				len = name.length;

				for ( ; i < len; i++ ) {
					map[ name[ i ] ] = jQuery.css( elem, name[ i ], false, styles );
				}

				return map;
			}

			return value !== undefined ?
				jQuery.style( elem, name, value ) :
				jQuery.css( elem, name );
		}, name, value, arguments.length > 1 );
	}
} );    


//.text() mainpulation

jQuery.fn.extend( {

	text: function( value ) {
		return access( this, function( value ) {
			return value === undefined ?
				jQuery.text( this ) :
				this.empty().each( function() {
					if ( this.nodeType === 1 || this.nodeType === 11 || this.nodeType === 9 ) {
						this.textContent = value;
					}
				} );
		}, null, value, arguments.length );
	},
    
})
```

# 输入

1. elems: 进行设置或获取值处理的集合
2. fn: function(elem, key, value) 进行设置值的函数
3. key: object|string 当object就是同时设置多个key value的Object
4. value: string 需要设置的值
5. chainable: 是否支持链式调用,如果是则返回集合
6. emptyGet: 当get值时,elems长度为0时,默认的返回值 用的不多
7. raw: 当raw为true表名value是原始数据,而false为函数 作用不大,处理特殊情况

# 输出

大多数情况

当set时,返回set完成后的集合

而get时 返回第一个元素匹配到的key

# 过程

1. 当key为object时,表明要同时set多个值,循环回调
2. 当value不为空,证明要set值,

    1. 当key为空时
         1. 并且value不是函数时,证明类似text(value)的时候,函数进行自行设置固定的key
         2. 当value为函数时,类似html(function()),需要把fn重新改成在,经过了value的函数处理的fn
       
   1. 对elmens进行循环赋值
3. 如果是链式就直接返回当前的elems
4. 当key为空时,类似text的情况,自行处理fn然后返回值
5. 有key则返回第一个elems的key

tips:

set都为链式返回

# 代码

```javascript
var access = function( elems, fn, key, value, chainable, emptyGet, raw ) {
	var i = 0,
		len = elems.length,
		bulk = key == null;
        
 //--------以下是set的情况--------------------------
	// Sets many values 同时设置多值时
	if ( jQuery.type( key ) === "object" ) {
		chainable = true;
		for ( i in key ) {
			access( elems, fn, i, key[ i ], true, emptyGet, raw );
		}

	// Sets one value
	} else if ( value !== undefined ) {
		chainable = true;

		if ( !jQuery.isFunction( value ) ) {
			raw = true;
		}
        //当key为null|undefined时
		if ( bulk ) {

			// Bulk operations run against the entire set
            //例如text(),key为null,所以elems自行each处理
			if ( raw ) {
				fn.call( elems, value );
				fn = null;

			// ...except when executing function values
			} else { //例如.html(function)时
				bulk = fn;
				fn = function( elem, key, value ) {
					return bulk.call( jQuery( elem ), value );
				};
			}
		}

		if ( fn ) {
			for ( ; i < len; i++ ) {
				fn(
					elems[ i ], key, raw ?
					value :
					value.call( elems[ i ], i, fn( elems[ i ], key ) //这里就是html()的情况,但value同时又是回调函数,需要先执行
                    )
				);
			}
		}
	}

	if ( chainable ) {
		return elems;
	}

	// Gets
	if ( bulk ) {
		return fn.call( elems );
	}

	return len ? fn( elems[ 0 ], key ) : emptyGet;
};
```