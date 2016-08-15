#Generator和Promise
##Generator
Generator函数是ES6提供的一种异步编程解决方案，语法行为与传统函数完全不同。本章详细介绍Generator函数的语法和API。  
形式上，Generator函数是一个普通函数，但是有两个特征。一是，function命令与函数名之间有一个星号；二是，函数体内部使用yield语句，定义不同的内部状态（yield语句在英语里的意思就是“产出”）。
```javascript
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
hw.next()
// { value: 'hello', done: false }
hw.next()
// { value: 'world', done: false }
hw.next()
// { value: 'ending', done: true }
hw.next()
// { value: undefined, done: true }
```
ES6没有规定，function关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。 
一般的写法是下面这种，即星号紧跟在function关键字后面。本书也采用这种写法。
```javascript
function* foo(x, y) { ··· }
```
###使用注意事项
*需要注意，yield语句不能用在普通函数中，否则会报错。*

```javascript
(function (){
  yield 1;
})()
// SyntaxError: Unexpected number
```
下面代码也会产生句法错误，因为forEach方法的参数是一个普通函数。
```javascript
var arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  a.forEach(function (item) {
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)){
  console.log(f);
}
```
*yield语句如果用在一个表达式之中，必须放在圆括号里面。*

```javascript
console.log('Hello' + yield); // SyntaxError
console.log('Hello' + yield 123); // SyntaxError

console.log('Hello' + (yield)); // OK
console.log('Hello' + (yield 123)); // OK
```
*yield语句用作函数参数或赋值表达式的右边，可以不加括号。*
```javascript
foo(yield 'a', yield 'b'); // OK
let input = yield; // OK
```
###next方法的参数
yield句本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield语句的返回值。
```javascript
function* f() {
  for(var i=0; true; i++) {
    var reset = yield i;
    console.log(`reset:${reset}`);
    if(reset==="fromnext") { i = -1; }
  }
}

var g = f();

g.next() 
// { value: 0, done: false }
g.next() 
//reset:undefined 
// { value: 1, done: false }
g.next("fromnext") 
//reset:fromnext 
// { value: 0, done: false}
```
再看一个通过next方法的参数，向Generator函数内部输入值的例子。
```javascript
function* dataConsumer() {
  console.log('Started');
  console.log(`1. ${yield}`);
  console.log(`2. ${yield}`);
  return 'result';
}

let genObj = dataConsumer();
genObj.next();
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
```
###Generator-prototype-throw()
Generator函数返回的遍历器对象，都有一个throw方法，可以在函数体外抛出错误，然后在Generator函数体内捕获。
```javascript
var g = function* () {
  try {
    yield;
  } catch (e) {
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');//执行后函数执行完毕。
  i.throw('b');//该报错抛出由外部捕获。
} catch (e) {
  console.log('外部捕获', e);
}
// 内部捕获 a
// 外部捕获 b
```
如果Generator函数内部没有部署try...catch代码块，那么throw方法抛出的错误，将被外部try...catch代码块捕获。
```javascript
var g = function* () {
  while (true) {
    yield;
    console.log('内部捕获', e);
  }
};

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕获', e);
}
// 外部捕获 a
```
如果Generator函数内部部署了try...catch代码块，那么遍历器的throw方法抛出的错误，不影响下一次遍历，否则遍历直接终止。

