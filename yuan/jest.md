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