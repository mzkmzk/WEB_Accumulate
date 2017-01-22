# Path模块

# 简单API

## 获取文件路径 path.dirname(path) 

```javascript
path.dirname('/foo/bar/baz/asdf/quux')
// Returns: '/foo/bar/baz/asdf'
```

## 获取文件后缀 path.extname(path)

```javascript
path.extname('index.coffee.md')
// Returns: '.md'

path.extname('index.')
// Returns: '.'

path.extname('index')
// Returns: ''

path.extname('.index')
// Returns: ''
```