#异步操作和Async函数
##基本概念
所谓"异步"，简单说就是一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

##异步的处理
这里举的例子多数用ajax，因为我们这个异步调用写的最多。
###回调函数
将后续处理作为参数传递到方法中，当异步执行完毕后，直接调用传递过来的function参数。
```javascript
$.get("url",function(result){
    console.log(result);
    //等待结果，执行异步
})
```
###Promise
jquery的ajax全部都封装好了Promise,异步处理不需要写成不断的callback。链式的处理更容易理解，逻辑处理也做到了很好的分割。
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

这种写法，定义dtd始终贯穿方法定义以及调用。而且dtd变量和方法的定义与调用几乎没有关联。其实dtd的调用只能算是一种简化的回调函数调用。 实际使用中，并不能简化开发。

###Generator函数
Generator函数是协程在ES6的实现，最大特点就是可以交出函数的执行权（即暂停执行）。
整个Generator函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用yield语句注明。Generator函数的执行方法如下。
```javascript
var fetch = require('node-fetch');
function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}

var g = gen(1);
g.next() // { value: 3, done: false }
g.next() // { value: undefined, done: true }
```
