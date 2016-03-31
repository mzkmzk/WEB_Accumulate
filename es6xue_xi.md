# ES6学习

## 令你蒙逼的ES6语法

虽然ES6很多语法令你蒙逼的语法,但是相对之前的JS是有改进的

觉得不错的改进

1. let

    传统的`var`存在变量提升的作用,例如在if里执行`var`代码,其实其作用域是和if同级别的,而非if的下一级别,这个原因是因为JS本身没有块级作用域
    
    而在if里声明`let`,这样let变量会存在if作用域内.
2. const: 该变量为常量.

令我蛋疼的改进

1. 反引号: 动态字符串可使用`${}`获取变量 eg: let k = `a${b}c`
2. 解构赋值: 可以解构数组/对象

    ```javascript
    //数组解构
    const arr = [1,2,3,4];
    const [first,second] = arr; //first=1,second=2
    
    //对象解构
    //旧写法
    function getFullName(user) {
      const firstName = user.firstName;
      const lastName = user.lastName;
    }
    
    //解构写法
    function getFullName ({firstName,lastName}) {
    }
    ```
3. 

    

