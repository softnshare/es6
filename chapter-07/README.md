## ES6 讀書會 - 關於「陣列」的擴充




### 資料來源
* [ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/array) （阮一峰）
* [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)




### 關於我
```
{
  name: "Allen Hsieh",
  roles: [
	"Front-end Engineer",
	"Java Programmer"
  ],
  worksAt: "達暉資訊",
  email: "allen@lustertech.net"
}
```




## 大綱
* Array.from()
* Array.of()
* copyWithin()
* find() 和 findIndex()
* fill()
* entries()，keys() 和 values()
* includes()
* 空位






## Array.from()
### 將物件轉換成陣列




### 語法
```javascript
Array.from(arrayLike[, mapFn[, thisArg]])

用途：
  將「物件」轉換為「陣列」

參數：
  arrayLike - 物件，ArrayLike 或 Iterable
  mapFn  - map function
  thisArg - map function 執行時的 this 物件
```




### Array-like 轉換範例

```javascript
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};

// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```




### Array-like 轉換範例（續）
```javascript
// NodeList对象
let ps = document.querySelectorAll('p');
Array.from(ps).forEach(function (p) {
  console.log(p);
});

// arguments对象
function foo() {
  var args = Array.from(arguments);
  // ...
}
```




### Array-like 必要條件
```javascript
// Array-like 必要條件，必須有 length 屬性

Array.from({ length: 3 });
// [ undefined, undefined, undefinded ]

Array.from({ 0: 'a', 1: 'b' })
// []
```




### Iterable 轉換範例
```javascript
Array.from('hello')
// ['h', 'e', 'l', 'l', 'o']

let namesSet = new Set(['a', 'b'])
Array.from(namesSet) // ['a', 'b']
```




### 擴展運算符號
```javascript
// 擴展運算符號，也可以將物件轉換成數據

// arguments对象
function foo() {
  var args = [...arguments];
}

// NodeList对象
[...document.querySelectorAll('div')]

// 要注意「擴展運算符號」是透過 Symbol.iterator 來進行轉換
// 所以無法轉換 { length: 3 } 這樣的 array-like 物件
// 這時必須透過 Array.from() 來處理
```




### 加上 map funciton 的範例
```javascript
Array.from(arrayLike).map(x => x * x);

// 等同於
Array.from(arrayLike, x => x * x);

Array.from([1, 2, 3], x => x * x);
// [1, 4, 9]


let spans = document.querySelectorAll('span.name');
let names1 = Array.prototype.map.call(spans, s => s.textContent);

// 等同於
let names2 = Array.from(spans, s => s.textContent)
```




### 更多範例
```javascript
Array.from([1, , 2, , 3], (n) => n || 0)
// [1, 0, 2, 0, 3]


function typesOf () {
  return Array.from(arguments, value => typeof value)
}
typesOf(null, [], NaN)
// ['object', 'object', 'number']
```




### 另一個用途，計算字串長度
```javascript
// 因為 from 函數可以正確處理各種 Unicode 字，
// 因此可避免 JavaScript 將大於 \uFFFF 的 Unicode 字，
// 算成兩個字的 bug.

function countSymbols(string) {
  return Array.from(string).length;
}
```






## Array.of()
### 建立陣列，彌補建構子的不足




### 傳統建構子的問題
```javascript
// Array() 遞入沒有、一個、三個參數，所代表意思為皆不同。

Array()          // []

Array(3)         // [, , ,]

Array(3, 11, 8)  // [3, 11, 8]
```




### of 函式的好處
```javascript=
// Array.of() 統一參數所代表的意思

Array.of()           // []

Array.of(undefined)  // [undefined]

Array.of(1)          // [1]

Array.of(1, 2)       // [1, 2]
```






## copyWithin()
### 複製陣列元素




### copyWithin 語法
```javascript
arr.copyWithin(target[, start[, end]])
 
參數：
  target - 替換的起始點
  start  - 來源的起始點
  end    - 來源的結束點
  3 個參數都是數字，array index
  從 0 開始，負數代表從後面開始算

```




### 範例
```javascript
[1, 2, 3, 4, 5].copyWithin(0, 3)
// [4, 5, 3, 4, 5]

// 将3号位复制到0号位
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
// [4, 2, 3, 4, 5]

// -2相当于3号位，-1相当于4号位
[1, 2, 3, 4, 5].copyWithin(0, -2, -1)
// [4, 2, 3, 4, 5]
```




### 範例（續.）
```javascript
// 将3号位复制到0号位
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
// {0: 1, 3: 1, length: 5}

// 将2号位到数组结束，复制到0号位
var i32a = new Int32Array([1, 2, 3, 4, 5]);
i32a.copyWithin(0, 2);
// Int32Array [3, 4, 5, 4, 5]

// 对于没有部署TypedArray的copyWithin方法的平台
// 需要采用下面的写法
[].copyWithin.call(new Int32Array([1, 2, 3, 4, 5]), 0, 3, 4);
// Int32Array [4, 2, 3, 4, 5]
```






## find() 和 findIndex()
### 尋找陣列元素




### find 語法
```javascript
arr.find(callback[, thisArg])
 
參數：
  callback - 判斷函式，可以接到三個參數
    * element - 目前元素
    * index   - 目前 array index
    * array   - 目前 array
    
  thisArg  - 給 callback 函式用的 this

回傳：
  回傳第一個符合條件的元素，反之則回傳 undefined
```




### find 範例
```javascript=
[1, 4, -5, 10].find((n) => n < 0)
// -5

[1, 5, 10, 15].find(function(value, index, arr) {
  return value > 9;
})
// 10

// 上面的例子也可以寫成
[1, 5, 10, 15].find(value => value > 9);
```




