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
```

# 输入

1. elems: 进行设置或获取值处理的集合
2. fn: function(elem, key, value) 进行设置值的函数
3. key: object|string 当object就是同时设置多个key value的Object
4. value: string 需要设置的值
5. chainable: 是否支持链式调用,如果是则返回集合
6. emptyGet: 
7. raw: