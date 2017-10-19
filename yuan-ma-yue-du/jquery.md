# jQuery

# Ajax

Ajax大致结构

```javascript
//给全局增加Prefilters或Transports的方法
let addToPrefiltersOrTransports = structure => {
    return (dataTypeExpression, func) => {...}
}
//检查并执行Prefilters或Transports
let inspectPrefiltersOrTransports = ( structure, options, originalOptions, jqXHR ) {
    ...
    return {
        send: (headers, complete) => {
            ...
            compelete(xhr.status, xhrstatusText)//标记请求完成
            ...
        },
        abort: () => {...}
    }
}
jQuery.extend({
    //默认设置
    ajaxSettings: {...},
    //进行全局的ajaxSetup
    ajaxSetup: (target, setting) => {...},
    //增加全局ajax的预处理的方法
    ajaxPrefilter: addToPrefiltersOrTransports( prefilters ),
    //
    ajaxTransport: addToPrefiltersOrTransports( transports ),
})
```