#let和const命令
##let命令
ES6新增了`let`命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

所以，我们可以直接摒弃所有的var操作，建议替换成let操作。

```javascript
{
	let a = 10;
	var b = 1;
}
a // ReferenceError: a is not defined.
b // 1
```
	
总而言之，就是摒弃掉了之前var全局的弊端。
```javascript
for(var i = 0; i < 10; i++);console.log(i);//10
for(let i = 0; i < 10; i++);console.log(i);//error
```
**let的最大好处就是，它不会变成全局变量。**

```javascript
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1

let b = 1;
window.b // undefined
```
但是在使用let时会有一些注意事项，比如：  
_注意事项：不能延后声明变量_  
**解决方案：在使用时，最好把let赋值放在代码最上面。**

```javascript
if (true) {
	// TDZ开始
	tmp = 'abc'; // ReferenceError
	console.log(tmp); // ReferenceError

	let tmp; // TDZ结束
	console.log(tmp); // undefined

	tmp = 123;
	console.log(tmp); // 123
}
```
_注意事项：let不允许在相同作用域内，重复声明同一个变量。_  
**解决方案：在相同作用域内，避免出现命名冲突。**

```javascript
// 报错
function () {
	let a = 10;
	var a = 1;
}

// 报错
function () {
	let a = 10;
	let a = 1;
}

function func(arg) {
	{
		let arg; // 不报错
	}
}
```
##const命令

`const`命令也是用来声明变量的，But声明的是常量。皆大喜奔！！！

在严格模式下，重写const的变量是会出现报错，但是如果正常模式下，不会报错，但是修改不会有效。

并且，*const的作用域与let命令相同：只在声明所在的块级作用域内有效*，这点也需要注意。

因为通常状况下，const比较多使用在全局的状况下。

并且const命令也不能延后声明，其实var的延后声明我们可以完全抛弃。而且const声明的常量，也与let一样不可重复声明。

```javascript
const PI = 3.1415;
PI = 3; // 常规模式时，重新赋值无效，但不报错
PI // 3.1415


'use strict';
const PI = 3.1415;
PI // 3.1415
PI = 3;
// TypeError: "PI" is read-only

```
但是，我们javascript中常量很多定义成对象，不然众多常量太零散，于是我们希望有一个对象常量，就是一个冻结对象。

对象冻结，应该使用`Object.freeze`方法。

```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```
*但是Object.freeze方法并不会做深度对象冻结。*
```javascript
const foo = Object.freeze({prop:{}});
foo.prop.a = 123;
console.log(foo);
//{ prop: { a: 123 } }
```
**解决方法：下面是一个将对象彻底冻结的函数。**

```javascript
'use strict';
let constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, value) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
  return obj;
};

const foo = constantize({prop:{}});
foo.prop.a = 123;
//Can't add property a, object is not extensible
```
