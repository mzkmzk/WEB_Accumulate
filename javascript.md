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

//使用
'a b c'.match(/[^\x20\t\r\n\f]+/g) //["a", "b", "c"]
```

原理

```javascript
代码/语法	说明
\W	匹配任意不是字母，数字，下划线，汉字的字符
\S	匹配任意不是空白符的字符
\D	匹配任意非数字的字符
\B	匹配不是单词开头或结束的位置
[^x]	匹配除了x以外的任意字符
[^aeiou]	匹配除了aeiou这几个字母以外的任意字符
```

`[^x]` 排除了空格和换行符等东西

## 匹配userAgent的内核和版本号

```javascript

```
