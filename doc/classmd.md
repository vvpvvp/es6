# Class

##基础语法
###概述
JavaScript语言的传统方法是通过构造函数，定义并生成新对象。下面是一个例子。

```javascript
function Point(x,y){
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
}
```
ES6提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。
```javascript
//定义类
class Point {

  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }

}
```
ES6的类，完全可以看作构造函数的另一种写法。

```javascript
class Point{
  // ...
}

typeof Point // "function"
Point === Point.prototype.constructor // true

let point = new Point();
point.constructor === Point.prototype.constructor // true

Object.assign(Point.prototype, {
  toString(){},
  toValue(){}
})
```
由于类的方法（除`constructor`以外）都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。

###constructor
`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

###对象
生成实例对象的写法，与ES5完全一样，也是使用new命令。如果忘记加上new，像函数那样调用Class，将会报错。

```javascript
// 报错
var point = Point(2, 3);

// 正确
var point = new Point(2, 3);
```
与ES5一样，类的所有实例共享一个原型对象。

```javascript
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__ === p2.__proto__
//true
```

###name
```javascript
class Point {}
Point.name // "Point"
```

##Class的集成
###基本用法
Class之间可以通过extends关键字实现继承，这比ES5的通过修改原型链实现继承，要清晰和方便很多。
**子类必须在constructor方法中调用super方法，否则新建实例时会报错。**
**另一个需要注意的地方是，在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。**

```javascript
class ColorPoint extends Point {

  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }

}
```

###实例的__proto__属性
子类实例的`__proto__`属性的`__proto__`属性，指向父类实例的`__proto__`属性。也就是说，子类的原型的原型，是父类的原型。

```javascript
var p1 = new Point(2, 3);
var p2 = new ColorPoint(2, 3, 'red');

p2.__proto__ === p1.__proto__ // false
p2.__proto__.__proto__ === p1.__proto__ // true
```
##更多的应用
更加详细的信息请至[阮一峰](http://es6.ruanyifeng.com/#docs/class)的文章中查看

