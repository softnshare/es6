# ES6 讀書會

解構賦值


###### tags: `解構賦值`

----

## 關於我

* James Yang (jawayang)
* ES6 讀書會召集人
* blog : [jamestw.logdown.com](jamestw.logdown.com)


###### tags: `解構`,`Destructuring`

----

## 解構賦值 / 分割代入

Destructuring assignment

基本用法

透過 `解構賦值` 來映射 `物件` 或 `陣列` 中的資料  


---

## 陣列的解構賦值  
Array destructuring

``` text
es5 寫法：
```
<!-- .element: class="fragment" data-fragment-index="1" -->
``` js
var a = 1;
var b = 2;
var c = 3;
```

<!-- .element: class="fragment" data-fragment-index="1" -->


``` text
es6 寫法：
```

<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
var [a, b, c] = [1, 2, 3];
```

<!-- .element: class="fragment" data-fragment-index="2" -->

``` text
上面代碼表示，可以從陣列中提取值，按照對應位置，對變數賦值。
```

<!-- .element: class="fragment" data-fragment-index="3" -->

----

``` text
簡單說就是一對一的關係
```

----

``` text
在陣列前可以使用 var 或 let 或 const 等指令..
```

``` js
var [a1,b1,c1] =[1,2,3];
```
<!-- .element: class="fragment" data-fragment-index="1" -->
``` js
let [a2,b2,c2] = [1,2,3];
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
const [d3,e3,f3] = [1,2,3];
```
<!-- .element: class="fragment" data-fragment-index="3" -->

----

## 格式匹配

``` text
只要等號兩邊的格式相同，左邊的變數就會被賦予對應的值
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
var [foo, [[bar], baz]] = [1, [[2], 3]];
foo //1
bar //2
baz //3
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` text
也可保留空位
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
var [, , third] = ["foo","bar","baz"];
third // "baz"
```
<!-- .element: class="fragment" data-fragment-index="2" -->

----

## 部分匹配

``` text
如果沒有對應的結構，變數的值就會是 undefined
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
var [foo] = [];
var [bar,foo] = [1];
//foo 都等於 undefined
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` text
不完全對應
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
let [x,y] = [1,2,3];
x //1
y //2

let [a,[b],c] = [1,[2,3],4];
a //1
b //2
c //4
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` text
雖然沒有完全解構，但是還是可以匹配部分的變數
```
<!-- .element: class="fragment" data-fragment-index="3" -->


----

## 不定量參數 "..."

不定參數 / 其餘參數 / 剩餘參數 / 殘餘引數 /  
Rest parameters

``` text
用法：不定量參數會回傳陣列
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
var [head, ...tail] = [1,2,3,4];
tail // [2,3,4]

let [x, y, ...z] = ['a'];
x // 'a'
y // undefined
z // []
```
<!-- .element: class="fragment" data-fragment-index="1" -->

----

## 非陣列指派

```
若等號的右邊不是同樣的陣列格式，就會出現錯誤
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
// 報錯
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
<!-- .element: class="fragment" data-fragment-index="2" -->

```
主要原因是因為不具備 Iterator 迭代介面 / 疊代
```
<!-- .element: class="fragment" data-fragment-index="3" -->

----

## 具備迭代介面的物件

``` text
若是使用 Set 物件，也可以用在陣列解構賦值
```

``` js
let [x,y,z] = new Set(["a","b","c"]);
x //"a"
y //"b"
z //"c"
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` text
可遍歷的物件,(指針物件 / 指標物件)
像陣列、物件都具備這樣的結構
而 ES6 又新增了 Map 與 Set
```
<!-- .element: class="fragment" data-fragment-index="2" -->

----

## 自建具備迭代介面的物件