```javascript
var gen = function* gen(){
  try {
    yield console.log('hello');
    yield console.log('hello2');
  } catch (e) {
    console.log("catch error");
  }
  yield console.log('world');
}

var g = gen();
g.next();
// hello
//{ value: undefined, done: false }
g.throw();
//catch error
//world
//{ value: undefined, done: false }
g.next();
//{ value: undefined, done: true }
```
上面代码在两次next方法之间，使用throw方法抛出了一个错误。由于这个错误在Generator函数内部被捕获了，所以不影响第二次next方法的执行。  
*使用Generator函数可以大大简化异常的处理。*
```javascript
function* g(){
  try {
    var a = yield foo('a');
    var b = yield foo('b');
    var c = yield foo('c');
  } catch (e) {
    console.log(e);
  }

  console.log(a, b, c);
}

//或者
function *foo() {
  var x = yield 3;
  var y = x.toUpperCase();
  yield y;
}
var it = foo();
it.next(); // { value:3, done:false }
try {
  it.next(42);
} catch (err) {
  console.log(err);
}

```
###Generator-prototype-return()
Generator函数返回的遍历器对象，还有一个return方法，可以返回给定的值，并且终结遍历Generator函数。
```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()        // { value: 1, done: false }
g.return("foo") // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```
如果Generator函数内部有try...finally代码块，那么return方法会推迟到finally代码块执行完再执行。
```javascript
function* numbers () {
  yield 1;
  try {
    yield 2;
    yield 3;
  } finally {
    yield 4;
    yield 5;
  }
  yield 6;
}
var g = numbers()
g.next() // { done: false, value: 1 }
g.next() // { done: false, value: 2 }
g.return(7) // { done: false, value: 4 }
g.next() // { done: false, value: 5 }
g.next() // { done: true, value: 7 }
```
###yield*语句
如果在Generater函数内部，调用另一个Generator函数，默认情况下是没有效果的。
```javascript
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  foo();
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "y"
```
这个就需要用到yield*语句，用来在一个Generator函数里面执行另一个Generator函数。
```javascript
function* foo() {
  yield 'a';
  yield 'b';
}
function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}
for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
```
###作为对象属性的Generator函数
如果一个对象的属性是Generator函数，可以简写成下面的形式。
```javascript
let obj = {
  * myGeneratorMethod() {
    ···
  }
};
```
##Promise
Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6将其写进了语言标准，统一了用法，原生提供了`Promise`对象。
###基本用法
ES6规定，Promise对象是一个构造函数，用来生成Promise实例。  
下面代码创造了一个Promise实例。
```javascript
var promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});

promise.then(function(value) {
  // success
}, function(value) {
  // failure
});
```
###resolve函数和reject函数
如果调用resolve函数和reject函数时带有参数，那么它们的参数会被传递给回调函数。  
当resolve函数传递的参数为另外一个Promise时，当前resolve函数会等待另外一个Promise的返回。而作为参数的Promise(`p2`)对象会将状态传递给当前的Promise(`p1`)对象。
```javascript
var p1 = new Promise(function (resolve, reject) {
  setTimeout(() => reject("Error: fail"), 3000)
})
var p2 = new Promise(function (resolve, reject) {
  setTimeout(() => resolve(p1), 1000)
})
p2.then(result => console.log(result))
p2.catch(error => console.log(error))
// Error: fail
```
这种写法为之后的链式写法提供了依据。
###then()
这个很好理解，与Jquery之前写的调用差不多。
```javascript
getJSON("/posts.json").then(function(post) {
  // ...
});
```
参数：当Promise返回成功时，执行第一个函数，失败执行第二个。
```javascript
getJSON("/post/1.json").then(
  comments => console.log("Resolved: ", comments),
  err => console.log("Rejected: ", err)
);
```
上面代码中，第一个`then`方法指定的回调函数，返回的是另一个Promise对象。这时，第二个`then`方法指定的回调函数，就会等待这个新的Promise对象状态发生变化。如果变为Resolved，就调用funcA，如果状态变为Rejected，就调用funcB。

###catch()
Promise.prototype.catch方法是.then(null, rejection)的别名，用于指定发生错误时的回调函数。
但是同时，Promise.prototype.catch方法也会在Promise执行报错时执行。
```javascript
var promise = new Promise(function(resolve, reject) {
  throw new Error('test');
});
promise.catch(function(error) {
  console.log(error);
});
// Error: test

var promise = new Promise(function(resolve, reject) {
  reject("Error: test");
});
promise.catch(function(error) {
  console.log(error);
});
// Error: test

// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

//我们一般推荐下面的写法
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```
跟传统的try/catch代码块不同的是，如果没有使用catch方法指定错误处理的回调函数，Promise对象在未执行完毕时抛出的错误不会传递到外层代码，即不会有任何反应。  
```javascript
var promise = new Promise(function(resolve, reject) {
  setTimeout(function() {
      throw new Error('test');
      resolve("ok");
  }, 10);
});
promise.then(function(value) { console.log(value) });
// Uncaught Error: test


var promise = new Promise(function(resolve, reject) {
  resolve("ok");
  setTimeout(function() { throw new Error('test') }, 0)
});
promise.then(function(value) { console.log(value) });
// ok
// Uncaught Error: test
```
注意，Chrome浏览器不遵守这条规定，它会抛出错误。

###done()
Promise对象的回调链，不管以then方法或catch方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为Promise内部的错误不会冒泡到全局）。因此，我们可以提供一个done方法，总是处于回调链的尾端，保证抛出任何可能出现的错误。



###Promise.all
Promise.all方法用于将多个Promise实例，包装成一个新的Promise实例。  
在这个实例包含的Promise中，所有的Promise执行完毕才会执行当前的Promise。
```javascript
var p = Promise.all([p1, p2, p3]);
```
p的状态由p1、p2、p3决定，分成两种情况。  

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。  

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。  
###Promise.race
Promise.race方法同样是将多个Promise实例，包装成一个新的Promise实例。
```javascript
var p = Promise.race([p1, p2, p3]);
```
上面代码中，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的Promise实例的返回值，就传递给p的回调函数。

###Promise.resolve
有时需要将现有对象转为Promise对象，Promise.resolve方法就起到这个作用。
```javascript
var jsPromise = Promise.resolve($.ajax('/whatever.json'));
```
上面代码将jQuery生成的deferred对象，转为一个新的Promise对象。  
上面的代码能够转为Promise对象是因为对象拥有then方法。  
Promise.resolve方法的参数分成两种情况。  
*（1）参数是一个thenable对象  *
比如传递的是Promise实例，或者对象具有then方法。
```javascript
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
  console.log(value);  // 42
});
```
*（2）参数不是具有then方法的对象，或根本就不是对象，或没有参数  *
如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的Promise对象，状态为Resolved。
```javascript
var p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello

var d = Promise.resolve();

d.then(function () {
  // ...
});
```

###Promise.reject()
Promise.reject(reason)方法也会返回一个新的Promise实例，该实例的状态为rejected。它的参数用法与Promise.resolve方法完全一致。
```javascript
var p = Promise.reject('出错了');
// 等同于
var p = new Promise((resolve, reject) => reject('出错了'))

p.then(null, function (s){
  console.log(s)
});
// 出错了
```

```javascript
```