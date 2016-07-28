# Generator function

以下章節 **由淺入深** 帶大家探索 Generator function！  
:seedling: :herb: :deciduous_tree:

### 大綱
- [一. 淺入淺出 Generator function](#ㄧ-淺入淺出-generator-function)
  - [1. 它是一個狀態機](#1-它是一個狀態機)
  - [2. 它會回傳一個遍歷器](#2-它會回傳一個遍歷器)
  - [3. 使用 `next()` 遍歷狀態](#3-使用-next-遍歷狀態)
  - [4. 在 `next()` 中傳入參數，與 generator 互動](#4-在-next-中傳入參數與-generator-互動)
- [二. 中入淺出 Generator function](#二-中入淺出-generator-function)
  - [1. for…of](#1-forof)
  - [2. Generator.prototype.throw](#2-generatorprototypethrow)
  - [3. Generator.prototype.return](#3-generatorprototypereturn)
  - [4. yield*](#4-yield)
  - [5. 當物件屬性是 generator](#5-當物件屬性是-generator)
- [三. 深入淺出 Generator function](#三-深入淺出-generator-function)
  - [1. generator 的 this](#1-generator-的-this)
  - [2. generator 的應用](#2-generator-的應用)
- [四. 參考連結](#四-參考連結)

:seedling:
## ㄧ. 淺入淺出 Generator function

### 1. 它是一個狀態機

它封裝多個內部狀態，而 ***yield 是一個暫停標誌***。

```js
function* generator() {
  yield 'state1';
  yield 'state2';
}
```

### 2. 它會回傳一個遍歷器

```js
function* speak() {
  yield 'hello';
  yield 'world';
}

const iterator = speak();
```

### 3. 使用 `next()` 遍歷狀態

```js
function* speak() {
  yield 'hello';
  yield 'world';
}

const i = speak();

i.next(); // { value: 'hello',   done: false }
i.next(); // { value: 'world',   done: false }
i.next(); // { value: undefined, done: true  }
```

#### 如果 generator 有 `return` 值

```js
function* speak() {
  yield 'hello';
  yield 'world';
  return '!';
}

const i = speak();

i.next(); // { value: 'hello',   done: false }
i.next(); // { value: 'world',   done: false }
i.next(); // { value: '!',       done: true  } -> done 已經是 true，而 value 取得的是 return 的值
i.next(); // { value: undefined, done: true  }
```

#### `yield` 後面的表達式，只有被呼叫 `next()` 才會執行

```js
function getName() {
  console.log('My name is world.');
  return 'world';
}

function* speak() {
  yield 'hello ' + getName();
}

const i = speak();

i.next(); // My name is world.
          // { value: 'hello world', done: false }
```

#### 更多語法特性

```js
// 不一定要使用 yield
function* speak() {}

// 但是 yield 一定要在 generator function 中使用
function speak() {
  yield 'hello world'; // Error!
}

// 在表達式中必須加括弧
console.log('hello' + (yield 'world'));

// 在參數中不用
getName(yield 'Who are you?');
```

### 4. 在 `next()` 中傳入參數，與 generator 互動

```js
function* conversation() {
  const name = yield 'What\'s your name?';  // name 會取得第二個 next 回傳的參數
  yield `Ok, your name is ${name}, right?`;
}

const i = conversation();
i.next().value;        // What's your name?
i.next('Jason').value; // Ok, your name is Jason, right?
```

#### 第一個 `next()` 是不能給參數的

```js
const i = conversation();
i.next().value;
i.next('Jason').value;
```

#### 小試題：下方例子會分別印出什麼呢？

```js
function* foo(x) {
  const y = 2 * (yield (x + 1));
  const z = yield (y / 3);
  return (x + y + z);
}

const i = foo(5);
console.log(i.next().value);
console.log(i.next(12).value);
console.log(i.next(13).value);
```

```js
function* foo(x) {
  const y = 2 * (yield (x + 1));
  const z = yield (y / 3);
  return (x + y + z);
}

const i = foo(5);              // x=5
console.log(i.next().value);   // yield x+1 -> 6
console.log(i.next(12).value); // y=2*12 -> yield 24/3 -> 8
console.log(i.next(13).value); // z=13 -> 5+24+13 -> 42
```

### 5. 淺入淺出小結

1. 它是一個狀態機
2. 它會回傳一個遍歷器
3. 使用 `next()` 遍歷狀態
4. 在 `next()` 中傳入參數，與 generator 互動

:herb:
## 二. 中入淺出 Generator function

### 1. for...of

```js
function *foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  yield 5;
  return 6;
}

for (let v of foo()) {
  console.log(v);
}
// 1 2 3 4 5
// 注意：沒有 `return` 的值
```

#### 一個 fibonacci 例子

```js
function* fibonacci() {
  let [prev, curr] = [0, 1];
  while (true) {
    [prev, curr] = [curr, prev + curr];
    yield curr;
  }
}

for (let n of fibonacci()) {
  if (n > 1000) break;
  console.log(n);
}
```

#### 使用 `Array.form()`、擴展和解構運算式

```js
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}

Array.from(numbers())   // [1, 2]

[...numbers()]          // [1, 2]

let [x, y] = numbers(); // x=1, y=2

for (let n of numbers()) {
  console.log(n);
}
// 1 2
```

### 2. Generator.prototype.throw

```js
function* g() {
  try {
    yield;
  } catch (e) {
    console.log('内部捕獲', e);
  }
}

var i = g();
i.next();

try {
  i.throw('a');
  i.throw('b');
} catch (e) {
  console.log('外部捕獲', e);
}

// 內部捕獲, a
// 外部補獲, b
```

#### `throw` 被捕獲以後，會附帶執行下一條 `yield` 語句

```js
function* g() {
  try {
    yield console.log('a');
  } catch (e) {
    // ...
  }
  yield console.log('b');
  yield console.log('c');
}

var i = g();
i.next();  // a
i.throw(); // b
i.next();  // c
```

### 3. Generator.prototype.return

```js
function* g() {
  yield 1;
  yield 2;
  yield 3;
}

var i = g();

i.next();        // { value: 1, done: false }
i.return('foo'); // { value: 'foo', done: true }
i.next();        // { value: undefined, done: true }
```

#### 如果使用 `finally`

```js
function* numbers() {
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
var i = numbers()
i.next();     // { done: false, value: 1 }
i.next();     // { done: false, value: 2 }
i.return(7);  // { done: false, value: 4 }
i.next();     // { done: false, value: 5 }
i.next();     // { done: true,  value: 7 }
```

### 4. yield*

```js
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

for (let v of bar()) {
  console.log(v);
}
// x a b y
```

#### 陣列是 iterator

```js
function* g() {
  yield* ['a', 'b', 'c'];
}

g().next() // { value: 'a', done: false }
```

#### 字串也是 iterator

```js
let read = (function* () {
  yield 'hello';
  yield* 'hello';
})();

read.next().value // 'hello'
read.next().value // 'h'
```

#### 接收 `yield*` 的 `return`

```
function* foo() {
  yield 2;
  yield 3;
  return 'foo';
}

function* bar() {
  yield 1;
  var v = yield* foo();
  console.log('v: ' + v);
  yield 4;
}

var i = bar();

i.next(); // { value: 1, done: false }
i.next(); // { value: 2, done: false }
i.next(); // { value: 3, done: false }
i.next(); // 'v: foo'
	      // { value: 4, done: false }
i.next(); // { value: undefined, done: true }
```

### 5. 當物件屬性是 generator

```js
const obj = {
  * myGeneratorMethod() {
    ···
  }
};
```

### 6. 中入淺出小結

我們介紹了以下語法：

1. for…of
2. Generator.prototype.throw
3. Generator.prototype.return
4. yield*
5. 當物件屬性是 generator

:deciduous_tree:
## 三. 深入淺出 Generator function

### 1. generator 的 this

#### gernerator 回傳的是 gernerator 實例

```js
function* g() {}

g.prototype.hello = function () {
  return 'hi!';
};

const obj = g();

obj instanceof g; // true
obj.hello();      // 'hi!'
```

#### generator 返回的是遍歷器，不是 this

```js
function* g() {
  this.a = 11;
}

let obj = g();
obj.a; // undefined
```

#### 不能使用 new

```js
function* F() {
  yield this.x = 2;
  yield this.y = 3;
}

new F()
// TypeError: F is not a constructor
```

### 2. generator 的應用

1. 以同步化的方式撰寫異步操作
2. 控制流管理
3. 部署 iterator 接口
4. 作為數據結構

:mortar_board:

## 四. 參考連結

1. [ECMAScript 6 入门 | 阮一峰](http://es6.ruanyifeng.com/#docs/generator)
2. [Egghead](https://egghead.io/lessons/ecmascript-6-generators?course=learn-es6-ecmascript-2015)
3. [淺入淺出 Generator Function](http://abalone0204.github.io/2016/05/08/es6-generator-func/)
4. [Promise, generator, async 與 ES6](http://huli.logdown.com/posts/292655-javascript-promise-generator-async-es6)
5. [Airbnb style guide](https://github.com/airbnb/javascript#iterators-and-generators)
