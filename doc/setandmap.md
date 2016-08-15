#Set和Map
##Set
###基本用法
ES6提供了新的数据结构Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。  
*向Set加入值的时候，不会发生类型转换，所以5和"5"是两个不同的值。*
```javascript
var set = new Set([1, 2, 3, 4, 4])
set.size // 4
[...set]
// [1, 2, 3, 4]

set.add("4");
set.size // 5
[...set]
// [1, 2, 3, 4,"4"]

var set = new Set([...document.querySelectorAll('div')])
set.size // 56
```
Set内部判断两个值是否不同，使用的算法叫做“Same-value equality”，相当于[Object.is()](#object)。  
*针对Set的特性，不建议存储对象，因为{}与{}是不相等的*

###Set实例的属性和方法
Set结构的实例有以下属性。
- `Set.prototype.constructor`：构造函数，默认就是Set函数。
- `Set.prototype.size`：返回Set实例的成员总数。

Set实例的方法:

- `add(value)`：添加某个值，返回Set结构本身。
- `delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
- `has(value)`：返回一个布尔值，表示该值是否为Set的成员。
- `clear()`：清除所有成员，没有返回值。

```javascript
s.add(1).add(2).add(2);
// 注意2被加入了两次

s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2);//true
s.has(2) // false

```
###Set的应用
使用Set，可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。

```javascript
let a = [1, 2, 3];
let b = [4, 3, 2];

// 并集
let union = new Set([...a, ...b]);
// [1, 2, 3, 4]

// 交集
let intersect = new Set(a.filter(x => b.has(x)));
// [2, 3]

// 差集
let difference = new Set(a.filter(x => !b.has(x)));
// [1]
```
Set结构的实例的forEach方法，用于对每个成员执行某种操作，没有返回值。

```javascript
let set = new Set([1, 2, 3]);
set.forEach((value, key) => console.log(value * 2) )
// 2
// 4
// 6
```
如果想在遍历操作中，同步改变原来的Set结构，目前没有直接的方法，但有两种变通方法。一种是利用原Set结构映射出一个新的结构，然后赋值给原来的Set结构；另一种是利用Array.from方法。

```javascript
// 方法一
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));
// set的值是2, 4, 6

// 方法二
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
// set的值是2, 4, 6
```

##Map
##Map结构的目的和基本用法
JavaScript的对象（Object），本质上是键值对的集合（Hash结构），但是只能用字符串当作键。这给它的使用带来了很大的限制。
为了解决这个问题，ES6提供了Map数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object结构提供了“字符串—值”的对应，
```javascript
var m = new Map();
var o = {};

m.set(o, "content")
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```
Map的初始化方法：
```javascript
var map = new Map([["name", "张三"], ["title", "Author"]]);

map.size // 2
map.has("name") // true
map.get("name") // "张三"
map.has("title") // true
map.get("title") // "Author"
map.get("title1") // undefined
```
注意，只有对同一个对象的引用，Map结构才将其视为同一个键。这一点要非常小心。
```javascript
var map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
//因为['a']!=['a']
```
###实例的属性和操作方法
Map结构的实例有以下属性和操作方法。
属性:
- `size`:属性返回Map结构的成员总数。

方法：
- `set(key, value)`:设置key所对应的键值，然后返回Map结构。如果key已经有值，则键值会被更新，否则就新生成该键。
- `get(key)`:读取key对应的键值，如果找不到key，返回undefined。
- `delete(key)`:删除某个键，返回true。如果删除失败，返回false。
- `has(key)`:返回一个布尔值，表示某个键是否在Map数据结构中。
- `clear()`:清除所有成员，没有返回值。

```javascript
let map = new Map();
map.set('foo', true);
map.set('bar', false);

map.size // 2

map.get('bar');//false
map.get('bar1');//undefined

map.has("bar") // true
map.delete('bar');//true
map.has("bar") // false

map.clear();
map.has("foo") // false

```
Map还有一个forEach方法，与数组的forEach方法类似，也可以实现遍历。
```javascript
map.forEach(function(value, key, map)) {
  console.log("Key: %s, Value: %s", key, value);
};
```
forEach方法还可以接受第二个参数，用来绑定this。

```javascript
var reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};

map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter);
```
上面代码中，forEach方法的回调函数的this，就指向reporter。

##WeakSet和WeakMap
WeakSet结构与Set类似，也是不重复的值的集合。但是，它与Set有两个区别。  
首先，WeakSet的成员只能是对象，而不能是其他类型的值。 

同理，WeakMap和Map结构基本类似，唯一的区别是它只接受对象作为键名（null除外），不接受其他类型的值作为键名，而且键名所指向的对象，不计入垃圾回收机制。

```javascript
var wm = new WeakMap();
var element = document.querySelector(".element");

wm.set(element, "Original");
wm.get(element) // "Original"

element.parentNode.removeChild(element);
element = null;
wm.get(element) // undefined
```
WeakMap应用的典型场合就是DOM节点作为键名，或者设置一些对象的私有属性。
```javascript
let myElement = document.getElementById('logo');
let myWeakmap = new WeakMap();

myWeakmap.set(myElement, {timesClicked: 0});

myElement.addEventListener('click', function() {
  let logoData = myWeakmap.get(myElement);
  logoData.timesClicked++;
  myWeakmap.set(myElement, logoData);
}, false);
```
这里对这两种对象不做多描述，详细文档至[阮一峰](http://es6.ruanyifeng.com/#docs/set-map#WeakSet)的文档。