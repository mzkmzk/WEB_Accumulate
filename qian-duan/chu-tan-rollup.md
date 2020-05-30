# 初探rollup

# 简介

webpack和rollup 在打包领域是各具特色

webpack全能, 复杂

rollup小巧, 简单

rollup的致命缺点是 不建议动态加载资源 使用它

webpack的致命缺点是, 太复杂了 主要指生成后的包

所以rollup主要用在library库的打包里

webpack主要用于项目的打包里

# 第一个lib包

首先 `npm install --save-dev rollup`

编写配置文件

```javascript
export default {
  input: path.join(__dirname, '../../src/js/login/index.js'),
  output: {
    file: 'bundle.js',
    format: 'cjs'
  }
}
```

这里rollup就会把input文件 中引用的所有的相对路径的代码全部打包到`bundle.js`中

而非相对路径的 保持require

```javascript
'use strict';

function _interopDefault (ex) { return (ex && (typeof ex === 'object') && 'default' in ex) ? ex['default'] : ex; }

var event$1 = _interopDefault(require('@/src/js/event'));
var loadJs = _interopDefault(require('@/src/js/utils/loadJs'));

const login = {...}

module.exports = login
```

# plugins介绍

rollup只是一个很单纯的把相对路径的代码合并到一个文件的工具, 它需要各种插件完成任务

|plugin名|作用|
|---|---|
|@rollup/plugin-commonjs|引node_modules时提供commonjs的引入解决方案, 几乎是rollup必须的|
|@rollup/plugin-node-resolve|rollup只会找相对路径, 在node_modules找包需要这个插件实现|
|@rollup/plugin-babel|处理babel|


# 解决的问题

### 不找index.js

例如我们`import user from '../user'`

而我们其实要引的是`../user/index.js`

这个webpack会主动去找`index.js`, 而roolup不会

所以要写个插件`local-resolve`

```javascript
const fs = require('fs')
const path = require('path')

/*
 * 引入目录时，rollup不会添加index.js去查找，所以用这个插件来添加
 * src/icon  ->  src/icon/index.js
 */
module.exports = function localResolve (alias = {}) {
  const aliasArray = Object.keys(alias)
  return {
    resolveId (importee, importer) {
      const localAlias = aliasArray.filter(k => importee.indexOf(k) > -1)[0]
      const isRelative = importee.indexOf('./') > -1
      if (!localAlias && !isRelative) {
        return
      }
      if (!importer) {
        return
      }
      const basename = path.basename(importer)
      let directory = importer.split(basename)[0]
      if (!isRelative) {
        directory = alias[localAlias].replace(localAlias, '')
      }
      const dirIndexFile = path.resolve(directory, importee, 'index.js')
      let stats

      try {
        stats = fs.statSync(dirIndexFile)
      } catch (e) {
        return
      }

      if (stats.isFile()) {
        return dirIndexFile
      }
    }
  }
}
```

rollup引入插件

```javascript
const localResolve = require('./local-resolve')
export default {
  input: path.join(__dirname, '../../src/js/login/index.js'),
  output: {
    file: 'bundle.js',
    format: 'cjs'
  },
  plugins: [
    localResolve({
      src: path.resolve(__dirname, '../../src')
    })
  ]
}
```

### rollup-plugin-vue 修改scss设置

项目中的`b.vue`通过`<style>`标签引a.scss文件

问题在于`a.scss`再去`import './mixins.scss'`的时候

找`./mixins.scss`这个相对路径的文件, 她的路径是相对于`b.vue`的目录

而非相对于`a.scss`

那么如何去解决这个问题

其实最好是修复这个相对路径的引用问题, 但没找到方案

所以改用了`alias`方案 就是所有scss里import的`scss`都写绝对路径, 

以`@`代表项目根目录

```javascript
const VuePlugin = require('rollup-plugin-vue')

{
  plugins: [
     VuePlugin({
      style: {
        preprocessOptions: {
          scss: {
            importer: function (url, prev) {
              url = url.replace(/^@\//, path.join(__dirname, '../../'))
              return {
                file: url
              }
            }

          }
        }
      }
    }),

  ]
}
```

参考sass api这个属性: https://sass-lang.com/documentation/js-api#importer

为了研究rollup-plugin-vue里的sass 是用的`rnder`还是`renderSync`方法渲染的, 阅读了源码

```
rollup-plugin-vue ->
vue-component-compiler ->
component-compiler-utils 
```

最终发现了是`renderSync`, https://github.com/vuejs/component-compiler-utils/blob/v3.1.2/lib/styleProcessors/index.ts#L33


### 一个尝试rollup后 又放弃了 rollup的项目

公司的一个组件库, 里面有js, vue scss

最后还是觉得不适合rollup

最后解决到一个 scss里background:url的图片引用问题

rollup没帮忙处理

想到的方法又是要在rollup的配置里input一个scss文件,

然后再vue里写路径去引用

感觉比较麻烦, rollup 比较适合纯js和vue的打包, 涉及到样式就很鸡肋了

# 参考链接

- 官方文档: https://www.rollupjs.com/guide/tutorial/#%E5%88%9B%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AAbundlecreating-your-first-bundle
- 官网rollup-plugin文档: https://github.com/rollup/plugins
- scss.options.includePaths解决方案: https://stackoverflow.com/questions/53442900/scss-alias-in-vue-sfc-via-rollup#
- rollup-plugin-vue 官方文档: https://rollup-plugin-vue.vuejs.org/options.html#normalizer
- @vue/component-compiler的api文档: https://github.com/vuejs/vue-component-compiler#api
- sass的api文档: https://sass-lang.com/documentation/js-api