# Event

# 结构

```javascript
function on ( elem, types, selector, data, fn, one){}

jQuery.event = {
    global: {},
    add: function( elem, types, handler, data, selector ) {},//
    remove: function (elem, types, handler, selector, mappedTypes){},//
    dispatch: function ( nativeEvent) {},//
    handlers: function( event, handles) {},//
    addProp: function( name, hook){},//
}


```