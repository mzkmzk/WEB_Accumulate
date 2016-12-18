# JavaScript

## 匹配空的字符

例如匹配`a b c`中以空格分开的字符串

```javascript

function createOptions( options ) {
	var object = {};
	jQuery.each( options.match( /[^\x20\t\r\n\f]+/g.
 ) || [], function( index, flag ) {
		object[ flag ] = true;
	} );
	return object;
}
```
