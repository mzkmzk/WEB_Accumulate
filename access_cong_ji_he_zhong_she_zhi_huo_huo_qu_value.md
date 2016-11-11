# access: 从集合中设置或获取value

# 作用

设置一个支持方法重载的函数

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