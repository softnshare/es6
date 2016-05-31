# Expansion of RegExp in ES6



#### 資料來源：[ECMAScript 6 入門](http://es6.ruanyifeng.com/#docs/regex)
#### 資料整理：[cashbook](https://github.com/cashbooktw/)

---

## Outline
- RegExp Constructor
- RegExp Methods
- The Flag of "u" (Unicode)
- The Flag of "y" (Sticky)
- The "sticky" Property
- The "flags" Property
- RegExp.escape()
- Lookbehind

---

## RegExp Constructor

ES5，在 RegExp 建構子內使用正則表達式，無法使用第二個參數
```js
let regex = /xyz/i;
let regex = new RegExp('xyz', 'i');
let regex = new RegExp(/xyz/i);

let regex = new RegExp(/xyz/, 'i');
//error
```
ES6，可使用第二個參數，且 flag 會覆蓋
```js
let regex = new RegExp(/xyz/ig, 'i');
let foo = regex.flags;
//i
```

---

## RegExp Methods

`RegExp.prototype[Symbol.match]`於內部被 `String.prototype.match()`呼叫 <br>
其餘方法如`replace`、`search`、`split`同上
```js
'abc'.match(/a/);
/a/[Symbol.match]('abc');
//return same result
```

---

## The Flag of "u" (Unicode)

加上 `u` flag 正確處理 UTF-16 編碼
```js
/^\uD83D/.test('\uD83D\uDC2A');
// true
/^\uD83D/u.test('\uD83D\uDC2A');
// false
```
加上 `u` flag，讓以下正則表達式的內容可以正確處理
- The Character "."
- Unicode Representation
- Occurrences
- Predefined Character Classes
- The Flag of "i" (ignore case)

----

### The Character "."

加上 `u` flag，讓 "." 可以正確處理 UTF-16 編碼
```js
let s = '𠮷';

/^.$/.test(s); // false
/^.$/u.test(s); // true
```

----

### Unicode Representation

`\u{n}`用來表示Unicode <br>
加上 `u` flag，處理大括號內表示的 Unicode 字元
```js
/\u{61}/.test('a'); // false
/\u{61}/u.test('a'); // true
```

----

### Occurrences

加上 `u` flag，讓 `{n}` (出現次數)正確處理
```js
/a{2}/.test('aa'); // true
/a{2}/u.test('aa'); // true
/𠮷{2}/.test('𠮷𠮷'); // false
/𠮷{2}/u.test('𠮷𠮷'); // true
```
第三個式子等於
```js
/\uD842\uDFB7{2}/.test('\uD842\uDFB7\uD842\uDFB7');
```

----

### Predefined Character Classes

加上 `u` flag，讓預定義模式正確處理
```js
/^\S$/.test('𠮷') // false
/^\S$/u.test('𠮷') // true
```

----

### The Flag of "i" (ignore case)

有些Unicode字元編碼不同，但長得幾乎一樣，例如
>`\u004B` = &#75; <br>
>`\u212A` = &#8490;

加上 `u` flag，可識別非規範內的字元
```js
/[a-z]/i.test('\u212A') // false
/[a-z]/iu.test('\u212A') // true
```

---

## The Flag of "y" (Sticky)

針對目標字串，只會從`lastIndex`屬性指定的索引位置開始尋找
```js
let s = 'aaa_aa_a';
let r1 = /a+/g;
let r2 = /a+/y;

r1.exec(s) // ["aaa"]
r2.exec(s) // ["aaa"]

r1.exec(s) // ["aa"]
r2.exec(s) // null
```

----

在`split`方法中使用 flag `y`，若匹配成功，則回傳矩陣第一個值一定為空字串 <br>
目標值必須相鄰才會被匹配成功
```js
'#x#'.split(/#/y);// [ '', 'x#' ]
'##'.split(/#/y);// [ '', '', '' ]
```

----

使用replace方法的例子
```js
const REGEX = /a/gy;
'aaxa'.replace(REGEX, '-'); // '--xa'
```

----

使用`match`方法只能返回第一個匹配，必須與flag `g`並用才能回傳所有匹配
```js
'a1a2a3'.match(/a\d/y); // ["a1"]
'a1a2a3'.match(/a\d/gy); // ["a1", "a2", "a3"]
```

----

flag y的一個應用，是從字串中提取 token，確保每一個匹配。若有非pattern的字出現則停止匹配。
```js
const TOKEN_Y = /\s*(\+|[0-9]+)\s*/y;
const TOKEN_G  = /\s*(\+|[0-9]+)\s*/g;

tokenize(TOKEN_Y, '3x + 4');
// [ '3' ]
tokenize(TOKEN_G, '3x + 4');
// [ '3', '+', '4' ]

function tokenize(TOKEN_REGEX, str) {
  let result = [];
  let match;
  while (match = TOKEN_REGEX.exec(str)) {
    result.push(match[1]);
  }
  return result;
}
```

---

## The "sticky" Property

`sticky`屬性為布林，若正則表達式物件使用flag `y`則為`true`
```js
let r = (/hello\d/y).sticky;
// true
```

---

## The "flags" Property

`flags`屬性為字串，回傳正則表達式使用的 flags
```js
// ES5的source property
// return pattern of RegExp
let foo = /abc/ig.source;
// "abc"

// ES6的flags property
// return flags of RegExp
let bar = /abc/ig.flags;
// 'gi'
```

---

## RegExp.escape()

> 未成為ES6標準

處理需要被跳脫的特殊字元
```js
RegExp.escape("The Quick Brown Fox"); // "The Quick Brown Fox"
RegExp.escape("Buy it. use it. break it. fix it.");
// "Buy it\. use it\. break it\. fix it\."
RegExp.escape("(*.*)"); // "\(\*\.\*\)"
```

---

## Lookbehind

> 未成為ES6標準

lookahead
```js
/\d+(?=%)/.exec('100% of US presidents have been male');
// ["100"]
```
negative lookahead
```js
/\d+(?!%)/.exec('that’s all 44 of them'); // ["44"]
```

----

lookbehind
```js
/(?<=\$)\d+/.exec('Benjamin Franklin is on the $100 bill');
// ["100"]
```
negative lookbehind
```js
/\d+(?!%)/.exec('that’s all 44 of them'); // ["44"]
```

----

不符合預期的行為
>capturing group 內容不同
```js
/^(\d+)(\d+)$/.exec('1053');
// ["1053", "105", "3"]
/(?<=(\d+)(\d+))$/.exec('1053');
 // ["", "1", "053"]
```
capturing group 順序不同
```js
/(?<=(o)d\1)r/.exec('hodor')  // null
/(?<=\1d(o))r/.exec('hodor')  // ["r", "o"]
```

---

## Reference

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp) <br>
[ECMA-262 Ed.6](http://www.ecma-international.org/ecma-262/6.0/)

使用工具
>[es6fiddle](http://www.es6fiddle.net/) <br>
[jsbin](http://jsbin.com/?js,console) <br>
[Scratch JS](https://chrome.google.com/webstore/detail/scratch-js/alploljligeomonipppgaahpkenfnfkn) <br>
[regex101](https://regex101.com/#javascript) <br>
