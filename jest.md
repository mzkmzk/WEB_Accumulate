# 用Jest单元测试维护你的javascript代码

# 单元测试的应用场景

笔者不太推荐所有业务都使用单元测试

但是一些JS公共组件库, 需要用单元测试来保证其模块API的稳定性

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

## 异步

### 场景一: callback回调

例如JSONP, 大部分情况下我们都mock掉

但是假如我们真的要测试jsonp方法是否有把请求发出去, 并验证接口的返回

### 测试用例一: done方法

```javascript
it('测试真实ajax', done => {
    $.ajax({
        url: 'http://dynamic.vip.*.com/cashv2/userCashList',
        data: { 
            paybiz: 'supervip'
        },
        timeout: 2000,
        success: result =>  {
            expect(result).toEqual({ result: -1, msg: '登录验证失败！'});
            done()
        },
        error: result => {}
    })
})
```

只有真正运行到done, 单元测试才会结束

要注意假如expect不正确, 就不会执行下面的done 并且不会告诉expect前后对比差在哪里, 只会等到jest的timeout错误

### 测试用例二: Promise方法

```javascript
it('测试真实ajax', () => {
    expect.assertions(1)
    return $.ajax({
        url: 'http://dynamic.vip.*.com/cashv2/userCashList',
        data: { 
            paybiz: 'supervip'
        },
        timeout: 2000
    })
    .then(result => expect(result).toEqual({ result: -1, msg: '登录验证失败！'});
)
})
```

`expect.assertions(1)`表示要执行一次expect校验

### 测试用例三: .resolves / .rejects 

```javascript
it('测试真实ajax', () => {
    expect.assertions(1)
    return $.ajax({
        url: 'http://dynamic.vip.*.com/cashv2/userCashList',
        data: { 
            paybiz: 'supervip'
        },
        timeout: 2000
    })
    .resolves()
    .toEqual({ result: -1, msg: '登录验证失败！'});
)
})
```

类似Async/Await的用法可以参考<http://facebook.github.io/jest/docs/en/asynchronous.html#async-await>

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

## Mock整个第三方库

### 场景

```javascript
let { exec } = require('child_process'),
    ci = {
    check: () => {
        exec('git log --name-only -1', (error, stdout, stderr) => {
            ...
        }
    }
}


```

###测试用例 

我们需要测试的是 回调中的处理 

但是exec里执行的我们要mock掉, 而且回调中的参数 要是我们mock的

```javascript
//在__tests__同父目录下新建, __mocks__/child_process.js
const child_process = jest.genMockFromModule('child_process');


let exec_callback_args = []
function __setMockExec(args = []) {
    exec_callback_args = args
}

function exec(_, callback) {
    callback && callback.apply(null, exec_callback_args )
}

child_process.__setMockExec = __setMockExec;
child_process.exec = exec;

module.exports = child_process;

```

```javascript
//test.js
jest.mock('child_process')

it('测试处理exec', () => {
    let myStdout = 'ahah',
        myStderr = 'aaa',
        ci = require('../src/ci.js')
        
    require('child_process').__setMockExec([null, myStdout, myStderr])
    ci.check()
    
    expect...    
    
})

```

这种方式是写mock的时候会比较麻烦

但是写好一次后 以后类似的mock功能 就可以通用了

## mock 引用的css文件和html文件

在引用HTML时, jest一般都会报错

例如

```javascript
import tplItem from './item.html';
```

在执行jest测试时候 会报错

```bash
/packages/vip-vconsole/src/log/item.html:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){<div class="vc-item vc-item-{{logType}} {{if (!noMeta)}}vc-item-nometa{{/if}} {{style}}">
    
SyntaxError: Unexpected token <
```

当这些HTML对我们的测试用例无影响时候 可以这样mock

引入`identity-obj-proxy`模块

`npm install --save-dev identity-obj-proxy`

tips: 

identity-obj-proxy主要的作用就是更改import模块的时候 其导出的是一个无论key为何值 value都等于key的一个对象

在项目跟目录创建jest.config.js文件

```javascript
moduleNameMapper: {
    "\\.(html)$": "identity-obj-proxy"
}
```

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

# 单元测试覆盖率 

```
> jest --coverage
Test Suites: 2 passed, 2 total
Tests:       18 passed, 18 total
Snapshots:   0 total
Time:        0.695s, estimated 1s
Ran all test suites.
----------|----------|----------|----------|----------|----------------|
File      |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
----------|----------|----------|----------|----------|----------------|
All files |      100 |      100 |      100 |      100 |                |
 index.js |      100 |      100 |      100 |      100 |                |
 utils.js |      100 |      100 |      100 |      100 |                |
----------|----------|----------|----------|----------|----------------|
```

1. Stmts: 语句覆盖率
2. Branch: 分支覆盖率
3. Funcs: 函数覆盖率
4. Lines: 行覆盖率

其实这些指标都是一个提示, 并不要太在意

通常80%以上的覆盖率就足够

若每个分支都要走一遍 很多是意义的

什么叫做无意义, 是即使你走了这个分支 你的断言也不知道该怎么检查

这4个指标里 一般百分比最高的是Funcs, 最低的是Branch

我的建议是: 对外开放的接口多测试

而内部使用的方法 保证内部使用正确即可 

# 有什么没讲

1. ui类测试
2. react测试

jest其实基本是为react定制的, 但是笔者不太喜欢给UI写测试, 所以这两类测试都木有讲

# 参考资料

1. mock第三方库参考:https://gist.github.com/jcouyang/34686f695cd28309759e
2. react和jest测试: http://www.bijishequ.com/detail/379328?p=14
3. mock jquery: http://dj1211.com/?p=673

