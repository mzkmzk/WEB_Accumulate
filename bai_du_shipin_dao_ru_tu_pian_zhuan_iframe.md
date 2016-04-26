# 百度视频导入图片转iframe

## 1. 需求

需求是这样的.

原本百度编辑器导入视频后,会变成这个球样

![原视频导视频](QQ20160426-4.png)

其实这样的话,优势就是可以修改,如果直接显示iframe,如果想修改,只能重来了

不过这样显示一张图片,的确怪怪的,各有好处吧

## 2.思路

思路应该挺简单,找到往编辑器插入这个图片的那个点,换一下就OK

看一下输出的img是啥

![思路](QQ20160426-5.png)

思路还好,把width height _url抓出来就好了

## 3. 看源码啊

真心,看了这么多插件,这个编辑器的代码写的真心不错..

瞄了几瞄

就能找到在哪生成这个img的啦

大概在一个`insertvideo`的command中找到了
```javascript
html.push(creatInsertStr( vi.url, vi.width || 420,  vi.height || 280, id + i, null, cl, 'image'));
```

再找到`creatInsertStr`的地方

![插入视频](QQ20160426-6.png)

OK,基本确认我们只要在grouplus_iframe中生成iframe即可

完成

```javascript
 case 'grouplus_iframe':
      str = '<iframe ' + (id ? 'id="' + id+'"' : '') + ' width="'+ width +'" height="' + height + '" src="'+url+'" class="' + classname.replace(/\bvideo-js\b/, '') + '"'  +
           ' style="border:1px solid gray;'+(align ? 'float:' + align + ';': '')+'" ></iframe>';
      break; 
```