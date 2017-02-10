# ES6支持IE8

# 关键字

例如class,default等关键字如果单独声明出来

比较常见的是babel转化import时会出现这句话

```javascript
	function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

```

这里的default在IE8下就会报错

解决方法是在webpack中引入`es3ify-loader`

使用方法

```javascript
npm i es3ify-loader --save

//在webpack中
{
         test: /\.js$/,
         loaders: ['es3ify','babel'] //es3ify必须放在babel前
},
```

使用后的编码

```javascript
function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { "default": obj }; }
```

## 参考链接

1. https://github.com/sorrycc/es3ify-loader/issues/3
2. https://github.com/sorrycc/es3ify-loader/issues/1