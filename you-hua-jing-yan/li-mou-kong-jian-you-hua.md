# 离某空间优化

# 背景

项目运用了react全家桶

# 目标

优化前非压缩包1.64M, 目标为优化其包体积

# jquery转zepto

由于jquery主要使用了ajax, 没有使用callbacks之类的东西 所以可以转为zepto

但是zepto直接运用在react会有报错

`Cannot read property 'createElement' of undefined`

解决方案为

`npm install --save-dev imports-loader`

然后修改webpack.config

```javascript
loaders: [
             {
                test: /\/node_modules\/zepto\/dist\/zepto\.js$/,
                loader: "imports-loader?this=>window"
            },
            ...
         ]
```