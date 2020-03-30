# koa入门

# 简介

一个极简的流式nodejs服务器框架

# 何为流式

把服务器请求生命流程理解为一条线

`------------------`

假如我们的API为生成证书, 用户请求一次, 服务器生成一次证书, 处理函数叫`A`

生命流程变为

`---------A--------`

假设我们要在生成证书前要先校验参数, 校验参数方法叫`B`

`---B------A--------`

然后我们加个统计API的执行时机, 处理的方法叫`C`

这个时机点的计算方式是 API返回的时间戳 - 进入API的时间戳

`C---B------A--------C`

# koa-router

koa本身主要处理http相关的事情, 没有处理路由相关的事情

路由主要交给`koa-router`这个组件

koa-router主要使用方式

```javascript
const Koa = require('koa')
const app = new Koa()
const router = require('koa-router')()

router.get(url, middleware, middleware...)
app.use(router.routes(), router.allowedMethods())
```

`middleware`为一个函数

以记录API处理事件为例写一个middleware

```javascript
async function (ctx, next) {
  const startTime = new Date().getTime()
  await next()
  const handleApiTime = new Date().getTime() - startTime
}
```

# 实战应用

还是以我生成证书为例

```javascript
const Koa = require('koa')
const app = new Koa()
const router = require('koa-router')()
const generateCa = require('./controller/generate-ca/index')

router.get(
  '/generate-ca', async (ctx, next) =>  {
    await generateCa(ctx, next)
    await next()
  }
)

app.use(router.routes(), router.allowedMethods())

app.listen(3000)
```

generateCa主要流程就是生产证书的代码, 这里就贴下返回的数据就好了

```javascript
// ./controller/generate-ca/index
module.exports = async function (ctx, next) {
  // ...
  ctx.body = { 
    code: 0,
    msg: '',
    data: { name }
  }
  await next()

}
```

这样 我的一个生成证书服务就基本搭起来了

### 验证参数中间件

但是假设我们要校验请求参数中的`dns0`和`dns1`, 需要添加一个校验参数的中间件`validate-param.js`

```javascript
const ruleCheckMap = {
  notEmpty: function(key, value){
    if (!value){
      throw new Error(`${key} 不能为空`)
    }
  },
  dns: function(key, value){
    // 只保证无特殊字符
    if (!value){

    } else if (!(/^[a-zA-Z0-9\.\-\*]{5,50}$/.test(value))){
      throw new Error(`${key} 不合法, 只允许字母数字以及-_.等字符`)
    }
  }
}

module.exports = function (param){
  return async (ctx, next) => {
    const { rules } = param
    for (let index = 0; index < rules.length; index++) {
      const { descriptor, key } = rules[index]
      const value = ctx.query[key]
      for (let descriptorIndex = 0; descriptorIndex < descriptor.length; descriptorIndex++) {
        const type = descriptor[descriptorIndex];
        ruleCheckMap[type](key, value)
      }
    }
    await next()
  } 
}
```

这是一个高阶函数, 接受一定的参数配置, 然后返回配置化的参数校验中间件

这个中间件就可以被业务使用


```javascript
router.get(
  '/generate-ca', 
  validateParam({
    rules: [ 
      // dns0必须并且符合dns规则, dns1可以空或者有的话需要符合dns规则   
      { key: 'dns0', descriptor: ['notEmpty','dns'] }, 
      { key: 'dns1', descriptor: ['dns'] }
    ]
  }), 
  async (ctx, next) => {
    await generateCa(ctx, next)
  }
)
```

### 添加队列处理

队列中间件

```javascript
const queue = require('queue')

const queueObj = {}

module.exports = function(options){
  return async function(ctx, next ){
    const { key, max, msg } = options
    if (!queueObj[key]) {
      queueObj[key] = queue({
        autostart: true,
        concurrency: max
      })
    }
    let promise
    if (queueObj[key].length >= max ){
      throw new Error(msg)
    } else {
      promise = next()
      queueObj[key].push(async function(cb){
        await promise
        cb()
      })
    }
    return promise
  }  
}
```

使用业务使用队列中间件

```javascript
router.get(
  '/generate-ca',
  validateParam({
    rules: [ 
      { key: 'dns0', descriptor: ['notEmpty','dns'] }, 
      { key: 'dns1', descriptor: ['dns'] }
    ]
  }), 
  // 添加队列key 和执行max队列最大处理数
  queue({
    key: 'generate-ca',
    max: 5,
    msg: '抱歉, 当前生成证书请求过多, 请稍候再试'
  }), async (ctx, next) =>  {
    await generateCa(ctx, next)
  }
)
```

# 异常错误

由上面的中间件
