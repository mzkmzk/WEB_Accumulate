# jQuery

# Ajax

Ajax大致结构

```javascript
let prefilters = {}, transports = {}
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
    //增加全局ajax的传输器方法
    ajaxTransport: addToPrefiltersOrTransports( transports ),
    ajax: (url, options) => {
        ...
        //初始化设置
        let s = jQuery.ajaxSetup( {}, options ),
            jqXHR = {
                readyState: 0,
                getResponseHeader: key => {...},
                getAllResponseHeaders: () => {...},
                setRequestHeader: (name, value) => {...},
                overrideMimeType: type => {...},
                statusCode: map => {...},
                abort: statusText => {...}
            }
        deferred.promise( jqXHR );//将jqXHR promise化
        ...
        inspectPrefiltersOrTransports( prefilters, s, options, jqXHR ) //经过预处理
        ...
        jqXHR.done( s.success );
	jqXHR.fail( s.error );
	
	//获取可以真正执行各种ajax操作的传输器对象
	transport = inspectPrefiltersOrTransports( transports, s, options, jqXHR )
	...
	//正在发送请求
	transport.send(  requestHeaders, done )
	
	//完成请求的回调
	function done(){
	    ...
	    if ( isSuccess ) {
		deferred.resolveWith( callbackContext, [ success, statusText, jqXHR ] );
	    } else {
		deferred.rejectWith( callbackContext, [ jqXHR, statusText, error ] );
	    }
	    ...
	}
    }
})
```

## 例子: 加载script (ajax/script.js)

```javascript
jQuery.ajaxPrefilter( "script", function( s ) {
    if ( s.cache === undefined ) {
    	s.cache = false;
    }
    if ( s.crossDomain ) {
	s.type = "GET";
    }
} );

jQuery.ajaxTransport( "script", function( s ) {

	// This transport only deals with cross domain requests
	if ( s.crossDomain ) {
		var script, callback;
		return {
			send: function( _, complete ) {
				script = jQuery( "<script>" ).prop( {
					charset: s.scriptCharset,
					src: s.url
				} ).on(
					"load error",
					callback = function( evt ) {
						script.remove();
						callback = null;
						if ( evt ) {
							complete( evt.type === "error" ? 404 : 200, evt.type );
						}
					}
				);

				// Use native DOM manipulation to avoid our domManip AJAX trickery
				document.head.appendChild( script[ 0 ] );
			},
			abort: function() {
				if ( callback ) {
					callback();
				}
			}
		};
	}
} );	
```

# 参考链接

1. ajax: http://www.jianshu.com/p/6266e69849dc
