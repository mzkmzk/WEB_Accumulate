# JavaScript

##1. `getElementByID()`和`getElementByName`

低于IE8时

`getElementByID()`对匹配元素的ID不区分大小写,而且也会返回匹配name属性的元素

`getElementByName()`同上,也会返回匹配的ID.

所以最好不要起`ID`和`name`起一样的.

##2. `getElementsByClassName`

`!DOCTYPE`声明的严格程度来选择`怪异模式`和`标准模式`,怪异模式的`getElementsByClassName`不区分大小写.

具体严格程度参考<http://www.w3school.com.cn/tags/tag_doctype.asp>