### findIndex 語法
```javascript
arr.findIndex(callback[, thisArg])
 
參數：
  callback - 判斷函式，可以接到三個參數
    * element - 目前元素
    * index   - 目前 array index
    * array   - 目前 array
    
  thisArg  - 給 callback 函式用的 this

回傳：
  回傳第一個符合條件的位置，反之則回傳 -1
```




### findIndex 範例
```javascript
[1, 5, 10, 15].findIndex(function(value, index, arr) {
  return value > 9;
}) // 2

[NaN].indexOf(NaN)
// -1

[NaN].findIndex(y => Object.is(NaN, y))
// 0
```






## fill()
### 填入陣列元素





### 語法
```javascript
arr.fill(value[, start = 0[, end = this.length]])
 
參數：
  value - 填入的值
  start - 起始點，array index，起始為 0
  end   - 結束點，array index，不包含該點
```




### 範例
```javascript
['a', 'b', 'c'].fill(7)
// [7, 7, 7]

new Array(3).fill(7)
// [7, 7, 7]

['a', 'b', 'c'].fill(7, 1, 2)
// ['a', 7, 'c']
```






## entries()、Keys() 和 values()
### 取得陣列 key, value or both




### 範例
```javascript
for (let index of ['a', 'b'].keys()) {
  console.log(index);
}
// 0
// 1

for (let elem of ['a', 'b'].values()) {
  console.log(elem);
}
// "a"
// "b"

for (let [index, elem] of ['a', 'b'].entries()) {
  console.log(index, elem);
}
// 0 "a"
// 1 "b"
```




### 手動用 next 取資料
```javascript
// 如不想使用 for...of 迴圈，也可改用 next 方法

let letter = ['a', 'b', 'c'];
let entries = letter.entries();

console.log(entries.next().value); // [0, "a"]
console.log(entries.next().value); // [1, "b"]
console.log(entries.next().value); // [2, "c"]
```






## includes()
### 判斷陣列是否包含該元素




### 語法
```javascript
var boolean = array.includes(searchElement[, fromIndex])
 
參數：
  searchElement - 搜尋物
  fromIndex     - 搜尋起點，array index，起始為 0
  
回傳：
  boolean，找到回傳 true，反之 false
```




### 範例
```javascript
[1, 2, 3].includes(2);      // true

[1, 2, 3].includes(4);      // false

[1, 2, 3].includes(3, 3);  // false

[1, 2, 3].includes(3, -1); // true

[1, 2, NaN].includes(NaN);  // true
```




### 以前判斷的方式
```javascript
if (arr.indexOf(el) !== -1) {
  // ...
}

// 缺點：
//   1. 不直覺
//   2. NaN 會出現誤判
[NaN].indexOf(NaN)   // -1

// includes 沒有 NaN 的問題
[NaN].includes(NaN)  // true
```




### 支援
* [屬於 ES7 規範](https://tc39.github.io/ecma262/#sec-array.prototype.includes)
* [Babel 已經支援](http://kangax.github.io/compat-table/esnext/)




### 補充
```javascript
Map 和 Set 有一個 has 方法，需要注意與 includes 區分。

Map 的 has 方法，是用来找 key 的：
* Map.prototype.has(key)
* WeakMap.prototype.has(key)
* Reflect.has(target, propertyKey)

Set 的 has 方法，是用来找 value 的:
* Set.prototype.has(value)
* WeakSet.prototype.has(value)
```






## 空位




### 先說結論
> 由於處理規則不統一，陣列 Array 應盡量「避免出現空位」。





### 空位代表該位置沒有任何值
```javascript
// 例如宣告一個長度 3 的陣列
Array(3)

// 因為沒有初始值，所以裡面都是「空位」
// [, , ,]
```




### 注意！ undefined 是有值的

```javascript
// 透過 in 運算子來判斷陣列0號位子有沒有值

// 回傳 true
0 in [undefined, undefined, undefined]

// 回傳 false
0 in [, , ,]
```





### ES5 對空位處理的方式不一
```javascript
// 大多數都是跳過空位

// forEach - 跳過空位
[,'a'].forEach((x,i) => console.log(i));  // 1

// filter - 跳過空位
['a',,'b'].filter(x => true)  // ['a', 'b']

// every - 跳過空位
[,'a'].every(x => x==='a') // true

// some - 跳過空位
[,'a'].some(x => x !== 'a') // false
```




### ES5 對空位處理的方式不一（續.）
```javascript
// map - 跳過空位，回傳值 undefined
[,'a'].map(x => 1)  // [undefined, 1]

// join - 將空位視為 undefined，並被處理成空字串
[,'a',undefined,null].join('#')  // "#a##"

// toString - 將空位視為 undefined，並被處理成空字串
[,'a',undefined,null].toString()  // ",a,,"
```




### ES6 把空位當 undefined 處理
```javascript
Array.from(['a',,'b'])  // [ "a", undefined, "b" ]

[...['a',,'b']]  // [ "a", undefined, "b" ]

[,'a','b',,].copyWithin(2,0)  // [undefined, "a", undefined, "a"]

new Array(3).fill('a')  // ["a","a","a"]

let arr = [, ,];
for (let i of arr) {
  console.log(1);
}
// 輸出兩次 1
```




### 以下函式也將空位處理成 undefined
```javascript
// entries()
[...[,'a'].entries()]  // [[0, undefined], [1, "a"]]

// keys()
[...[,'a'].keys()]  // [0, 1]

// values()
[...[,'a'].values()]  // [undefined, "a"]

// find()
[,'a'].find(x => true)  // undefined

// findIndex()
[,'a'].findIndex(x => true) // 0
```
