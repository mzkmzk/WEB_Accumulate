# typeof instanceof toString 等判断类型

# typeof

可靠的比较-检测基本类型

```javascript
console.log(typeof 'k') //string
console.log(typeof 123) // number
console.log(typeof true) //boolean
console.log(typeof undefined) //undefined
```

不可靠的比较-检测null和索引值

```javascript
console.log(typeof null) //object
console.log(typeof []) //object
console.log(typeof {}) //object
console.log(typeof new Date()) //object
console.log(typeof new RegExp()) //object
```

# instanceof 

instanceof不仅检验构造这个对象的构造器,还检验原型链上的构造器

所以 所有非基本类型 & undefined & null, instanceof Object都是true




