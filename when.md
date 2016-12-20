# when

# API

提供了一个基于一个或多个对象的状态来执行回调函数的功能,通常是基于具有异步事件的异步队列

`jQuery.when( deferreds )`

example

```javascript
$.when(
   $.ajax(),
   $.ajax(),
   $.ajax(),
).done(whenDone).fail(whenFail)

//whenDone里得到的数组是ajax里全部success时的得到的参数
```

# 结构


```javascript
function adoptValue( value, resolve, reject ) {
    var method;
    try {
        //  检查副本
        if ( value && jQuery.isFunction( ( method = value.promise ) ) ) {
            method.call( value ).done( resolve ).fail( reject );
        //  检查then
        } else if ( value && jQuery.isFunction( ( method = value.then ) ) ) {
            method.call( value, resolve, reject );
        //  其他情况
        } else {
            resolve.call( undefined, value );
        }
    } catch ( value ) {
        reject.call( undefined, value );
    }
}

jQuery.extend( {
	when: function( singleValue ) {
		var

			// count of uncompleted subordinates
			remaining = arguments.length,

			// count of unprocessed arguments
			i = remaining,

			// subordinate fulfillment data
			resolveContexts = Array( i ),
			resolveValues = slice.call( arguments ),

			// the master Deferred
			master = jQuery.Deferred(),

			// subordinate callback factory
			updateFunc = function( i ) {
				return function( value ) {
					resolveContexts[ i ] = this;
					resolveValues[ i ] = arguments.length > 1 ? slice.call( arguments ) : value;
					if ( !( --remaining ) ) {
						master.resolveWith( resolveContexts, resolveValues );
					}
				};
			};

		// Single- and empty arguments are adopted like Promise.resolve
		if ( remaining <= 1 ) {
			adoptValue( singleValue, master.done( updateFunc( i ) ).resolve, master.reject );

			// Use .then() to unwrap secondary thenables (cf. gh-3000)
			if ( master.state() === "pending" ||
				jQuery.isFunction( resolveValues[ i ] && resolveValues[ i ].then ) ) {

				return master.then();
			}
		}

		// Multiple arguments are aggregated like Promise.all array elements
		while ( i-- ) {
			adoptValue( resolveValues[ i ], updateFunc( i ), master.reject );
		}

		return master.promise();

})
```