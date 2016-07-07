# 错误收集

# 1. 引入代码设置

错误提示: `Parsing error: 'import' and 'export' may appear only with 'sourceType: module'`

原因: 

pareseOptions中默认的sourceType为`script`

即默认来源限定为`script`标签,如果使用了ECMAScript modules,请修改为`module`

改正方法: 修改.eslintrc

```javascript
"parseOptions": {
  ...
  "sourceType": "module"
  ...
}
```

# 2. 强制/禁止分号

错误提示: `error  Missing semicolon  semi`

原因在于

我在rules中设置了semi为[2,"always"]

这样的话会强制要求分号

所以改为semi: [2,'never']为永不要分号,要了分号会爆`error  Extra semicolon  semi`

改正方法: 修改.eslintrc

```javascript
"rules": {
  ...
  "semi": [2,"never"]
  ...
}
```
