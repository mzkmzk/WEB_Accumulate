# 由于标签没闭合引发的各种奇怪问题

一开始爆这样的错

```javascript
Src/Components/Tasks/Tasks.js: Unexpected token (71:10)
  69 |
  70 | Tasks.propTypes = {
> 71 |   taskData: PropTypes.array
     |           ^
  72 | }
```

然后把这段干掉后会爆

```javascript
Src/Components/Tasks/Tasks.js: Unterminated JSX contents (65:18)
  63 |
  64 |                     </div>
> 65 |             </div>
     |                   ^
  66 |         )
  67 |     }
  68 | }
```

这里才是真正的原因,我的div没有闭合掉,但是之前的刚开始的`taskData: PropTypes.array`报错是没错的

然后吧这里的pro

# 注释标签不能放在return的第一行

情况是这样的

```javascript
 return (
           {/* 列表模式 */}
           <div className="files">
```

就会报错

```javascript
/Components/Tasks/Tasks.js
  13:17  error  Parsing error: Unexpected token className

✖ 1 problem (1 error, 0 warnings)


ERROR in ./Src/Components/Tasks/Tasks.js
Module build failed: SyntaxError: /Src/Components/Tasks/Tasks.js: Unexpected token (13:16)
  11 |         return (
  12 |            {/* 列表模式 */}
> 13 |            <div className="files">
```