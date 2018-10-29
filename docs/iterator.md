#Iterator和for...of循环
##Iterator
###Iterator（遍历器）的概念
JavaScript原有的表示“集合”的数据结构，主要是数组（Array）和对象（Object），ES6又添加了Map和Set。这样就有了四种数据集合，用户还可以组合使用它们，定义自己的数据结构，比如数组的成员是Map，Map的成员是对象。这样就需要一种统一的接口机制，来处理所有不同的数据结构。  
Iterator的作用有三个：
- 一是为各种数据结构，提供一个统一的、简便的访问接口；
- 二是使得数据结构的成员能够按某种次序排列；
- 三是ES6创造了一种新的遍历命令for...of循环，Iterator接口主要供for...of消费


###自带Iterator的对象
自带Iterator的对象：
- 数组
- Set和Map结构
- 某些类似数组的对象（比如arguments对象、DOM NodeList对象）
- Generator对象
- 字符串


####关于Symbol.iterator方法
ES6规定，默认的Iterator接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就可以认为是“可遍历的”（iterable）。调用`Symbol.iterator`方法，就会得到当前数据结构默认的遍历器生成函数。`Symbol.iterator`本身是一个表达式，返回Symbol对象的iterator属性，这是一个预定义好的、类型为Symbol的特殊值，所以要放在方括号内。

```javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```
下面是另一个为对象添加Iterator接口的例子。
```javascript
let obj = {
  data: [ 'hello', 'world' ],
  [Symbol.iterator]() {
    const self = this;
    let index = 0;
    return {
      next() {
        if (index < self.data.length) {
          return {
            value: self.data[index++],
            done: false
          };
        } else {
          return { value: undefined, done: true };
        }
      }
    };
  }
};

for(let o of obj){
    console.log(o);
}
//"hello"
//"world"

```

###调用Iterator接口的场合
###对象
对于普通的对象，`for...of`结构不能直接使用，会报错，必须部署了`iterator`接口后才能使用。  
一种解决方法是，使用`Object.keys`方法将对象的键名生成一个数组，然后遍历这个数组。
```javascript
for (var key of Object.keys(someObject)) {
  console.log(key + ": " + someObject[key]);
}
```
####解构赋值
```javascript
let set = new Set().add('a').add('b').add('c');
let [x,y] = set;
// x='a'; y='b'
let [first, ...rest] = set;
// first='a'; rest=['b','c'];
```
####扩展运算符
```javascript
// 例一
var str = 'hello';
[...str] //  ['h','e','l','l','o']

// 例二
let arr = ['b', 'c'];
['a', ...arr, 'd']
// ['a', 'b', 'c', 'd']
```
####yield*
```javascript
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```

##for...of循环
一个数据结构只要部署了`Symbol.iterator`属性，就被视为具有iterator接口，就可以用`for...of`循环遍历它的成员。也就是说，`for...of`循环内部调用的是数据结构的`Symbol.iterator`方法。
###字符串
对于字符串来说，for...of循环还有一个特点，就是会正确识别32位UTF-16字符。
```javascript
const arr ="adfsf\uD83D\uDC0A";
let iterator  = arr[Symbol.iterator]();

for(let v of arr) {
  console.log(v); // "a" "d" "f" "s" "f" '\uD83D\uDC0A'
}
```
###数组
数组原生具备iterator接口
```javascript
const arr = ['red', 'green', 'blue'];
let iterator  = arr[Symbol.iterator]();

for(let v of arr) {
  console.log(v); // red green blue
}
```
###Set
Set结构原生提供三个遍历器生成函数。
- `keys()`：返回一个键名的遍历器
- `values()`：返回一个键值的遍历器
- `entries()`：返回一个键值对的遍历器

```javascript
let set = new Set(['red', 'green', 'blue']);

for ( let item of set.keys() ){
  console.log(item);
}
// red
// green
// blue

for ( let item of set.values() ){
  console.log(item);
}
// red
// green
// blue

for ( let item of set.entries() ){
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]

for ( let [key, value] of set.entries()){
  console.log(key,value);
}
// "red" "red"
// "green" "green"
// "blue" "blue"

// 等同于使用set.values()
for (let key of set) {
  console.log(key);
}
```
###Map
Map结构原生提供三个遍历器生成函数。
- `keys()`：返回一个键名的遍历器
- `values()`：返回一个键值的遍历器
- `entries()`：返回一个键值对的遍历器

```javascript
let map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
```
###Generator函数
```javascript
function *foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
```
上面代码使用for...of循环，依次显示5个yield语句的值。这里需要注意，*一旦next方法的返回对象的done属性为true，for...of循环就会中止，且不包含该返回对象，所以上面代码的return语句返回的6*，不包括在for...of循环之中。

下面是一个利用Generator函数和for...of循环，实现斐波那契数列的例子。
```javascript
function* fibonacci() {
  let [prev, curr] = [0, 1];
  for (;;) {
    [prev, curr] = [curr, prev + curr];
    yield curr;
  }
}

for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```
###类似数组的对象
类似数组的对象包括好几类。下面是for...of循环用于字符串、DOM NodeList对象、arguments对象的例子。
```javascript
// 字符串
let str = "hello";

for (let s of str) {
  console.log(s); // h e l l o
}

// DOM NodeList对象
let paras = document.querySelectorAll("p");

for (let p of paras) {
  p.classList.add("test");
}

// arguments对象
function printArgs() {
  for (let x of arguments) {
    console.log(x);
  }
}
printArgs('a', 'b');
// 'a'
// 'b'
```
*并不是所有类似数组的对象都具有iterator接口，一个简便的解决方法，就是使用Array.from方法将其转为数组。*
```javascript
let arrayLike = { length: 2, 0: 'a', 1: 'b' };
// 报错
for (let x of arrayLike) {
  console.log(x);
}

// 正确
for (let x of Array.from(arrayLike)) {
  console.log(x);
}
```