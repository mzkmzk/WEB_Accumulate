# Jest

请记住: 最难写的单元测试是第一个单元测试

# 第一个单元测试

1. npm install --save-dev jest
2. 例如我有一个方法,校验url是否为正规的url,我的测试代码

    ```javascript
    import common from '../../Public/Common/Common'
    const TEST_URL = {
        'common': 'http://xunlei.com?name=404&author=mzk#ahaha'
    }
    
    test('get_url_info',() => {
        var url_info = common.get_url_info( TEST_URL.common );
        expect(url_info.is_url).toBe(true);
    })
    ```
3. 在package.json里添加

    ```javascript
      "scripts": {
        "test": "jest"
      },
    ```
4. `npm test`

    ```shell
     ~/Learning/apache_sites/K-Utils: npm run test

    > k-utils@0.0.1 test /Users/maizhikun/Learning/apache_sites/K-Utils
    > jest
    
     PASS  Test/Common/Common.test.js
      ✓ get_url_info (19ms)
    
    Test Suites: 1 passed, 1 total
    Tests:       1 passed, 1 total
    Snapshots:   0 total
    Time:        0.895s, estimated 1s
    Ran all test suites.
    ```

# 常见的测试疑问

## 测试promise

```javascript
//待测试代码
runExec = (execArray, callback) => {
        if ( Object.prototype.toString.call(execArray) !== '[object Array]') throw 'execArray need array '
        
        return execArray.reduce( (acc, current, index, array) => {
            return new Promise( (resolve, reject) => {
                exec(current.command, current.options, (error, stdout, stderr) => {
                    if (error) throw error
                    if ( index === array.length - 1) callback()
                    resolve() 
                })
            })
        }, Promise.resolve())
    }
```



# 遗留问题

# 测试cookie的有效期

```javascript
//测试代码

it('设置 根域 expires_s是否有效', (done) => {
    //jest.useFakeTimers();
    //jest.useRealTimers() ;
    document.domain = ''
    vip_cookie.setCookie('test3','test3_value', {
        is_root_domain: false,// 是否写在根域名下
        expires_s: 1, // cookie存活时间 单位秒
    })
    expect(vip_cookie.getCookie('test3')).toBe('test3_value')
    setTimeout(function(){
        console.log('2 ' + vip_cookie.getCookie('test3'))
        expect(vip_cookie.getCookie('test3')).toBe('test3_value')
         //done()
    },50)
    
    //jest.runTimersToTime(999)
    setTimeout(function(){
        console.log('4 ' + vip_cookie.getCookie('test3'))
        expect(vip_cookie.getCookie('test3')).toBe('')
        done()
    },1050)
     //jest.runTimersToTime(2500)
     //jest.runTimersToTime(2500);
     //jest.runAllTimers()

})

```

这里耗时过长, 原因jest的关于计时器都不会更改实际的时间 

