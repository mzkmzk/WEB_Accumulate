# 用Jest单元测试维护你的javascript代码

# 安装

1. npm install --save-dev jest babel-jest babel-core
2. 编辑package.json, 添加`scripts: { test: jest }`
3. npm t即可

更详细请前往<http://facebook.github.io/jest/docs/en/getting-started.html#content>

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
     
    expect(examples.data).toBe(1)                             
})
```

重点在于`$.ajax = jest.genMockFunction()` 表示以后`$.ajax`都会被模拟处理

`$.ajax.mock.calls[0][0].success`表示的是: $.ajax中第1次被调用时的第一个参数中success变量

整个逻辑就是模拟ajax会返回数字1, 然后验证其最终的加法是否正确

## 简单定时器处理

### 场景 

要等待定时器执行完毕后, 然后验证其计算结果

```javascript
let examples = {
    data: 0,
    addData: () => {
        setTimeout(() => {
            examples.data += 1        
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
    expect(examples.data).toBe(1) 
})
```

`jest.useFakeTimers()`的作用是对`(setTimeout, setInterval, clearTimeout, clearInterval, nextTick, setImmediate and clearImmediate`等函数进行mock

作用类似于`setTimeout = jest.fn()`

方案2: 模拟运行时间

`setTimeout.mock.calls[0][0]()`替换成`jest.runTimersToTime(3000)`

也可以通过测试, runTimersToTime(3000)表示时间向前运行了3000毫秒

方案3: 运行等待的时间类任务

`setTimeout.mock.calls[0][0]()`替换成

`jest.runAllTimers()`或

`jest.runOnlyPendingTimers()` 也会完成当前所有等待的时间类任务

这两者的区别后面会谈到



## 修改userAgent

userAgent代表一些不能被JS修改的变量

有时我们的业务代码对不同的userAgent做判断

那单元测试应该怎跑 

### 场景

```javascript
let utils = {
    isWeixin: function() {
        let ua = navigator.userAgent.toLowerCase();
        return ua.match(/MicroMessenger/i) == "micromessenger") 
    }
}

```


### 测试用例

```javascript
it('测试isWeixin', () => {
    let util = require('../../src/common/util.js').default

    Object.defineProperty(navigator, 'userAgent', {
        writable: true,
        value: ' MicroMessenger '
    })

    expect(util.isWeixin()).toBe(true)
})
```


## 测试模块内部不公开的函数

### 场景

有时候有些模块里的内部方法不想被外部调用, 而不暴露在模块里

这时候我们应该如何进行测试

### 测试用例

这里我们主要用[rewire](https://github.com/jhnns/rewire "rewire")这个库

```javascript
let rewire = require('rewire'),
    path = require('path')

//getHtmlUrl即为我们没暴露, 但是要进行测试的方法
it('test', () => {
    //getHtmlUrl就是../../src/ci模块中的内部方法
    let app = rewire(path.join(__dirname, '../../src/ci')),
        getHtmlUrl = app.__get__('getHtmlUrl')
    expect(getHtmlUrl()).toEqual('xxx')
})
     
```

这里有个坑就是 `../../src/ci`如果直接用require的时候是可以的

但是rewire的时候就说找不到模块, 所以采用了绝对路径

# 疑惑点

## jest.runAllTimers()和jest.runOnlyPendingTimers()的区别

jest.runAllTimers(): 为执行当前所有的时间类任务

jest.runOnlyPendingTimers(): 为执行当前正在等待的所有时间类任务

例子

```javascript
let infiniteTimerGame = () => {
    setTimeout(() => {
        console.log('Times up! 10 seconds before the next game starts...');
        callback && callback();
    
        // Schedule the next game in 10 seconds
        setTimeout(() => {
          infiniteTimerGame(callback);
        }, 10000);
    }, 1000);
}
infiniteTimerGame()
```

这个任务是

1. 调用方法后等待1000ms后执行回调
2. 过10000ms后就继续第一个步骤

这种情况下 测试代码

执行`jest.runOnlyPendingTimers()` 只会执行第一个1000ms的正在等待任务

但是`jest.runAllTimers()` 会执行定时器里的定时任务, 假如定时器里还有定时器, 又会继续执行定时器任务, 这里的例子, 测试用例将会不断的死循环执行

# 注意事项

## resetModules

最好都在单元测试中顶部增加

```javascript
beforeEach(() => jest.resetModules());
```
这会在运行每次单元测试前 重置模块

不然全局中的变量的.mock.calls很容易会记录之前单元测试的记录

也可以防止某个单元测试修改了模块, 而影响到了其他单元测试


# 待补充

1. 异步 mock第三方库

# 参考资料

1. mock第三方库参考:https://gist.github.com/jcouyang/34686f695cd28309759e
2. react和jest测试: http://www.bijishequ.com/detail/379328?p=14
3. mock jquery: http://dj1211.com/?p=673

