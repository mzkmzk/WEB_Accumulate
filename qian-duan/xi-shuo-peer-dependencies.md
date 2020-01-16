# 细说 peerDependencies

# 问题

在开发npm插件的时候, 例如element-ui

首先element-ui基于vue, 所以它希望使用element-ui的项目, 本身拥有Vue模块

所以peerDependencies 主要用于npm插件告诉使用者, npm插件本身依赖哪些模块, 并且需要安装它

# 业界使用这个属性的情况

> ant-design

```javascript
"peerDependencies": {
    "react": ">=16.0.0",
    "react-dom": ">=16.0.0"
}
```

这个表明使用ant-design 中的react建议大于等于16.0.0版本

> element-ui 

```javascript
"peerDependencies": {
    "vue": "^2.5.17"
},
```

# 不满足条件时的警告

peerDependencies 在npm3及其之后的版本起到的作用只是提示作用

```bash
warning " > vue-loader@15.8.3" has unmet peer dependency "css-loader@*".
warning "@nuxtjs/eslint-module > eslint-loader@3.0.3" has unmet peer dependency "webpack@^4.0.0 || ^5.0.0".
warning "@nuxtjs/eslint-config > eslint-plugin-vue > vue-eslint-parser@5.0.0" has incorrect peer dependency "eslint@^5.0.0".
```

`>`表示哪个项目中引入的插件

例如第一行就表名是根项目本身引用的vue-loader插件中要求根项目有安装css-loader

但跟项目没有安装css-loader, 所以就会有这个问题

has unmet peer dependency表示peerDependency声明的依赖没安装

has incorrect peer dependency 表示peerDependency声明的依赖安装了 但是版本没对上
> 

# 疑问

> 若使用npm模块时, 项目本身不满足npm模块peerDependencies 会发生什么?

其实只会给一句警告

例如 

> npm插件依赖的模块为什么不直接写在Dependencies中?

例如element-ui为什么不把vue模块 写在dependencies中,

这样无论如何使用element-ui都会有vue模块

但是这样安装的vue是在这个element-ui中的node_modules中

```bash
my-project
| - node_modules
    | - vue
    | - element-ui
        | - node_modules
            | - vue
```
这样打包出来就会包含两个vue模块

> 有peer denpendency warning的时候一定要处理吗?

不一定, 其实这也是peerDenpendency非常鸡肋的地方, 因为有可能项目是满足要求的, 但是还是会warning

为什么

例如 这个warming

```bash
warning "@nuxtjs/eslint-module > eslint-loader@3.0.3" has unmet peer dependency "webpack@^4.0.0 || ^5.0.0".
```

eslint-loader声明要装webpack, 但是webpack在nuxt这个包里, 所以就检查不到项目其实有装webpack了

但实际依赖是满足要求的, 但是也会报warming