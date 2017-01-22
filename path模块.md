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

## 获取文件基本名称 path.basename(path[, ext])

```javascript
path.basename('/foo/bar/baz/asdf/quux.html')
// Returns: 'quux.html'

path.basename('/foo/bar/baz/asdf/quux.html', '.html')
// Returns: 'quux'
```

## 相对路径变绝对路径 path.normalize(path)

path.normalize,主要解决..和.的问题

```javascript
path.normalize('/foo/bar//baz/asdf/quux/..')
// Returns: '/foo/bar/baz/asdf'
On Windows:

path.normalize('C:\\temp\\\\foo\\bar\\..\\');
// Returns: 'C:\\temp\\foo\\'
```

## 路径连接 path.join([...paths])

这样用于链接各路径,其实就是字符串链接后,然后调用path.normalize(path)

