# Module

##export 和 import
模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。下面是一个JS文件，里面使用`export`命令输出变量。

```javascript
// profile.js
export let firstName = 'Michael';
export let lastName = 'Jackson';
export let year = 1958;

//另外一种方法写起来会更好一点
// profile.js
let firstName = 'Michael';
let lastName = 'Jackson';
let year = 1958;
export {firstName, lastName, year};
//重命名
export {
  v1 as firstName,
  v2 as lastName,
  v2 as year
};

// main.js
import {firstName, lastName, year} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}

//另外一种方法
import { ln as lastName } from './profile';
```
##模块的整体加载

```javascript
// profile.js
let firstName = 'Michael';
let lastName = 'Jackson';
let year = 1958;
export {firstName, lastName, year};

// main.js
import * as user from './profile';
console.log(user.firstName);
console.log(user.lastName);
console.log(user.year);
```
##export default
为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。
无需为整个模块命名，import也不需要知道export的参数名。
```javascript
// profile.js
let firstName = 'Michael';
let lastName = 'Jackson';
let year = 1958;
export default function crc32() {
  // ...
};

// main.js
import some from './profile';
some();
```
##模块的继承
模块之间也可以继承。
假设有一个circleplus模块，继承了circle模块。
```javascript
// circleplus.js

export * from 'circle';
export var e = 2.71828182846;
export default function(x) {
  return Math.exp(x);
}
```
上面代码中的`export *`，表示再输出`circle`模块的所有属性和方法。注意，`export *`命令会忽略`circle`模块的`default`方法。然后，上面代码又输出了自定义的e变量和默认方法。

##更多深入的内容
更加详细的加载机制等信息请至[阮一峰](http://es6.ruanyifeng.com/#docs/module)的文章中查看