``` js
function* fib(){
	var a = 0;
	var b = 1;
	while (true){
		yield a;
		[a,b] = [b, a + b];
	}
}

var [first,second,third,fourth] = fib();
first 	// 0
second 	// 1
third   // 2
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```
fibs 是一個 Generator 函式，具備 Iterator 介面
解構賦值會依次從這個介面取值
```
<!-- .element: class="fragment" data-fragment-index="2" -->


----

## 陣列解構賦值的運用

``` text
用來交換變數
```
``` js
var a = 1; b = 2;
[b,a] = [a,b];
a //2
b //1
```

----

## 陣列解構賦值的運用

``` text
與函式並用，可以取得函式回傳的陣列值
```
```
function f(){
	return [1,2,3];
}
var [a,,b] = f();
a //1
b //3
```

----

## 陣列解構賦值的運用

``` text
用正則表達式匹配提取值  
```
``` js
var url = "http://www.w3schools.com/svg/";

var parsedURL = /^(\w+)\:\/\/([^\/]+)\/(.*)$/.exec(url);
var [, protocol, fullhost, fullpath] = parsedURL;

console.log(protocol); // 輸出"https:"
```

----

## 陣列解構賦值的運用

``` text
函式的參數也可以使用解構賦值
```
``` js
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```
``` text
函式 add 的參數表面上是一個陣列，但在傳入參數的那一刻，
陣列參數就被解構成變量x和y
```
``` text
下面是另一個例子
```
``` js
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]

[[1,2],[3,4]].map(function([a,b]){
	return a+b;
})

