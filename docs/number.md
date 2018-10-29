#Number的扩展

##二进制和八进制表示法
从ES5开始，在严格模式之中，八进制就不再允许使用前缀0表示，ES6进一步明确，要使用前缀0o表示。  
ES6提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示。  
_如果要将0b和0x前缀的字符串数值转为十进制，要使用Number方法_

```javascript
0b111110111 === 503 // true
0o767 === 503 // true

Number('0b111')  // 7
Number('0o10')  // 8
```
##Number对象扩展
###Number.isFinite(), Number.isNaN()
ES6在Number对象上，新提供了`Number.isFinite()`和`Number.isNaN()`两个方法，用来检查`Infinite`和`NaN`这两个特殊值。

```javascript
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```
`Number.isNaN()`用来检查一个值是否为NaN。

```javascript
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true'/0) // true
Number.isNaN('true'/'true') // true
```
与传统的全局方法`isFinite()`和`isNaN()`的比较

```javascript
isFinite(25) // true
isFinite("25") // true
Number.isFinite(25) // true
Number.isFinite("25") // false

isNaN(NaN) // true
isNaN("NaN") // true
Number.isNaN(NaN) // true
Number.isNaN("NaN") // false
```
###Number.parseInt(), Number.parseFloat()
ES6将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。

```javascript
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```
###Number.isInteger()
Number.isInteger()用来判断一个值是否为整数。

```javascript
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```
###Number.EPSILON
ES6在Number对象上面，新增一个极小的常量Number.EPSILON。  
引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。我们知道浮点数计算是不精确的。

```javascript
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// '0.00000000000000022204'

0.1 + 0.2
// 0.30000000000000004

//但是如果这个误差能够小于Number.EPSILON，我们就可以认为得到了正确结果。
//因此，Number.EPSILON的实质是一个可以接受的误差范围。

function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON
}
withinErrorMargin(0.1 + 0.2, 0.3)
// true
withinErrorMargin(0.2 + 0.2, 0.3)
// false

```

###Number.isSafeInteger()
JavaScript能够准确表示的整数范围在-2^53到2^53之间（不含两个端点），超过这个范围，无法精确表示这个值。  
ES6引入了Number.MAX_SAFE_INTEGER和Number.MIN_SAFE_INTEGER这两个常量，用来表示这个范围的上下限。

```javascript
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
// true

Number.MAX_SAFE_INTEGER === 9007199254740991
// true

Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
// true
Number.MIN_SAFE_INTEGER === -9007199254740991
// true

Number.isSafeInteger(3) // true
Number.isSafeInteger(1.2) // false
Number.isSafeInteger(9007199254740990) // true
Number.isSafeInteger(9007199254740992) // false
```
##Math对象的扩展
###Math.trunc()
`Math.trunc`方法用于去除一个数的小数部分，返回整数部分。  
_如果非整数，内部会将其转为数值。_
*对于空值和无法截取整数的值，返回NaN。*

```javascript
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4

Math.trunc('123.456') // 123
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
```
###Math.sign()
`Math.sign`方法用来判断一个数到底是正数、负数、还是零。  
_如果非整数，内部会将其转为数值。_
它会返回五种值。  
* 参数为正数，返回+1；
* 参数为负数，返回-1；
* 参数为0，返回0；
* 参数为-0，返回-0;
* 其他值，返回NaN。

```javascript
Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN
Math.sign('foo'); // NaN
Math.sign();      // NaN
```
###Math.cbrt()
`Math.cbrt`方法用于计算一个数的立方根。

```javascript
Math.cbrt(-1) // -1
Math.cbrt(0)  // 0
Math.cbrt(9)  // 3
Math.cbrt(2)  // 1.2599210498948734
```
###Math.clz32()
JavaScript的整数使用32位二进制形式表示，`Math.clz32`方法返回一个数的32位无符号整数形式有多少个前导0。

```javascript
Math.clz32(0) // 32
Math.clz32(1) // 31
Math.clz32(1000) // 22
Math.clz32(0b01000000000000000000000000000000) // 1
```
###Math.imul()
`Math.imul`方法返回带符号整数相乘的结果。

```javascript
Math.imul(2, 4);          // 8
Math.imul(-1, 8);         // -8
Math.imul(-2, -2);        // 4
```

###Math.fround()
`Math.fround`方法返回一个数的单精度浮点数形式。

```javascript
Math.fround(0);     // 0
Math.fround(1);     // 1
Math.fround(1.337); // 1.3370000123977661
Math.fround(1.5);   // 1.5
Math.fround(NaN);   // NaN
```
###Math.hypot()
`Math.hypot`方法返回所有参数的平方和的平方根。

```javascript
Math.hypot(3, 4);        // 5
//3的平方加上4的平方，等于5的平方。
```
##对数方法
对数方法我就不多描述，如果想要看的人可以去[阮一峰](http://es6.ruanyifeng.com/#docs/number)的文章中查看。
* Math.expm1():返回ex - 1，即Math.exp(x) - 1。
* Math.log1p(x):返回1 + x的自然对数，即Math.log(1 + x)。
* Math.log10(x):返回以10为底的x的对数。
* Math.log2(x):返回以2为底的x的对数。

##三角函数方法
ES6新增了6个三角函数方法。
* Math.sinh(x) 返回x的双曲正弦（hyperbolic sine）
* Math.cosh(x) 返回x的双曲余弦（hyperbolic cosine）
* Math.tanh(x) 返回x的双曲正切（hyperbolic tangent）
* Math.asinh(x) 返回x的反双曲正弦（inverse hyperbolic sine）
* Math.acosh(x) 返回x的反双曲余弦（inverse hyperbolic cosine）
* Math.atanh(x) 返回x的反双曲正切（inverse hyperbolic tangent）

##指数运算符
ES7新增了一个指数运算符（\*\*），目前Babel转码器已经支持。

```javascript
2 ** 2 // 4
2 ** 3 // 8
//指数运算符可以与等号结合，形成一个新的赋值运算符（**=）
let a = 2;
a **= 2;
// 等同于 a = a * a;
```
