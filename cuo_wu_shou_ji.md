# 错误收集

# 1. 

错误提示: `Parsing error: 'import' and 'export' may appear only with 'sourceType: module'`

原因: 

pareseOptions中默认的sourceType为`sourceType`



改正方法: 修改.eslintrc

```javascript
"parseOptions": {
  ...
  "sourceType": "module"
  ...
}
```