```

----

## 指定預設值

```text
解構賦值允許指定預設值
當對應值為 undefined 的時候，就會使用預設值
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```javascript
var [a=0,b=0] = [1,];
a //1
b //0
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` text
預設值可以引用解構賦值的其他變數，但該變數必須已經宣告。
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
var [x = 1, y = x] = [];     // x=1; y=1
var [x = 1, y = x] = [2];    // x=2; y=2
var [x = 1, y = x] = [1, 2]; // x=1; y=2
var [x = y, y = 1] = [];     // ReferenceError
```
<!-- .element: class="fragment" data-fragment-index="2" -->


---

## 物件的解構賦值  
Object destructuring



``` text
解構不僅僅可以用在陣列，也可以用在物件
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
var { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` text
物件與陣列的差別，在於陣列是根據順序排列，
變數的內容根據位置決定，而物件並沒有順序，
變數必須與物件屬性同名，才能取得到內容
```
<!-- .element: class="fragment" data-fragment-index="2" -->

``` js
var { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```
<!-- .element: class="fragment" data-fragment-index="2" -->

----

## 名稱不一致
``` text
如果變量名與屬性名不一致，必須寫成下面這樣。
```
<!-- .element: class="fragment" data-fragment-index="1" -->

``` js
var { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
```
<!-- .element: class="fragment" data-fragment-index="2" -->

```text
再複雜一點
```
<!-- .element: class="fragment" data-fragment-index="3" -->

``` js
let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
<!-- .element: class="fragment" data-fragment-index="4" -->

----


## 實際的內部機制
``` js
var { foo, bar } = { foo: "aaa", bar: "bbb" };
```
<!-- .element: class="fragment" data-fragment-index="1" -->
```text
上方的程式碼同等於下方的程式碼
```
<!-- .element: class="fragment" data-fragment-index="1" -->
``` js
var { foo: foo, bar: bar } = { foo: "aaa", bar: "bbb" };
```
<!-- .element: class="fragment" data-fragment-index="1" -->
``` text
物件的解構賦值的內部機制，是先找到同名屬性，然後再賦給對應的變數。
真正被賦值的是後者，而不是前者
```
<!-- .element: class="fragment" data-fragment-index="2" -->
``` js
var { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```
<!-- .element: class="fragment" data-fragment-index="3" -->
``` text
上面代碼中，真正被賦值的是變數 baz，而不是模式foo。
```
<!-- .element: class="fragment" data-fragment-index="4" -->

----

## 注意事項

``` text
使用這種寫法，變數的宣告與賦值是一起的。但是對於 let 與 const 指令來說，
變數不能重新被宣告，所以一旦變數之前已經被宣告過了，
在結構賦值的時候，就會發生錯誤。
```

```js
let foo;
let {foo} = {foo: 1};
// SyntaxError: Duplicate declaration "foo"

let baz;
let {bar: baz} = {bar: 1};
// SyntaxError: Duplicate declaration "baz"
```

----

## 解決方案
``` text
在解構賦值時不要使用 let 指令
```
<!-- .element: class="fragment" data-fragment-index="1" -->
```js
let foo;
({foo} = {foo: 1}); // 成功

let baz;
({bar: baz} = {bar: 1}); // 成功
```
<!-- .element: class="fragment" data-fragment-index="1" -->
``` text
const只能賦值時並用，沒辦法分成兩段
```
<!-- .element: class="fragment" data-fragment-index="2" -->
```js
const a; //報錯
const {bar: a} = {bar: 1};  // 成功
```
<!-- .element: class="fragment" data-fragment-index="2" -->

----

## 解構嵌套/巢狀解構/Nested
``` text
和陣列一樣，解構也可以用於嵌套結構的物件
```
``` js
var obj = {
  p: [
    "Hello",
    { y: "World" }
  ]
};

var { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```

``` text
注意，這時p是樣式 patten ，不是變數，因此不會被賦值。
```

----

## 解構嵌套/巢狀解構/Nested

```js
var node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

var { loc: { start: { line }} } = node;
line // 1
loc  // error: loc is undefined
start // error: start is undefined
```
``` text
上面代碼中，只有line是變量，loc和start都是模式，不會被賦值。
```

----

## 解構嵌套/巢狀解構/Nested

```js
let obj = {};
let arr = [];

({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });

obj // {prop:123}
arr // [true]
```

----

## 設定預設值
```
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var { message: msg = "Something went wrong" } = {};
msg // "Something went wrong"
```
``` text
預設值生效的條件是，物件的屬性值嚴格等於 undefined
```
``` js
var {x = 3} = {x: undefined};
x // 3

var {x = 3} = {x: null};
x // null
```

----

## 解構失敗

``` text
如果解構失敗，變量的值等於 undefined。
```
``` js
var {foo} = {bar: 'baz'};
foo // undefined
```

``` text
如果解構模式是嵌套的物件，而且子物件所在的父屬性不存在，那麼將會報錯。
```
``` js
var {foo: {bar}} = {baz: 'baz'};
```
``` text
因為 foo 這時等於 undefined ， 再取子屬性就會報錯
```

----

## 奇怪的寫法

```  text
如果要將一個已經聲明的變數用於解構賦值，必須非常小心。
```
```
var x;
{x} = {x: 1};
```
``` text
把大括號放在聚首，會被誤以為是程式區塊，而發生錯誤，
避免這個問題要在外面加上圓括號
```
```js
var x;
({x} = {x: 1});
```

``` text
解構賦值允許，等號左邊的模式之中，不放置任何變數名
```
``` js
({} = [true, false]);
({} = 'abc');
({} = []);
```
``` js
上面的表達式雖然毫無意義，但是語法是合法的，可以執行。
```

----

## 物件解構賦值的應用

``` text
物件的解構賦值，可以很方便地將現有物件的方法，賦值到某個變數。
```
``` js
let { log, sin, cos } = Math;
```
``` text
上面代碼將Math對象的對數、正弦、餘弦三個方法，
賦值到對應的變量上，使用起來就會方便很多。
```

----

## 布林值與數值的解構

```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```

``` text
解構賦值的規則是，只要等號右邊的值不是對象，
就先將其轉為對象。由於undefined和null無法轉為對象，
所以對它們進行解構賦值，都會報錯。
```

----

## 字串的解構賦值的應用

``` text
字串與陣列都有一個length屬性，因此還可以對這個屬性解構賦值。
```
``` js
let {length : len} = 'hello';
len // 5
```

----

## 函式參數解構賦值的運用

``` text
函式參數的解構可以使用預設值。
```
``` js
function move({x = 0, y = 0} = {}) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0]
move({}); // [0, 0]
move(); // [0, 0]
```
``` text
上面程式中，函式 move 的參數是一個物件，通過對這個物件進行解構，
得到變數x和y的值。如果解構失敗，x和y等於預設值。
```
<pre class="james"> James 解讀：
若 move() 沒有傳參數進去，參數預設就會是一個空物件，
然後 x 預設就是 0 , y 預設為 0
</pre>

----

## 函式參數解構賦值的運用

``` text
寫法不同，結果不同
```

``` js
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]
```

```
undefined就會觸發函式參數的預設值。
```

----

## 圓括號的使用

``` text
解構賦值雖然很方便，但是解析起來並不容易。
對於編譯器來說，一個`程式碼`到底是`樣式`，
還是`表達式`，沒有辦法從一開始就知道。
```
``` text
ES6 的處理規則，只要是有可能導致解構的爭議，就不得使用。
建議盡量不要在樣式中放置圓括號
```

----
## 不能使用圓括號的狀況

1. 變數宣告
``` js
var [(a)] = [1];

var {x: (c)} = {};
var ({x: c}) = {};
var {(x: c)} = {};
var {(x): c} = {};}

