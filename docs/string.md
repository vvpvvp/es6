# String的扩展
## 新添加的属性以及方法
### 字符串的遍历
ES6为字符串添加了遍历器接口，可以直接使用for循环：

```javascript
for (let codePoint of 'foo') { console.log(codePoint) } 
// "f"
// "o" 
// "o" 
```

并且，如果使用遍历器循环，可以正确的识别超过范围的Unicode：

```javascript
var text = String.fromCodePoint(0x20BB7); 
for (let i = 0; i < text.length; i++) { console.log(text[i]); } // " " // " " 
for (let i of text) { console.log(i); } // " 
```

###includes(), startsWith(), endsWith()

* 传统上，JavaScript只有indexOf方法，可以用来确定一个字符串是否包含在另一个字符串中。
ES6又提供了三种新方法。
* `includes()`：返回布尔值，表示是否找到了参数字符串。
* `startsWith()`：返回布尔值，表示参数字符串是否在源字符串的头部。
* `endsWith()`：返回布尔值，表示参数字符串是否在源字符串的尾部。

```javascript
var s = 'Hello world!';
s.startsWith('Hello') // true 
s.endsWith('!') // true 
s.includes('o') // true 
```
这三个方法都支持第二个参数，表示开始搜索的位置。

```javascript
var s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

###repeat
`repeat`方法返回一个新字符串，表示将原字符串重复n次。

```javascript
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""

//参数如果是小数，会被取整。
'na'.repeat(2.9) // "nana"
//如果repeat的参数是负数或者Infinity，会报错。
'na'.repeat(Infinity)
// RangeError
'na'.repeat(-1)
// RangeError

//0
'na'.repeat(-0.9) // ""
'na'.repeat(NaN) // ""
'na'.repeat('na') // ""

'na'.repeat('3') // "nanana"

```

###padStart(),padEnd()

ES7推出了字符串补全长度的功能。如果某个字符串不够指定长度，会在头部或尾部补全。`padStart`用于头部补全，`padEnd`用于尾部补全。

```javascript
'x'.padStart(5, 'a') // 'aaaax'
'x'.padStart(5, 'ab') // 'ababx'

'x'.padEnd(5, 'b') // 'xbbbb'
'x'.padEnd(4, 'ab') // 'xaba'
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
 'xxx'.padStart(2, 'ab') // 'xxx'
'xxx'.padEnd(2, 'ab') // 'xxx'
如果省略第二个参数，则会用空格补全长度。
 'x'.padStart(4) // '   x'
'x'.padEnd(4) // 'x   '
```

##ES6加强了对Unicode的支持，并且扩展了字符串对象。
JavaScript允许采用\uxxxx形式表示一个字符，其中“xxxx”表示字符的码点。
但是，这种表示法只限于\u0000——\uFFFF之间的字符。超出这个范围的字符，必须用两个双字节的形式表达。
ES6在读取超过4位的Unicode做出了改进，只要将码点放入大括号，就能正确解读该字符。

```javascript
"\uD842\uDFB7"
// "𠮷"

"\u20BB7"
// " 7"

"\u{20BB7}"
// "𠮷"

'\u{1F680}' === '\uD83D\uDE80'
// true
```

ES6加强了对超过\u0000——\uFFFF之间的范围的字符处理，对之前很多string的方法加了很多新的函数，替换以前的方法。

### codePointAt

ES6提供了`codePointAt`方法，替代了ES5的`charCodeAt`方法，返回一个字符的码点。

```javascript
var s = "𠮷";
//"\uD842\uDFB7"
//"\u20BB7"


s.length // 2
s.charCodeAt(0).toString(16)  // D842
s.charCodeAt(1).toString(16)  // DFB7
s.codePointAt(0).toString(16) // "20bb7"
```

###String.fromCodePoint()
ES6提供了`String.fromCodePoint`方法，替代了ES5提供的`String.fromCharCode`方法，用于从码点返回对应字符。

```javascript
 String.fromCharCode(0x20BB7)
// "ஷ"
String.fromCodePoint(0x20BB7)
// "告"
```

###at()
ES6提供了字符串的`at()`方法，替代了ES5提供的`charAt`方法，用于从码点返回对应字符。

```javascript
'𠮷'.charAt(0) // "\uD842"
'𠮷'.at(0) // "𠮷"
```

###normalize()
ES6提供字符串实例的`normalize()`方法，用来将字符的不同表示方法统一为同样的形式，这称为Unicode正规化。

```javascript
'\u01D1'==='\u004F\u030C' //false

'\u01D1'.normalize() === '\u004F\u030C'.normalize()
// true
```

##模板字符串
ES6引入了模板字符串解决了字符串连接的问题。
```javascript
$("#result").append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```
模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。  
如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。  
如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。
```javascript
var greeting = `\`Yo\` World!`;

$("#warning").html(`
  <h1>Watch out!</h1>
  <p>Unauthorized hockeying can result in penalties
  of up to ${maxPenalty} minutes.</p>
`);
```
模板字符串中嵌入变量，需要将变量名写在`${}`之中。  
大括号内部可以放入任意的JavaScript表达式，可以进行运算，以及引用对象属性。
```javascript
var x = 1;
var y = 2;

`${x} + ${y} = ${x + y}`
// "1 + 2 = 3"

`${x} + ${y * 2} = ${x + y * 2}`
// "1 + 4 = 5"

var obj = {x: 1, y: 2};
`${obj.x + obj.y}`
// 3
```
模板字符串之中还能调用函数。
```javascript
function fn() {
  return "Hello World";
}

`foo ${fn()} bar`
// foo Hello World bar
```
##String.raw
ES6还为原生的String对象，提供了一个raw方法。

`String.raw`方法，往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。
```javascript
String.raw`Hi\n${2+3}!`;
// "Hi\\n5!"

String.raw`Hi\u000A!`;
// 'Hi\\u000A!'

//如果原字符串的斜杠已经转义，那么String.raw不会做任何处理。
String.raw`Hi\\n`
// "Hi\\n"
```
`String.raw`方法可以作为处理模板字符串的基本方法，它会将所有变量替换，而且对斜杠进行转义，方便下一步作为字符串来使用。  
`String.raw`方法也可以作为正常的函数使用。这时，它的第一个参数，应该是一个具有raw属性的对象，且raw属性的值应该是一个数组。
```javascript
String.raw({ raw: 'test' }, 0, 1, 2);
// 't0e1s2t'

// 等同于
String.raw({ raw: ['t','e','s','t'] }, 0, 1, 2);
```