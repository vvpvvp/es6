#Object的扩展
##属性的简洁表示法
ES6允许直接写入变量和函数，作为对象的属性和方法。这样的书写更加简洁。
```javascript
var foo = 'bar';
var baz = {foo};
baz // {foo: "bar"}

// 等同于
var baz = {foo: foo};
```

除了属性简写，**方法**也可以简写。
```javascript
var o = {
  method() {
    return "Hello!";
  }
};

// 等同于

var o = {
  method: function() {
    return "Hello!";
  }
};
```
如果一个对象的属性是Generator函数，可以简写成下面的形式。
```javascript
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
```

应用：
```javascript
var birth = '2000/01/01';

var Person = {
    name: '张三',
    //等同于birth: birth
    birth,
    // 等同于hello: function ()...
    hello() {
        console.log('我的名字是', this.name);
    }
};
```

##属性名表达式
JavaScript语言定义对象的属性，有两种方法。
```javascript
// 方法一
obj.foo = true;

// 方法二
obj['a' + 'bc'] = 123;
```
ES6允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```javascript
let propKey = 'foo';

let obj = {
  [propKey]: true,
  ['a' + 'bc']: 123,
  //表达式还可以用于定义方法名。
  ['h'+'ello']() {
    return 'hi';
  }
};
```
**注意，属性名表达式与简洁表示法，不能同时使用，会报错。**
```javascript
// 报错
var foo = 'bar';
var bar = 'abc';
var baz = { [foo] };

// 正确
var foo = 'bar';
var baz = { [foo]: 'abc'};
```
##Object对象扩展
###Object.js()
ES5比较两个值是否相等，只有两个运算符：相等运算符（==）和严格相等运算符（===）。它们都有缺点，前者会自动转换数据类型，后者的NaN不等于自身，以及+0等于-0。
```javascript
Object.is('foo', 'foo')
// true
Object.is({}, {})
// false

+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
###Object.assign()
`Object.assign`方法用来将源对象（source）的所有可枚举属性，复制到目标对象（target）。  
与JQuery.extend()方法相同。但是不同的是`Object.assign`方法不能深度复制。

```javascript
var target = { a: 1 };

var source1 = { b: 2 };
var source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

##属性的遍历
ES6一共有6种方法可以遍历对象的属性。  
每种遍历返回结果不同，但是区别主要还是在返回不同种类的结果。  
对象的属性主要分：**可枚举**，**不可枚举**，**Symbol属性**
###for..in
`for...in`循环遍历对象自身的和继承的可枚举属性（不含Symbol属性）。
###Object.keys(obj)
`Object.keys`返回一个数组，包括对象自身的（不含继承的）所有可枚举属性（不含Symbol属性）。
###Object.getOwnPropertyNames(obj)
`Object.getOwnPropertyNames`返回一个数组，包含对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）。
###Object.getOwnPropertySymbols(obj)
`Object.getOwnPropertySymbols`返回一个数组，包含对象自身的所有Symbol属性。
###Reflect.ownKeys(obj)
`Reflect.ownKeys`返回一个数组，包含对象自身的所有属性，不管是属性名是Symbol或字符串，也不管是否可枚举。
###Reflect.enumerate(obj)
`Reflect.enumerate`返回一个Iterator对象，遍历对象自身的和继承的所有可枚举属性（不含Symbol属性），与for...in循环相同。