var { o: ({ p: p }) } = { o: { p: 2 } };

```

2. 函式參數中不得使用

``` js
function f([(z)] reture z );
```

3. 解構賦值，不能將整個或某一層，放在圓括符中。
``` js
({ p: a }) = { p: 42 };
([a]) = [5];
[({ p: a }), { x: c }] = [{}, {}];
```

## 可以使用圓括號的狀況

1. 賦值程式的非樣式部分，可以使用圓括號。
``` js
[(b)] = [3]; // 正確
({ p: (d) } = {}); // 正確
[(parseInt.prop)] = [3]; // 正確
```
```
全是賦值，而非宣告，圓括號全都不是模式的部分。
```

----

## 解構的用途

1.交換變數


``` js
[x,y] = [y,x];
```

----

2.從函式返回多個值

``` text
函式只能返回一個值，如果要返回多個值，
只能將它們放在陣列或物件裡返回。
有了解構賦值，取出這些值就非常方便。
```
``` js
// 返回一個陣列
function example() {
  return [1, 2, 3];
}
var [a, b, c] = example();
```
``` js
// 返回一個物件
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
var { foo, bar } = example();

```

----

3.函式參數定義

``` text
解構賦值可以方便地將一組參數與變數名稱對應
```

``` js
// 參數是一組有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 參數是一組無次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

----

4.取出 JSON 數據

``` text
解構賦值對提取JSON對象中的數據，尤其有用。
```
``` js
var jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

----

5.設定參數的預設值
``` js
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```
```
指定參數的預設值，就避免了在函式內部寫
var foo = config.foo || 'default foo';
這樣的程式碼。
```

----

6.遍歷map結構
``` text
任何部署了Iterator介面的物件，
都可以用for...of循環遍歷。
Map結構原生支持Iterator介面，
配合變數的解構賦值，獲取鍵名和鍵值就非常方便。
```

```js
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}

// first is hello
// second is world

```

----

``` text
如果只想獲取鍵名，或者只想獲取鍵值，可以寫成下面這樣。
```

```js
// 獲取鍵名
for (let [key] of map) {
  // ...
}

// 獲取鍵值
for (let [,value] of map) {
  // ...
}
```

----

7.輸入模組的指定方法
``` text
加載模組時，往往需要指定輸入那些方法。解構賦值使得輸入語句非常清晰。
```
```js
const { SourceMapConsumer, SourceNode } = require("source-map");
```


---
