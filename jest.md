# 用Jest单元测试维护你的javascript代码



# 踩坑记录

> jest.mock

jest.mock('jquery')

beforeEach(() => jest.resetModules());

不然 $.ajax.mock.calls情况进行累加 

mock jqeury 单一

https://gist.github.com/jcouyang/34686f695cd28309759e

react和jest: http://www.bijishequ.com/detail/379328?p=14

mock jquery: http://dj1211.com/?p=673

# 常用技巧

## 模拟ajax返回

### 场景

代码情况: 业务调用ajax, 根据返回内容做处理数据

```javascript
let examples = {
    data: 0,
    addData: () => {
        $.ajax({
            ...
            success: function(result){ examples.data += result}
        })
    }
}


```

### 测试用例

```javascript
it('检查处理数据', () => {
    let $ = require('jquery'),
        examples = require('../examples.js')
    $.ajax = jest.fn()
    examples.addData()
    $.ajax.mock.calls[0][0].success(1)  
     
    expect(examples.data).toBe(2)                             
})
```

重点在于`$.ajax = jest.genMockFunction()` 表示以后`$.ajax`都会被模拟处理

`$.ajax.mock.calls[0][0].success`表示的是: $.ajax中第1次被调用时的第一个参数中success变量

整个逻辑就是模拟ajax会返回数字1, 然后验证其最终的加法是否正确

## 简单定时器处理

### 场景 

要等待定时器执行完毕后, 然后验证其计算结果

```javascript
var examples = {
    data: 0,
    addData: () => {
        setTimeout(() => {
            examples += 1        
        }, 3000)
    }
}
```

### 测试用例

```javascript
it('测试定时器' => {
    jest.useFakeTimers()
    let exampels = require('../examples.js')
    
    examples.addData()
    
    setTimeout.mock.calls[0][0]()
    expect(examples.data).toBe(2) 
})
```

`jest.useFakeTimers()`的作用是对`(setTimeout, setInterval, clearTimeout, clearInterval, nextTick, setImmediate and clearImmediate`等函数进行mock

作用类似于setTimeout = jest.fn()


# 测试模块内部不公开的函数

```javascript
let rewire = require('rewire'),
    path = require('path')
    
it('test', () => {
    //getHtmlUrl就是../../src/ci模块中的内部方法
    let app = rewire(path.join(__dirname, '../../src/ci')),
        getHtmlUrl = app.__get__('getHtmlUrl')
    expect(getHtmlUrl()).toEqual('xxx')
})
     
```

这里有个坑就是 `../../src/ci`如果直接用require的时候是可以的

但是rewire的时候就说找不到模块, 所以采用了绝对路径