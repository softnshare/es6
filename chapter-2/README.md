# ES6 讀書會 

let 與 const

----

## 關於我

* James Yang (jawayang)
* ES6 讀書會召集人
* blog : [jamestw.logdown.com](jamestw.logdown.com)


----


## let 與 const

ES6 入門 第二章
作者：阮一峰
http://es6.ruanyifeng.com/#docs/let


----

let 指令
=====

基本用法

```
使用 `let` 陳述式來宣告變數，其範圍限於宣告所在的`區塊`
在 `javascript` 中 我們使用 {} 來代表`區塊`
```
<!-- .element: class="fragment" data-fragment-index="1" -->


----

## var 與 let 的差別

``` js
{
  let a = 10;
  var b = 1;
}

console.log(a) // ReferenceError: a is not defined.
console.log(b) // 1

```
``` text
區塊中，分別用 var 與 let 宣告了兩個不同的變數
在區塊外部，很明確地可以看出差異，使用`let`的變數，只能用在區塊內部。
```

----

## 使用時機

let 適合用在 for 迴圈
``` text
與 var 比較
```
``` js
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[1](); //10
a[6](); //10
```

``` text
使用 var 的時候，i 會不斷地被覆蓋，結果永遠等於 10
```

----

## 使用時機

在迴圈中使用 let

``` js
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[1](); // 1
a[6](); // 6

```
``` text
使用 let 的時候，i 不會被覆蓋，每次循環都會是一個新的宣告
```

----

## Hoisting 提升

宣告提升到其所在區域內頂端的行為
``` text
與 var 不同，不會有變數提升的現象。
```

``` js
console.log(foo); //顯示 undefined;
console.log(bar); //報錯 ReferenceError
var foo = 2;
let bar = 2;
```

----

## 暫時性死區

``` text
ES6 明確規定，區塊中如果存在 `let` 或 `const` 宣告、這個區塊對這些宣告
會產生封閉的作用區。
簡單說：在宣告 let 跟 const 變數之前，該變數都是不可用的。  
這在語法上，稱為「暫時性死區」（temporal dead zone，簡稱TDZ）。
```

<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
if (true) {
  // TDZ開始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ結束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` text
這樣的設計是為了讓大家養成良好的編程習慣，變量一定要在宣告之後使用
```
<!-- .element: class="fragment" data-fragment-index="3" -->

----

##


``` text
實際測試的時候，測試環境結果有所不同
chrome 50 版本會根據本書上的敘述，表示錯誤。
若用 `Babel` 轉譯則不會顯示錯誤
支援程度可以參考：http://kangax.github.io/compat-table/es6/
```

----

## 暫時性死區

``` text
這也表示使用`typeof`來檢查使用`let`宣告的變數，可能會出現錯誤  
```
``` text
範例：
```

```  js
typeof x;// 出現錯誤
let x;
```
```
因為在 let 宣告 x 之前，無法使用 x 這個變數
```



----

## 暫時性死區

較為不容易察覺的`死區`

``` js
function bar (x=y,y=2){
	return [x,y];
}
// 參數 x 預設為 y
// 參數 y 預設為 2
```
``` text
因為此時 y 尚未被宣告，所以會報錯
```

修正的方式
``` js
function bar (x=2,y=x){
	return [x,y];
}
// 先宣告 x 再宣告 y
```


----

## let 不允許重複
``` text
`let` 不允許在同一個區塊內宣告同一個變數
```

``` js
function(){
	let a = 10;
	let a = 9 // 發生錯誤
}
```
``` text
所以不能再函式內部重新宣告參數
```

``` js
function func(arg){
	let art; // 發生錯誤
}
function func(arg){
	{
		let arg; // 不發生錯誤
	}
}
```

----

## 為什麼需要區塊範圍變數 ?

``` text
在`ES5`中只有全域變數與函式變數，沒有區塊範圍變數。
因此造成了很多不合理的狀況。
```

### 狀況一
``` js
var tmp = new Data();

function f(){
	console.log(tmp); // undefined
	if(false){
		var tmp = "hello world";
	}
}
f();
```
``` text
在 `es5` 執行之後，tmp 在函數內輸出的結果會是 `undefined`，
由於 `tmp` 變數提升，導致函式內部的變數，覆蓋了外部的 `tmp`
```



----

## 狀況二


``` js
var s = 'hello';
for(var i=0 ; i< s.length; i++){
	console.log(s[i]);
}
console.log(i) //5
```
``` text
變數 i 只用來提供迴圈執行，執行結束後，並沒有消失，變成了全域變數。
```

----

## ES6 區塊作用域

let 提供了新的變數作用域

``` js
function f1() {
  let n = 5; //5
  if (true) {
    let n = 10; //10
  }
  console.log(n); // 5
}
```
```
若使用 var 最後輸出結果會變成 10
```

----

## let 限制變數作用區域


``` js
{{{
    {
      let a = 1;
        {let b = 1};
        console.log(b); //報錯
    }
  	console.log(a); //報錯
}}}
```

----

過去我們希望隔離不同的變數區域時，  
會需要用到立即執行匿名函式  
像這樣：

``` js
(function(){
  var a = 0;
})();
//確保 a 不會污染全域

console.log(a); // undefined

//在ES6中可以改成用區塊撰寫
{
 let b = 0;
}
console.log(b);

```

----

## 區塊中的函式

``` text
ES6 中規定 函數本身的作用域，在其所在的塊級作用域之內。
```
```  js
function f(){ console.log('我在外面');}
(function(){
	if(true){
		function f(){ console.log('我在裡面');}
	}
	f();
})();
```
```
若由 es5 的執行環境執行時，結果會是'我在裡面'  
若由 es6 執行環境執行，結果會是'我在外面'
```
``` test
在ES6中內部宣告的函數皆不會影響到作用域的外部
```

----

## 嚴格模式 use strict
``` text
在ES5嚴格模式規定，函式只能在頂層作用域或是函式內宣告，  
其他狀況（例如`if`指令、循環指令區)的宣告都會報錯。
```
```js
//ES5
`use strict`
if(true){
	function f(){ } //報錯
}


