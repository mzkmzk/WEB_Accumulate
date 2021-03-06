# Object.defineProperty 

# API

`Object.defineProperty(obj, prop, descriptor)`

descriptor 属性:

1. configurable: 是否可配置,默认false
2. enumerable: 是否可枚举,false
3. value: 值
4. writable: 是否可写,默认false

# 注意点

1. 当如果writable即使是false,你去改变prop,它居然不会报错,而只是改变无效而已
2. 当configurable为false时,其他都设置都无效



# 相关应用

## 尽量不破坏原对象

```javascript
// use plain assignment
if ( owner.nodeType ) {
    owner[ this.expando ] = value;
} else {
    Object.defineProperty( owner, this.expando, { 
        value: value,
        configurable: true
    } );
}
```

如果是普通node对象,就直接添加属性,如果是其他,则设置了一个相当于

```javascript
Object.defineProperty( owner, this.expando, { 
        value: value,
        configurable: true,
        writable: false, //默认
        enumerable: false //默认
    } );

```

的对象,这个只能delete掉,而不可学不可枚举
   


# 相关的接口

1. Object.getOwnPropertyDescriptors(object): 返回所有非继承的每个属性都带有descriptor相关属性
2. Object.getOwnPropertyDescriptor(object,prop): 返回指定的非继承的属性都带有descriptor相关属性