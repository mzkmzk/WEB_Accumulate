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
    fix: function( originalEvent ){},//
    special: {
        load: {},//
        focus: {
            tirgger: function(){},
            delegateType: "focusin"
        },//
        blur: {
            tirgger: function(){},
            delegateType: "focusout"
        },
        click: {
            trigger: function(){},
            _default: function ( event ){}
        },
        beforeunload: {
            postDispatch: function( event ){}
        }
    }
}


```