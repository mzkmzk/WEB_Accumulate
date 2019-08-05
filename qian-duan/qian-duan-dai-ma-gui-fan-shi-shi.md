# 前端代码规范实施

# 简介

主要在3个方面去抓代码规范

主要依赖的是eslint

3方面分别是 

- 代码编辑器在编码时尽可能辅助开发去写正确的代码
- 代码编辑器在编码时就立马展示不符合标准的代码
- 打包 经过eslint校验

# 编辑器

这里笔者主要用的是 vscode

也主要说一下这个编辑器

> 文件tab表示x空格 字符编码 等属性的统一

tab键代表2个空格 4个空格?...

这就是经常需要统一的地方

还有 文件的编码 utf-8

还有文件结尾的字符 有lf, cr, crlf

这些最好都统一起来

而就是建议用 .editorconfig文件来统一

vscode在 1.36.1 (1.36.1) 本身还不支持 .editorconfig的配置

需要下一个 editorconfig for vscode 插件 https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig

笔者项目的.editorconfig一般为

```bash
# editorconfig.org
root = true

[*]
indent_size = 2
indent_style = space
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

[*.md]
trim_trailing_whitespace = false

```

> 边打代码边显示不符合的代码 

这个默认的情况下 在项目中设置了.eslintconfig

eslint就会给出错误的提示

但是像.vue后缀, 默认vscode 并不会去检测其代码

建议在项目根目录创建`.vscode`目录 下创建settings.json文件

内容为

```javascript
{
  "prettier.eslintIntegration": true,
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "html",
    "vue"
  ],}

```

这里表示需要去校验 js文件 jsx文件 html文件 vue文件

# eslint的设置

这位最关键的部分, 因为整个代码检测就是围绕这个去做的

拿vue的项目来说

笔者的.eslintrc文件内容为

```javascript
{
  "extends": [
    "standard",
    "plugin:vue/recommended"
  ],
  "parserOptions": {
    "sourceType": "module",
    "ecmaVersion": 6,
    "parser": "babel-eslint"
  }
}
```

需要安装依赖

```bash
yarn add -D babel-eslint eslint eslint-config-standard eslint-plugin-import eslint-plugin-node eslint-plugin-promise eslint-plugin-standard eslint-plugin-vue
```

# 打包工具配置

> webpack

需要安装依赖 

```bash
yarn add -D eslint-loader 
```

webpack.config.js配置

```javascript
module.exports = {
    module: {
        rules: [{
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /node_modules/
        }]
    }
}
```