# 基于webpack打造一个原生态的前端脚支架

# 背景

业务实现: 产品需求->设计->重构->前端

现在的很多业务,其实都是简单一个1个~4个页面作用,大多数都是重构的活

# 愿景

直接把重构的image、css、html复制过来,然后直接可以进行开发,并且处理好打包和版本号的问题

# 分析与解决

## js打包

所有JS分为两个包

1. 主流程js: index.js
2. 辅助包: lib.js

注意利用glob,遍历js

```javascript
var entries = getEntry('src/js/index.js');//搜索主js
function getEntry(globPath, pathDir) {
	var files = glob.sync(globPath);
	var entries = {},
		entry, dirname, basename, extname;
 
	for (var i = 0; i < files.length; i++) {
		entry = files[i];

		dirname = path.dirname(entry); //获取文件路径
		extname = path.extname(entry); //获取文件后缀
		basename = path.basename(entry, extname); //获取文件基本名称
		
		entries[basename] = ['./' + entry];
	}
	return entries;
}


```

最后得出的entries类似`{"index":["./src/js/index.js"]}`

得出了所有的js后,就可以设置入口文件了

```javascript
entries['lib'] =['jquery'] //说明lib模块

var config = {
	entry: entries,
```

这样就可以打包出单独的index和lib的js文件啦.

# css打包

由于css压缩包,一般比较小,所以仅一个css包

css主要的就是在index.js里面import进去,避免写在html里,




tips: 可能有使用bootstrap的出来说了,如果类似使用css组件的话,其实css也应该分为index.css和lib.css,但是由于本人最近待的公司都没有用这些组件了,所以暂不做此需求


# html打包

1. 支持多页面打包
2. 引入相关css和js
3. 对资源路径的更换和加版本号

思路是依靠HtmlWebpackPlugin插件进行打包,还是用glob遍历所有html

```javascript
var pages = Object.keys(getEntry('src/**/*.html')); //entries: {"index":["./src/index.html"]}
pages.forEach(function(pathname) {
    var conf = {
        filename: '' + pathname + '.html', //生成的html存放路径，相对于path
        template: 'src/' + pathname + '.html', //html模板路径
        inject: false, //js插入的位置，true/'head'/'body'/false
    };
    if (pathname in config.entry) {
        //conf.favicon = path.resolve(__dirname, 'src/imgs/favicon.ico');
        conf.inject = 'body';
        conf.chunks = ['lib', pathname];
        conf.hash = true;
    }
    config.plugins.push(new HtmlWebpackPlugin(conf));
});
```

这里的疑问是html如何和js对应是,其实是根据文件的基础名称

例如index.html会找入口js的index.js








