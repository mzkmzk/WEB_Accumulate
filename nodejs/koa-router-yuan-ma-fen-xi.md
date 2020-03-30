# koa-router源码分析

# 简介

`koa-router`主要用来处理koa中的路由分发到不同的中间件

# 基本流程

### 初始化 register

初始化各种http请求方法, 主要是遍历https://github.com/jshttp/methods/blob/master/index.js#L42-L67

这里的方法都是个壳, 稍微处理下参数都是全丢给Router.prototype.register方法

Router的主要数据结构只有`params`和`stack`

```javascript
function Router(opts) {
  if (!(this instanceof Router)) {
    return new Router(opts);
  }

  this.opts = opts || {};
  this.methods = this.opts.methods || [
    'HEAD',
    'OPTIONS',
    'GET',
    'PUT',
    'PATCH',
    'POST',
    'DELETE'
  ];

  this.params = {};
  this.stack = [];
};
```

### 业务注册路由

`router/get(...)`都是往register方法里丢

register方法主要就是创建一个`Layer`然后往堆栈`stack`里丢

### koa-router回调往koa里丢回调

koa-router和koa唯一的关联就是 需要把

- router.routes()丢给koa.use
- router.allowedMethods()丢给koa.use

`router.routes`里的回调函数在`koa-router`最为重要

负责分发业务往`koa-router`里注册的路由和中间件

主要工作室

- 匹配路由: 主要是调用这个库 https://www.npmjs.com/package/path-to-regexp
- 对符合路由的layer里的数组注入参数
- 对layer.stack 再丢入到当前的匹配数组堆栈中
- 然后最终把整个堆栈交给`koa-compose`

koa-compose是处理中间件的关键, 会在后面的文章再阐述

```javascript
Router.prototype.routes = Router.prototype.middleware = function () {
  var router = this;

  var dispatch = function dispatch(ctx, next) {
    debug('%s %s', ctx.method, ctx.path);

    var path = router.opts.routerPath || ctx.routerPath || ctx.path;
    var matched = router.match(path, ctx.method);
    var layerChain, layer, i;

    if (ctx.matched) {
      ctx.matched.push.apply(ctx.matched, matched.path);
    } else {
      ctx.matched = matched.path;
    }

    ctx.router = router;

    if (!matched.route) return next();

    var matchedLayers = matched.pathAndMethod
    var mostSpecificLayer = matchedLayers[matchedLayers.length - 1]
    ctx._matchedRoute = mostSpecificLayer.path;
    if (mostSpecificLayer.name) {
      ctx._matchedRouteName = mostSpecificLayer.name;
    }

    layerChain = matchedLayers.reduce(function(memo, layer) {
      memo.push(function(ctx, next) {
        ctx.captures = layer.captures(path, ctx.captures);
        ctx.params = layer.params(path, ctx.captures, ctx.params);
        ctx.routerName = layer.name;
        return next();
      });
      return memo.concat(layer.stack);
    }, []);

    return compose(layerChain)(ctx, next);
  };

  dispatch.router = this;

  return dispatch;
};
```