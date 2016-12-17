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
        class2type[ Object.prototype.toString.call( obj ) ] || "object" :
        typeof obj;
},
...
// Populate the class2type map
class2type = {}
jQuery.each( "Boolean Number String Function Array Date RegExp Object Error Symbol".split( " " ),
function( i, name ) {
	class2type[ "[object " + name + "]" ] = name.toLowerCase();
} );
```
其实原理比较简单

1. 基本类型交给typeof
2. null和undefined,分别转换为null和undefined
3. object类型的交给Object.prototype.toString去处理


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


