#关于...参数
##函数传参时
ES6引入rest参数（形式为“...变量名”），用于获取函数的多余参数，这样就不需要使用arguments对象了。

```javascript
function add(...values) {
    for (var val of values) {
        console.log(val);
    }
}

add(2, 5, 3);
// 2
// 5
// 3

function add1(a,...values) {
    //a:2
    //values:[5, 3]
}

add1(2, 5, 3);


function push(array, ...items) {
  array.push(...items);
}

push([1,2,3],4,5,6)
//[1,2,3,4,5,6]


function add2(x, y) {
  return x + y;
}

add2(...[4, 38]) 
// 42

```
扩展运算符与正常的函数参数可以结合使用，非常灵活。

```javascript
function f(v, w, x, y, z) { }
var args = [0, 1];
f(-1, ...args, 2, ...[3]);
```
注意，rest参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。

```javascript
// 报错
function f(a, ...b, c) {
  // ...
}
```
##扩展运算符
###含义
扩展运算符（spread）是三个点（...）。它好比rest参数的逆运算，将一个数组转为用逗号分隔的参数序列。

```javascript
// 报错
console.log(...[1, 2, 3])
// 1 2 3

console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5

[...document.querySelectorAll('div')]
// [<div>, <div>, <div>]
```
###应用
####合并数组
扩展运算符提供了数组合并的新写法。
```javascript
[1, 2].concat(more)
// ES6
[1, 2, ...more]
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

// ES5的合并数组
arr1.concat(arr2, arr3));
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```
####与解构赋值结合
扩展运算符可以与解构赋值结合起来，用于生成数组。
这里和方法的赋值机制很类似
```javascript
// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list
```
####实现了Iterator接口的对象
**任何Iterator接口的对象，都可以用扩展运算符转为真正的数组。**

```javascript
//类数组结构
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
//Map
let map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

let arr = [...map.keys()]; // [1, 2, 3]
[...map.values()]// ['one', 'two', 'three']
[...map.entries()]// [[1,'one'], [2, 'two'], [3, 'three']]
[...map]// [[1,'one'], [2, 'two'], [3, 'three']]

//Map
let set = new Set([1,2,3]);

let arr = [...set.keys()]; // [1, 2, 3]
[...set.values()]// [1, 2, 3]
[...set.entries()]// [[1,1], [2, 2], [3,3]]
[...set]// [1,2,3]


var go = function*(){
  yield 1;
  yield 2;
  yield 3;
};

[...go()] // [1, 2, 3]
//这里可以直接循环go，直接执行所有的next()。
Array.from(numbers()) // [1, 2]

let [x, y] = numbers();
x // 1
y // 2

```