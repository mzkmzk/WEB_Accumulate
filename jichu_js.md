#1. `getElementByID()`和`getElementByName`

低于IE8时

`getElementByID()`对匹配元素的ID不区分大小写,而且也会返回匹配name属性的元素

`getElementByName()`同上,也会返回匹配的ID.

所以最好不要起`ID`和`name`起一样的.

#2. `getElementsByClassName`

`!DOCTYPE`声明的严格程度来选择`怪异模式`和`标准模式`,怪异模式的`getElementsByClassName`不区分大小写.

具体严格程度参考<http://www.w3school.com.cn/tags/tag_doctype.asp>

IE8及其低版本不支持'`getElementsByClassName`

#3. `querySelectorAll`

`IE7 8`支持CSS2选择器.

但是很多浏览器拒绝返回伪类`:link` 和 `:visited`

#4. children等节点方法

| 属性名                 | 被替代的属性      |
|------------------------|-------------------|
| children               | childNodes        |
| childElmentCount       | childNodes.length |
| firstElementChild      | firstChild        |
| lastElementChild       | lastChild         |
| nextElementSlibling    | nextSlibling      |
| previousElementSlibing | previousSlibing   |

以上属性IE6/7/8只支持children属性,使用新的API会比旧的要快很多,因为少了空白等无用的节点个数.

##5 ECMAScript 5

1. 判断是否为数组 `Array.isArray([])`
2. 函数调用的this 权威`8.2.1`
3. 严格模式下,无法使用擦callee和caller8.3.2
4. 严格模式下,call和apply第一个参数8.7.3
5. ECMAScript新增bing 8.7.4