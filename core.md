# core

# 结构

```javascript
jQuery.extend( {
    makeArray(arr, results)//
})

function isArrayLike( obj ) {
    
}

```

# 常用知识点

## 判断类数组

```javascript
function isArrayLike( obj ) {
	var length = !!obj && "length" in obj && obj.length,
		type = jQuery.type( obj );

	if ( type === "function" || jQuery.isWindow( obj ) ) {
		return false;
	}

	return type === "array" || length === 0 ||
		typeof length === "number" && length > 0 && ( length - 1 ) in obj;
}
```