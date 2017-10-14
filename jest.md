# jest

# 踩坑记录

> jest.mock

jest.mock('jquery')

beforeEach(() => jest.resetModules());

不然 $.ajax.mock.calls情况进行累加 

mock jqeury 单一

https://gist.github.com/jcouyang/34686f695cd28309759e

react和jest: http://www.bijishequ.com/detail/379328?p=14

mock jquery: http://dj1211.com/?p=673

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
