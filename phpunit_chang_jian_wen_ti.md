# 常见问题

本文基于使用Laravel5.2 & phpunit4.8 

# 1.程序报错

	1) User_Test::test_Insert
	ErrorException: Invalid argument supplied for foreach()
	
	原因是seeJson需要返回的是JSON,当返回无法JSON格式化的数据,大多数报这个错误.

	但可恶的是这些错误不会报到报错文件!!所以建议实践操作一次.看错误内容是神马.

	虽然在控制台会显示报错,但是偶尔会显示不全所有报错信息.

	[参考](http://stackoverflow.com/questions/31921451/laravel-5-1-phpunit-api-test-returns-always-invalid-argument-error-foreach)	,

# 2.伪集合数组问题
		
		//代码
		 $this->post('/Controller/User_Controller/insert', ['id' =>  $this::$id , 'username' => $this::$id])
            ->seeJson([
                'result' => "true",
            ]);
		
		//报错
		1) User_Test::test_Insert
		Unable to find JSON fragment ["result":"true"] within [{"result":true}].
		Failed asserting that false is true.
		
	第一眼看上去以为是前面是数组里的属性,而后面是数组里对象的属性的原因....万万没想到..是因为true前后的双引号的原因..在测试类中去掉就好了	
	
# 3.数组需要包含所有属性问题

	问题是我需要检验一个对象中的部分属性,好像实现不了.我的代码
	
		 public function test_Query()
    	{
        	$this->post('/Controller/User_Controller/query', ['id' => $this::$id])
            ->seeJson([
                'data'   => [ 0 =>['username' =>  $this::$id.'_update']],
            ]);
    	}
		
		//测试结果
		1) User_Test::test_Query
		Unable to find JSON fragment ["data":[{"username":"1137598_update"}]] within [{"current_page":1,"data":[{"created_at":"2015-10-29 18:57:13","deleted_at":"0000-00-00 00:00:00","email":"","id":1137598,"last_login_at":"0000-00-00 00:00:00","login_times":0,"name":"","phone":"","privilege":1,"status":0,"updated_at":"2015-10-29 18:57:13","username":"1137598_update"}],"from":1,"last_page":1,"next_page_url":null,"per_page":20,"prev_page_url":null,"to":1,"total":1}].
		Failed asserting that false is true.
        
# 4. 调用url错误

执行代码
```php
$response = $this->call('POST','/auth/login',[
            'email'=> '437292602@qq.com',
            'password'=> '805547MZK',
]);
```

错误代码

```
Fatal error: Call to a member function make() on null in /Users/maizhikun/Learning/apache_sites/grouplus_front/trunk/vendor/laravel/framework/src/Illuminate/Foundation/Testing/Concerns/MakesHttpRequests.php on line 447
```

解决方法
需要在调用php代码前加`parent::setUp();`

# 5. 模拟登陆

在测试中Controller很多都依赖于登陆的情况下获取数据,所以我们对这些方法测试时,需要模拟登陆
```php
 public function setUp(){
        //new \App\Http\Controllers\LoginController();
        parent::setUp();
        $response  = $this->call('POST','登陆url',[
            'email'=> '1231232131@qq.com',
            'password'=> '12312312312',
            'rememberPwd'=> '0',
            '_token'=>'12321312',
        ]);
        $this->assertEquals(302, $response->status());
    }
```

这样session就会保存在我们之后的测试用例当中

# 6. 测试私有方法

通过放射机制

```php
$method = new ReflectionMethod('\App\Http\Controllers\AController','method__');
$method->setAccessible(true); //改变private为public
dump($method->invoke(new AController()); 调用方法
```

