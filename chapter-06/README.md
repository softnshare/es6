# ES6讀書會 
- [數值的擴展-作者：阮一峰](http://es6.ruanyifeng.com/#docs/number)
- 6/21(二) RayWay

---

## 二進制和八進制表示法

----

### 二進制和八進制表示法 (ES6) 
- 二進制(Binary)
	- `OB` or `ob`

```js
0b111110111 === 503 // true
```

- 八進制(Octal)
	- `0O` or `0o`
	
```js
0o767 === 503 // true
```
	
- 轉成十進制

```js
Number('0b111')  // 7
Number('0o10')  // 8
```

----

### 二進制和八進制表示法 (ES5) 
- 0開始表示八進位
```js
(function(){
  console.log(0o11 === 011);
})() // true
```
- strict mode禁用
```js
(function(){
  'use strict';
  console.log(0o11 === 011);
})() 
// Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```

---

## Number.isFinite(), Number.isNaN()

----

### ES6

----

#### Number.isFinite()
- Infinity, -Infinity

```js
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
Number.isFinite(1/0); // false
Number.isFinite(-1/0); // false
```

----

#### Number.isNaN()

```js
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true'/0) // true
Number.isNaN('true'/'true') // true
NaN === NaN // false
```

----

### ES5

----

#### Number.isFinite()

```js
(function (global) {
  var global_isFinite = global.isFinite;

  Object.defineProperty(Number, 'isFinite', {
    value: function isFinite(value) {
      return typeof value === 'number' && global_isFinite(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);
```

----

#### Number.isNaN()

```js
(function (global) {
  var global_isNaN = global.isNaN;

  Object.defineProperty(Number, 'isNaN', {
    value: function isNaN(value) {
      return typeof value === 'number' && global_isNaN(value);
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);
```

----

#### 比較

```js
isFinite(25) // true
isFinite("25") // true
Number.isFinite(25) // true
Number.isFinite("25") // false

isNaN(NaN) // true
isNaN("NaN") // true
Number.isNaN(NaN) // true
Number.isNaN("NaN") // false
```

---

## Number.parseInt(), Number.parseFloat()

----

- Global -> Number

```js
// ES5
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```

- 行為不變
 
```js
Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```

---

## Number.isInteger()

----

- integer和float在js中是一樣的貯存方式

```js
Number.isInteger(25) // true
Number.isInteger(25.0) // true
Number.isInteger(25.1) // false
Number.isInteger("15") // false
Number.isInteger(true) // false
```

----

### ES5

```js

(function (global) {
  var floor = Math.floor,
    isFinite = global.isFinite;

  Object.defineProperty(Number, 'isInteger', {
    value: function isInteger(value) {
      return typeof value === 'number' && isFinite(value) &&
        value > -9007199254740992 && value < 9007199254740992 &&
        floor(value) === value;
    },
    configurable: true,
    enumerable: false,
    writable: true
  });
})(this);
```

---

## Number.EPSILON

----

### IEEE 754 造成的誤差
- JS沒有所謂的Integer

```js
0.1 + 0.2
// 0.30000000000000004

0.1 + 0.2 - 0.3
// 5.551115123125783e-17

5.551115123125783e-17.toFixed(20)
// '0.00000000000000005551'
```

----

### Number.EPSILON
- ES6引進之極小常量
 
```js
Number.EPSILON
// 2.220446049250313e-16
Number.EPSILON.toFixed(20)
// '0.00000000000000022204'
```

----

### 可接受之誤差

```js
function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON;
}
withinErrorMargin(0.1 + 0.2, 0.3)
// true
withinErrorMargin(0.2 + 0.2, 0.3)
// false
```

---

## 安全整數和Number.isSafeInteger()

----

### JS的數值範圍
- -2^53^~2^53^ (不包含)

```js
Math.pow(2, 53) // 9007199254740992

9007199254740992  // 9007199254740992
9007199254740993  // 9007199254740992

Math.pow(2, 53) === Math.pow(2, 53) + 1
// true
```

----

### 安全整數
- Number.MAX_SAFE_INTEGER
- Number.MIN_SAFE_INTEGER

```js
Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
// true
Number.MAX_SAFE_INTEGER === 9007199254740991
// true

Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
// true
Number.MIN_SAFE_INTEGER === -9007199254740991
// true
```

----

- Number.isSafeInteger()
	- 若拿來計算時，參與運算的每個數都要檢查！
	
```js
Number.isSafeInteger('a') // false
Number.isSafeInteger(null) // false
Number.isSafeInteger(NaN) // false
Number.isSafeInteger(Infinity) // false
Number.isSafeInteger(-Infinity) // false

Number.isSafeInteger(3) // true
Number.isSafeInteger(1.2) // false
Number.isSafeInteger(9007199254740990) // true
Number.isSafeInteger(9007199254740992) // false

Number.isSafeInteger(Number.MIN_SAFE_INTEGER - 1) // false
Number.isSafeInteger(Number.MIN_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER) // true
Number.isSafeInteger(Number.MAX_SAFE_INTEGER + 1) // false
```



---

## Math物件的擴展

----

### 一般

----

#### Math.trunc()
- 去除小數

```js
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```

- 非數值先型態轉換

```js
Math.trunc('123.456') // 123
```

- 無法處理就回傳NaN

```js
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN

```

----

#### Math.sign()
- 正數, +1
- 負數, -1
- 零
	- +0
	- -0
- NaN

```js

Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN
Math.sign('foo'); // NaN
Math.sign();      // NaN

```

----

#### Math.cbrt()

- 立方根

```js
Math.cbrt(-1) // -1
Math.cbrt(0)  // 0
Math.cbrt(1)  // 1
Math.cbrt(2)  // 1.2599210498948734
```

----

#### Math.clz32() 
- 回傳整數以32bit二進位顯示之前導`0`個數
- 浮點數只取整數部分，非數值會先轉為數值。

```js
Math.clz32(0) // 32
Math.clz32(1) // 31
Math.clz32(1000) // 22
Math.clz32(0b01000000000000000000000000000000) // 1
Math.clz32(0b00100000000000000000000000000000) // 2
Math.clz32(1 << 1) // 30
Math.clz32(1 << 2) // 29
Math.clz32(1 << 29) // 2
Math.clz32(3.2) // 30
Math.clz32() // 32
Math.clz32(NaN) // 32
Math.clz32(Infinity) // 32
Math.clz32(null) // 32
Math.clz32('foo') // 32
Math.clz32([]) // 32
Math.clz32({}) // 32
Math.clz32(true) // 31
```

----

#### Math.imul() 
- 32-bit 整數相乘

```js
Math.imul(2, 4)   // 8
Math.imul(-1, 8)  // -8
Math.imul(-2, -2) // 4
```

- 可以返回正確低位數值

```js
(0x7fffffff * 0x7fffffff) | 0 // 0
Math.imul(0x7fffffff, 0x7fffffff) // 1
```

----

#### Math.fround() 
- 返回單精度浮點數

```js
Math.fround(0)     // 0
Math.fround(1)     // 1
Math.fround(1.337) // 1.3370000123977661
Math.fround(1.5)   // 1.5
Math.fround(NaN)   // NaN
```

----

#### Math.hypot()
- 返回所有參數的平方和之平方根

```js
Math.hypot(3, 4);        // 5
Math.hypot(3, 4, 5);     // 7.0710678118654755
Math.hypot();            // 0
Math.hypot(NaN);         // NaN
Math.hypot(3, 4, 'foo'); // NaN
Math.hypot(3, 4, '5');   // 7.0710678118654755
Math.hypot(-3);          // 3
```

----

### 對數

----

#### Math.expm1()
- Math.expm1(x)返回e^x^ - 1，即Math.exp(x) - 1。

```js
Math.expm1(-1) // -0.6321205588285577
Math.expm1(0)  // 0
Math.expm1(1)  // 1.718281828459045
```

----

#### Math.log1p()
- Math.log1p(x)方法返回1 + x的自然對數
	- Math.log(1 + x)
- x < -1
	- NaN

```js
Math.log1p(1)  // 0.6931471805599453
Math.log1p(0)  // 0
Math.log1p(-1) // -Infinity
Math.log1p(-2) // NaN
```

----

#### Math.log10()
- 以10為底

#### Math.log2()
- 以2為底

```js
Math.log10(2)      // 0.3010299956639812
Math.log10(1)      // 0
Math.log10(0)      // -Infinity
Math.log10(-2)     // NaN
Math.log10(100000) // 5
//---
Math.log2(3)       // 1.584962500721156
Math.log2(2)       // 1
Math.log2(1)       // 0
Math.log2(0)       // -Infinity
Math.log2(-2)      // NaN
Math.log2(1024)    // 10
Math.log2(1 << 29) // 29
```

----

### 三角函数

```js
Math.sinh(x) 
Math.cosh(x) 
Math.tanh(x) 
Math.asinh(x) 
Math.acosh(x) 
Math.atanh(x) 
```

---

## 指數運算符
- ES7 (Babel已支援)
	- ** 運算子
	- 可以與等號連用

```js
2 ** 2 // 4
2 ** 3 // 8
//---
let a = 2;
a **= 2; // a = a * a;

let b = 3;
b **= 3; // b = b * b * b;
```

