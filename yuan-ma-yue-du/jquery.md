# jQuery

# Ajax

Ajax大致结构

```javascript
let addToPrefiltersOrTransports = structure => {
    return (dataTypeExpression, func) => {...}
}
let inspectPrefiltersOrTransports = ( structure, options, originalOptions, jqXHR ) {
    ...
    return {
        send: (headers, complete) => {},
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