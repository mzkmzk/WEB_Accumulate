# 无法安装node-sass

# 1. 过程

在save-dev中需要`node-sass: 3.8.0`,但是在`npm install`中一直会卡死在

```javascript
> node-sass@3.8.0 install /Users/maizhikun/Learning/apache_sites/Journey_Office/node_modules/node-sass
> node scripts/install.js
```

然后一动不动...

然后我就强制`ctrl-c`退出了

然后尝试webpack打包

报错

```javascript
ERROR in dlopen(/node_modules/node-sass/vendor/darwin-x64-47/binding.node, 1): no suitable image found.  Did find:
	/node_modules/node-sass/vendor/darwin-x64-47/binding.node: truncated mach-o error: segment __TEXT extends to 1212416 which is past end of file 852528
 @ ./Src/Components/Header/Header.scss 4:14-129
```


# 2. 解决过程

1. 强制 rm -rf node_modules 再 npm install 或 sudo npm install 仍会卡死在原来的地方
2. 单独安装node-sass

  1. 首先删除node-sass: rm -rf node_modules/node-sass
  2. 单独安装node-sass: npm install --save-dev node-sass,参考<https://paulschreiber.com/blog/2015/06/22/fixing-dlopen-no-suitable-image-found-errors-with-node-sass-and-grunt/>
  3. webpack打包,成功
3. 再尝试一次性install

  1. 因为不信邪,为了一探究竟,重新重置node_modules,直接npm install全部安装
  2. ....突然成功了...我日....