# __call __invoke bindTo简单说明

# javascript动态添加方法

```javascript
function Foo() {
}

var obj = new Foo();
obj.say = function () {
    return "Hello World";
};
print(obj.say());

// Result:
// Hello World
```

