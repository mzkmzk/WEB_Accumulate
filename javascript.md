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

tips: 不建议用userAgent去判断内核和版本号

因为有各种奇怪的版本号了,用这种方法只能算出大多数情况下的内核和版本号,有一些奇怪的版本号 并没有按规定设置.

各内核对应的正则如下

```javascript
webkit: /(webkit)[ \/]([\w.]+)/,
opera: /(opera)(?:.*version)?[ \/]([\w.]+)/,
msie: /(msie) ([\w.]+)/,
mozilla: /(mozilla)(?:.*? rv:([\w.]+))?/,
```

### webkit

示例字符串

```
mozilla/5.0 (linux; u; android 4.1.2; zh-cn; mi-one plus build/jzo54k) applewebkit/534.30 (khtml, like gecko) version/4.0 mobile safari/534.30 micromessenger/5.0.1.352
```

要匹配其中的

1. `webkit/534.30`
2. `webkit 534.30`

正则表达式: `/(webkit)[ \/]([\w.]+)/`

1. `(webkit)`: 表示要匹配一个webkit
2. `[ \/]`: 要匹配一个空格或者一个`/`
3. `([\w.]+)`: 要匹配1个或多个的\w(字母或数组或下划线或汉字)或小数点

### mozilla

示例字符串

```
Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:50.0) Gecko/20100101 Firefox/50.0
```

正则表达式: `/(mozilla)(?:.*? rv:([\w.]+))?/`

1. `(mozilla)`: 要匹配一个mozilla
2. `(?:exp)`: 匹配exp,不捕获匹配的文本,也不给此分组分配组号
3. `.*?`: .表示任意字符,*?表示重复任意次,但尽量少重复