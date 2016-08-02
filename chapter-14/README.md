ES6 讀書會
== 
![alt](http://ryanchristiani.com/wp-content/uploads/2015/06/js-logo.png)

## Iterator & for...of 循環

----

參考資料
- [ECMAScript 6 入門](http://es6.ruanyifeng.com/#docs/iterator)

- [MDN document](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

- [Exploring ES6](http://exploringjs.com/es6/ch_iteration.html)

---

# Iterator Concepts

----

## Usage

* 為各種數據結構，提供一個統一的、簡便的訪問接口

* 任何數據結構只要部署 Iterator 接口，就可依次處理該數據結構的所有成員

* ES6 創造了一種新的遍歷命令 for...of 循環


----

## Iterator 遍歷過程

``` javascript
var someString = "hi";

var iterator = someString[Symbol.iterator]();
iterator + "";    // "[object String Iterator]"
 
iterator.next();  // { value: "h", done: false }
iterator.next();  // { value: "i", done: false }
iterator.next();  // { value: undefined, done: true }
```
* `next` 方法返回一個對象，表示當前數據成員的信息
* `value` 屬性返回當前位置的成員
* `done` 屬性是一個布林值，表示遍歷是否結束

----

## Builtin Iterables

* 陣列
* 字串
* Sets
* Maps

:exclamation: 物件原生不具備遍歷器接口

----

- 不用任何處理，就可以被 `for...of` 循環遍歷

- 這些數據結構原生部署了 `Symbol.iterator` 屬性

---

# Iterator Protocal

----

## Iterable Protocol

* 部署在數據結構的 `Symbol.iterator` 屬性

:arrow_right: 具有 `Symbol.iterator` 屬性，就可以認為是 `iterable`

----

```javascript
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();

iter.next() // { value: 'a', done: false }
iter.next() // { value: 'b', done: false }
iter.next() // { value: 'c', done: false }
iter.next() // { value: undefined, done: true }
```

----

```javascript
var someArray = [1, 5, 7];
var someArrayEntries = someArray.entries();

someArrayEntries.toString(); // "[object Array Iterator]"
someArrayEntries === someArrayEntries[Symbol.iterator](); // true
typeof someArrayEntries[Symbol.iterator]; // "function"
```

---

# Constucts Supporting Iteration

----

1. for-of loop

```javascript
let iterable = [10, 20, 30];

for (let value of iterable) {
  console.log(value);
}

// 10
// 20
// 30
```

----

2. Spread operator (...)

```javascript
var someString = "hi";

[...someString] // ["h", "i"]
```

:+1: 可以將任何部署了Iterator接口的數據結構，轉為陣列

----

3. yield*

```javascript
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```

----

## Others

* Array.from():
  `const arr = Array.from(new Set(['a', 'b', 'c']));`

* Constructors of Maps and Sets:
  `const map = new Map([[false, 'no'], [true, 'yes']]);`
  `const set = new Set(['a', 'b', 'c']);`

* Promise.all(), Promise.race():
  `Promise.all(iterableOverPromises).then(···);`
  `Promise.race(iterableOverPromises).then(···);`

---

# Iterators and Generators

----

```javascript
var myIterable = {}
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable] // [1, 2, 3]
```

---

# return() and throw() Methods

----

## return()

* 使用場合: 如果for...of循環提前退出（通常是因為出錯，或者有break語句或continue語句）
* 如果一個對象在完成遍歷前，需要清理或釋放資源，就可以部署return方法

----

```javascript
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

var g = gen();

g.next()        // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next()        // { value: undefined, done: true }
```

----

## throw() 

* 主要是配合Generator函數使用，一般的遍歷器對象用不到這個方法

---

# for...of 循環

----

## 使用的範圍包括:

* Array
* Set
* Map
* DOM NodeList ......

----

```javascript
// DOM NodeList obj
let paras = document.querySelectorAll("p");

for (let p of paras) {
  p.classList.add("test");
}
```

----

## 與 for...in 的差異 - 1

```javascript
var arr = ['a', 'b', 'c', 'd'];

for (let a in arr) {
  console.log(a); // 0 1 2 3
}

// 只能獲得對象的鍵名，不能直接獲取鍵值

for (let a of arr) {
  console.log(a); // a b c d
}

// 允許遍歷獲得鍵值
```

----

## 與 for...in 的差異 - 2

```javascript
let arr = [3, 5, 7];
arr.foo = 'hello';

for (let i in arr) {
  console.log(i); // "0", "1", "2", "foo"
}

for (let i of arr) {
  console.log(i); //  "3", "5", "7"
}

// 數組的遍歷器接口只返回具有數字索引的屬性
```

----

## for...in 循環缺點:

* 陣列的鍵名是數字，但是for...in循環是以字符串作為鍵名“0”、“1”、“2”等等
* for...in循環不僅遍歷數字鍵名，還會遍歷手動添加的其他鍵，甚至包括原型鏈上的鍵
* 某些情況下，for...in循環會以任意順序遍歷鍵名

:exclamation: 主要是為遍歷物件而設計的，不適用於遍歷陣列

----

## for...of 與其他遍歷語法的比較之優點

* 有著同for...in一樣的簡潔語法，但是沒有for...in的缺點
* 不同用於forEach方法，它可以與break、continue和return配合使用
* 提供了遍歷所有數據結構的統一操作接口

----

## forEach()

```javascript
myArray.forEach(function (value) {
  console.log(value);
});
```

* 無法中途跳出forEach循環

---

Thanks :bow: 
