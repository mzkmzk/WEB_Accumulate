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

## 判断类型

```javascript
type: function( obj ) {
    if ( obj == null ) {
        return obj + "";
    }

    // Support: Android <=2.3 only (functionish RegExp)
    return typeof obj === "object" || typeof obj === "function" ?
        class2type[ toString.call( obj ) ] || "object" :
        typeof obj;
},
```


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


