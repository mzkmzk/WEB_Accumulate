# access: 从集合中设置或获取value

# 作用

设置一个支持方法重载的函数

# demo

```javascript

jQuery.fn.extend( {
  	attr: function( name, value ) {
 		return access( this, jQuery.attr, name, value, arguments.length > 1 );
  	},
```