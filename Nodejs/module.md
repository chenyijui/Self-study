Module是什麼？
===
### 使用外部檔案
要寫出模組化的程式就必須把程式切分的乾淨. node.js 遵照 CommonJS 的慣例, 用 `require` 以及` exports` 來作檔案和模組之間的溝通.
### Q&A
#### 下面 3 種語法有什麼不同呢?
``` javascript
const something = require( './something' );
const something = require( './something.js' );
const something = require( 'something' );
```
第一種和第二種作法基本上一樣. 如果 node.js 找不到 `./something` 這個檔案便會自動尋找 `./something.js`. 所以基本上用 `./something.j` 會快一點點. 至於第三種呢, node.js 會尋找叫做 something 的套件.

#### exports VS module.exports
他們很相似但是還是有些不同. 底下兩種寫法用起來一樣.
#### 用 `module.exports`
```javascript
//something.js
module.exports = {
    do_a: function(){
        return ('A');
    },
    do_b: function() {
        return ('B');
    }
}
```
#### 用 `exports`
```javascript
// justsomething.js
exports.do_a = function() {
    return ('a');
};
exports.do_b = function() {
    return ('b');
};
```
#### 以上兩者我們都可以像下面這樣使用
```javascript
//app.js
const something = require('./something');
const justsomething = require('./justsomething');

console.log(something.do_a());// A
console.log(something.do_b());// B
console.log('=====');
console.log(justsomething.do_a());// a
console.log(justsomething.do_b());// b
console.log('=========');
console.log(something);// { do_a: [Function: do_a], do_b: [Function: do_b] }
console.log(justsomething); //  { do_a: [Function], do_b: [Function] }
```

### 關於 require 這個方法

`require` 這個方法會實體化一個叫 `Module` 的 Class，並自我執行一個匿名函式。

- example
```javascript
//a.js
module.exports = '123';
```
這個模組很單純，會回傳字串 '123'。如果在 b.js 中要調用，執行：

```javascript
// b.js
var a = require('./a.js'); // a 為 123;
```
所以以白話講，`module.exports` 所被賦予的值，就會成為 `require` 之後的回傳值。


```javascript
// 這段示意 "require('./a.js')" 會做的事
// Module 的建構式，
// Module 擁有一些屬性，其中一個是 'exports'
function Module() {
  // other setup
  this.exports = {};
}

var module = new Module();
(function(exports, require, module){
  // a.js 的內容
  module.exports = '123';
})(module.exports, require, module);
```
注意到 a.js 的內容是在該自動執行的匿名函式中執行的，所以可以看到在 a.js 的 closure 中，會有三個變數 `exports`、`require` 及 `module`，也就是在定義模組時很常用到的三個關鍵字。

從自動執行匿名函式丟入的參數 `(module.exports, require, module)` 可以知道 a.js closure 中的 exports，就等於 module.exports。

因為 `exports` 指向 `module.export`，所以要 expose 屬性就不必打 module.exports，簡化為 exports 就好：
```javascript
// a.js
exports.name = 'jcc';
// 上述其實等於 module.exports = 'jcc';
```
但是如果在 `a.js` 中改變 `exports` 的指向，就會喪失對  `module.export` 的指向。此時對 `exports` 下屬性就不會作用在 `module.exports`，這不會是你要的：

```javascript
// a.js
exports = {}; // exports 指向到一個空物件
exports.value = 'a value';

// b.js
var a = require('./a.js');
console.log(a.value); // undefined
```
















##### 參考
- http://blog.hellojcc.tw/2016/01/08/module-exports-vs-exports-in-node-js/
- http://dreamerslab.com/blog/tw/node-js-basics/
- https://stackoverflow.com/questions/7137397/module-exports-vs-exports-in-node-js/17944431#17944431
- https://ithelp.ithome.com.tw/articles/10185083