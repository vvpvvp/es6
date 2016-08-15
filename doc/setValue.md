#变量的赋值
##数组的解构赋值
ES6新增了一种快速的赋值方式：

```javascript
//es5
let a = 1;
let b = 2;
let c = 3;

//es6，es6中尽量使用let替代var
let [a, b, c] = [1, 2, 3];
```
这种快速的赋值方式同样可以深度赋值，当没有对应的值时，变量的值为undefined

```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []

```
*如果左边为数组赋值，右边对应的不是数组则会报错。*  

对于Set结构，也可以使用数组的解构赋值。  
事实上，只要某种数据结构具有Iterator接口，都可以采用数组形式的解构赋值。  

```javascript

let [x, y, z] = new Set(["a", "b", "c"])
x // "a"

//Generator函数
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5

```
注：如果对Generator函数不熟悉，可以至[Generator函数](#generator)章节查看。
注：如果对Set数据结构不熟悉，可以至[Set和Map数据结构](#setAndMap)章节查看。

##对象的解构赋值
解构赋值的方式也可以应用在对象上。
对象赋值主要是通过对象的名称来关联，没有次序之分。

```javascript
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

```
如果变量名与属性名不一致，必须写成下面这样。  
**有一点需要注意，变量的命名是在后面，前面是对应的属性值名称，并且赋值为内部机制，属性名并未赋值**

```javascript
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined

```
和数组一样，解构也可以用于嵌套结构的对象。_注意，这时p是模式，不是变量，因此不会被赋值。_

```javascript
let obj = {
  p: [
    "Hello",
    { y: "World" }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p //error: p is undefined
```
下面是嵌套赋值的例子。

```javascript
let obj = {};
let arr = [];

({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });

obj // {prop:123}
arr // [true]

```
对象的解构赋值，可以很方便地将现有对象的方法，赋值到某个变量。

```javascript
let { log, sin, cos } = Math;
```
##字符串的解构赋值
字符串也可以解构赋值。

```javascript
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
_特殊应用，其实这个属于对象赋值了，如果右边是数值和布尔值，则同样也会先转为对象。_

```javascript
let {length : len} = 'hello';
len // 5
```

##函数的解构赋值
函数的参数也可以使用解构赋值。

```javascript
function add([x, y]){
  return x + y;
}

add([1, 2]) // 3

```
下面是另一个例子。

```javascript
[[1, 2], [3, 4]].map(([a, b]) => a + b)
// [ 3, 7 ]
```
注：这个方法涉及到函数的扩展。

## 默认值
解构赋值允许指定默认值，默认值对应的空判断为undefined。

```javascript
let [foo = true] = [];
foo // true

let {x = 3} = {};
x // 3

[x, y = 'b'] = ['a'] // x='a', y='b'
[x, y = 'b'] = ['a', undefined] // x='a', y='b'

let {x = 3} = {x: null};
x // null

let [x = 1] = [null];
x // null

```
**函数的默认值设置，这种赋值方式非常有效，大大减少了工作量，并让我们加强了对函数的理解。**  
有两种方式赋值，通常我们推荐第一种。

```javascript

//直接使用赋值，存在如果不传参数报错的问题
function move({x, y = 5}) {
  console.log(x, y);
}
move() // TypeError: Cannot read property 'x' of undefined


function move({x = 0, y = 0} = {}) {
  return [x, y];
}

//[{x=0,y=0}={}] = [{x: 3, y: 8}];

move({x: 3, y: 8}); // [3, 8]
//{x=0,y=0}={x: 3, y: 8}

move({x: 3}); // [3, 0]
//{x=0,y=0}={x: 3}

move({}); // [0, 0]
//{x=0,y=0}={}

move(); // [0, 0]
//{x=0,y=0}={}


//只有当没有参数时，才使用默认值
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

//[{x,y}= { x: 0, y: 0 }] = [{x: 3, y: 8}];

move({x: 3, y: 8}); // [3, 8] 
//{x,y} = {x:3, y:8}

move({x: 3}); // [3, undefined] 
//{x,y} = {x:3}

move({}); // [undefined, undefined] 
//{x,y} = {}

move(); // [0, 0] 
//{x,y} = { x: 0, y: 0 }
```


如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。

```javascript
function f(){
  console.log('aaa');
}

let [x = f()] = [1];
```
**默认值可以引用解构赋值的其他变量，但该变量必须已经声明。**

```javascript
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError
```


##注意事项
_采用这种写法时，实际就是声明了一个变量，所以当与已存在的变量命名冲突时，会产生报错。_

```javascript
let foo;
let {foo} = {foo: 1}; // SyntaxError: Duplicate declaration "foo"
let [foo] = [1]; // SyntaxError: Duplicate declaration "foo"

```
**如果要将一个已经声明的变量用于解构赋值，也必须非常小心。**

```javascript
// 错误的写法

let x;
{x} = {x: 1};
// SyntaxError: syntax error

// 正确的写法
({x} = {x: 1});

```
_如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。_

```javascript
// 报错
let {foo: {bar}} = {baz: 'baz'}
```
##圆括号问题
有以下解构赋值不得使用圆括号。

```javascript
// 全部报错
let [(a)] = [1];

let { x: (c) } = {};

let { o: ({ p: p }) } = { o: { p: 2 } };

function f([(z)]) { return z; }

({ p: a }) = { p: 42 };

([a]) = [5];

[({ p: a }), { x: c }] = [{}, {}];

```

可以使用圆括号的情况：

```javascript
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```
但是这种添加了圆括号的方式没有什么意义，_总结：解构赋值不推荐使用（）。_