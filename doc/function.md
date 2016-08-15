#Function的扩展
##函数参数的默认值以及和解构赋值默认值结合使用
这里已经在[变量的结构赋值](#setValue)的默认值章节中说明。 

##函数的length属性
函数的length属性返回函数预期传入的参数个数。
```javascript
(function(a){}).length // 1
(function(a = 5){}).length // 0
(function(a, b, c = 5){}).length // 2
(function(...args) {}).length // 0
```
##关于参数默认值的作用域
与其他变量的作用域规则是一样的，即先是当前函数的作用域，然后才是全局作用域。
```javascript
var x = 1;

function f(x, y = x) {
  //x=2,y=x->y=2
  console.log(y);
}

f(2) // 2

```

如果调用时，函数作用域内部的变量x没有生成，结果就会不一样。

```javascript
let x = 1;

function f(y = x) {
  //y=1
  let x = 2;
  console.log(y);
}

f() // 1

```
##应用