```


----

## ES6 嚴格模式
``` text
ES6由於引入了塊級作用域，這種情況可以理解成函數在塊級作用域內聲明
```

```js
// 不報錯
'use strict';
if (true) {
  function f() {}
}

// 報錯 不能缺少 { }
'use strict';
if (true)
  function f() {}

f();  // 無法執行因為在區塊外部
```


----



# Const 命令

基本用法

----

## 宣告常數

const 是一個唯讀的常數。   
一旦被宣告，常數的值就不能變。

``` text
嚴格模式
```
``` js
'use strict';
const PI = 3.1415; // 3.1415
PI = 3;  // TypeError: "PI" is read-only

```
``` text
非嚴格模式，不報錯，且PI的值不變。
```
```
const PI = 3.1415; // 3.1415
PI = 3;  // 3.1415
```

----

## 宣告常數
``` text
嚴格模式'use strict';
```
``` js
'use strict';
const foo;
// SyntaxError: missing = in const declaration

```
``` text
非嚴格模式
```
``` js
const foo;
foo = 1; // 重新賦值無效
foo // undefined
```

----

## const 作用域
``` text
作用域與 let 相同，同樣存在暫時性死區，只能在宣告後面的位置使用。
```
``` js
if(true){
	console.log(MAX); //錯誤參照
	const MAX = 5;
}
MAX // 未定義
```

----

## 宣告不可重複
``` text
與 let 相同，同一個變數名稱在同一區塊不得重複宣告。
```
```
var message = "Hello!";
let age = 25;

// 以下兩行都會報錯
const message = "Goodbye!";
const age = 30;
```

----

## const 宣告物件及陣列
``` text
對於複合資料類型之數據，像是 array 或 obj，  
宣告時指向的是數據所在的參考，無法保證變數內的數據不變。
所以當宣告一個物件為常數的時候要很小心。
```
``` js
const foo = {}; //foo 指向物件位置
foo.prop = 123; //物件內容可以被修改

foo.prop; //123
```

----

## 避免常數物件或陣列異動
``` text
為了避免宣告的物件或陣列被修改，
可以使用 Object.freeze();
方法凍結該物件或陣列。

```
``` js
const obj = Object.freeze({}); //宣告一個不能異動的常數物件
const arr = Object.freeze([]); //宣告一個不能異動的陣列物件
// 下面的指令沒有作用
// 嚴格模式下會顯示錯誤
obj.prop = 123;
arr[0] = 123;
console.log(obj.prop);  //無法執行
console.log(arr);		//無法執行
```
``` text
在 http://www.es6fiddle.net/ 中執行會出錯，必須拿掉修改才會正常執行
```

----

## 凍結屬性
``` text
除了物件本身應該要凍結之外，物件的屬性若指向物件時應該也要凍結，凍結的方法
```
``` js
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, value) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

----

## 變數宣告

ES5只有兩種聲明變量的方法：  
`var` 和 `function`

``` text
除了原本的 var 跟 function 之外
```
ES6有 `let` 跟 `const`    
還有 `import` 與 `class`
``` text
共六種變數宣告方式
```

----

## 全域物件
``` text
全域物件，是指最上層的物件，
在瀏覽器中指的是 `window` 物件
在`Node.js`指的是`global` 物件
```
``` js
//在 ES5 中 全域物件的屬性，就等於全域變數
window.a = 1;
console.log(a); // 1

a = 2;
console.log(window.a); //2;

b = 3; //沒有宣告
console.log(window.b); //3;
```

``` text
在 ES5 中沒有宣告的全域變數，自動變成為全域物件window的屬性，
不容易除錯，很容易就產生問題。
```

----

## 全域物件

從ES6開始，全域變數將逐步與全域物件的屬性脫鉤。

``` text
ES6為了改變這一點，但仍然保持兼容性，  
var 和 function 宣告的全域變數，依舊是全域物件的屬性；
另一方面規定，let命令、const命令、class命令聲明的全域變數，
不屬於全域物件的屬性。
```
``` js
//如果是在最外層宣告
var a = 1;
window.a; //1

let b = 1;
window.b // undefined
```

----

結論
====
<!-- .slide: data-transition="zoom" -->

let 與 const 是用來宣告區塊中的變數  
這些變數在區塊外部是無效的  
並且要注意宣告與使用的順序  

<!-- .element: class="fragment" data-fragment-index="1" -->
