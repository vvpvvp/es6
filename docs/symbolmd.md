# Symbol

##概述
ES5的对象属性名都是字符串，这容易造成属性名的冲突。比如，你使用了一个他人提供的对象，但又想为这个对象添加新的方法（mixin模式），新方法的名字就有可能与现有方法产生冲突。如果有一种机制，保证每个属性的名字都是独一无二的就好了，这样就从根本上防止属性名的冲突。这就是ES6引入Symbol的原因。

ES6引入了一种新的原始数据类型Symbol，表示独一无二的值。它是JavaScript语言的第七种数据类型，前六种是：Undefined、Null、布尔值（Boolean）、字符串（String）、数值（Number）、对象（Object）。

```javascript
let s = Symbol();

typeof s// "symbol"

//添加字符串参数主要是为了log时容易区分
var s2 = Symbol('bar');
s2 // Symbol(bar)
s2.toString() // "Symbol(bar)"

//两个Symbol实例始终都不相等
var s1 = Symbol();
var s2 = Symbol();
s1 === s2 // false

//Symbol值不能与其他类型的值进行运算，会报错。
"your symbol is " + s1
// TypeError: can't convert symbol to string

//Symbol值也可以转为布尔值，但是不能通过运算的方式转换。
Boolean(s1)//true
!sym  // false

```
##作为属性名
由于每一个Symbol值都是不相等的，这意味着Symbol值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。


```javascript
var mySymbol = Symbol();

// 第一种写法
var a = {};
a[mySymbol] = 'Hello!';

// 第二种写法
var a = {
  [mySymbol]: 'Hello!'
};

// 第三种写法
var a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });

// 以上写法都得到同样结果
a[mySymbol] // "Hello!"
```

##消除静态字符串
风格良好的代码，应该尽量消除静态字符串，该由含义清晰的变量代替。


```javascript
var shapeType = {
  triangle: 'Triangle'
};

function getArea(shape, options) {
  var area = 0;
  switch (shape) {
    case shapeType.triangle:
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}

getArea(shapeType.triangle, { width: 100, height: 100 });

//替代
var shapeType = {
  triangle: Symbol('Triangle')
};
...
```
##属性名的便利

`Symbol`作为属性名，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`返回。
但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有`Symbol`属性名。

```javascript
var obj = {};
var a = Symbol('a');
var b = Symbol.for('b');

obj[a] = 'Hello';
obj[b] = 'World';

var objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]
```
##Symbol.for()，Symbol.keyFor()
有时，我们希望重新使用同一个Symbol值，Symbol.for方法可以做到这一点。
```javascript
var s1 = Symbol.for('foo');
var s2 = Symbol.for('foo');
s1 === s2 // true
//Symbol.keyFor方法返回一个已登记的Symbol类型值的key。
var s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"
```
##更多的应用
更加详细的信息请至[阮一峰](http://es6.ruanyifeng.com/#docs/symbol)的文章中查看