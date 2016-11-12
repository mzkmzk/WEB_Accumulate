# 获取离浏览器左上角的距离

--代码位置 jQuery/src/offset.99~118

# 需求

判断elem是否在首屏区域内

所以需要获取elem距离左上角的top和left

# 需要理解的api

1. Element.getBoundingClientRect(): 获取距离浏览器窗口左上角和右上角的距离.
2. window.pageYOffset() : 获取垂直距离已经滚动了的距离

# 思路

代码思路不叫简单

1. 把不兼容的返回{top: 0 , left: 0}
2. 如果elem是隐藏的,直接返回elem.getBundingClientRect
3. 获取最终值

    1. top:rect.top+win.pageYOffset
    2. left: rect.left+win.pageXOffset
# 代码

```javascript
if ( !elem ) {
			return;
		}

		// Support: IE <=11 only
		// Running getBoundingClientRect on a
		// disconnected node in IE throws an error
		if ( !elem.getClientRects().length ) {
			return { top: 0, left: 0 };
		}

		rect = elem.getBoundingClientRect();

		// Make sure element is not hidden (display: none)
		if ( rect.width || rect.height ) {
			doc = elem.ownerDocument;
			win = getWindow( doc );
			docElem = doc.documentElement;//获取到当前顶级元素的html标签

			return {
				top: rect.top + win.pageYOffset - docElem.clientTop, //最后减的html标签clientTop一般为0,可能有的浏览器需要
				left: rect.left + win.pageXOffset - docElem.clientLeft
			};
		}

		// Return zeros for disconnected and hidden elements (gh-2310)
		return rect;
```