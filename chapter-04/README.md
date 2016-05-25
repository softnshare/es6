# ES6 讀書會 - ECMAScript 6 入門

### Chapter 4 - String

_Prepared By [Peng Jie](https://github.com/neighborhood999)_

## Table Of Content

- [使用 unicode 表示字串](#使用-unicode-表示字串)
    - [在 JavaScript 中的 String](#在-javascript-中的-string)
    - [codePointAt 方法](#codepointat-方法)
    - [String.fromCodePoint 方法](#stringfromcodepoint-方法)
- [String 的 iterator](#string-的-iterator)
- [at 方法](#at-方法)
- [normalize 方法](#normalize-方法)
- [字串確認](#字串確認)
    - [includes 方法](#includes-方法)
    - [startsWith 方法](#startswith-方法)
    - [endsWith 方法](#endswith-方法)
- [repeat 方法](#repeat-方法)
- [字串補全](#字串補全)
    - [padStart 方法](#padstart-方法)
    - [padEnd 方法](#padend-方法)
- [Template Strings](#template-strings)
- [raw 方法](#raw-方法)

## 使用 unicode 表示字串

**TL;DR**

- Unicode 解決了不同文字編碼的問題。
- 每個文字和符號都對應到一組 Code Point。
- 十六進制格式：`\u0000` - `\uFFFF`。

_延伸閱讀：_ [Wiki - Unicode](https://zh.wikipedia.org/wiki/Unicode)、[Unicode字元平面對映](http://bit.ly/1TIEHvG)

### 在 JavaScript 中的 String

- UCS-2 encode。
- Support **UTF-16** encode。
- Every string is **2 Byte**。

在 JavaScript 中，我們可以透過這樣的方式來表示字串：

```js
const a = '\x61'; //a
const b = '\u0061'; // a

'\x61' === '\u0061'; // true
```

由**十六進制格式的兩個位元**組成，代表一個編碼位置。  
超出 `\u0000` - `\uFFFF` 範圍必須要用**兩個** Code Ponit 來表示。

在 ES6 中，改善了這個問題，將超出範圍的 Code Point 放入 **{ }** 就可以了。

```js
// ES5

const a = '\uD842\uDFB7'; // '𠮷' (4 Bytes)
const b = '\u20BB7'; // ₻7 (\u20BB+7)

// ES6

const c = '\u{20BB7}'; // '𠮷'
```

### codePointAt 方法

簡單來說，透過 `codePointAt()` 可以正確處理 **4 Byte** 的字元。

```js
const emoji = '\uD83D\uDE02'; // 😂

const tmp = emoji.codePointAt(0); // 128514 (十進制)
tmp.toString(16); //1f602 (十六進制)

emoji.codePointAt(1); // 56834 (十進制)

```

`codePointAt()` 方法回傳一個非負整數，它是 Unicode Code Point 的值。

### String.fromCodePoint 方法

_ES5 和 ES6 方法比較：_

> ES5：`String.fromCharCode()` < _（0xFFFF）_

> ES6：`String.fromCodePoint()` > _（0xFFFF）_

`String.fromCharCode()` 會省略超過 `0xFFFF` 最高位元：

```js
String.fromCharCode(0x1f602); //  => Actual：0xf602
String.fromCodePoint(0x1f602); // 😂 => success
```

## String 的 iterator

ES6 的 `for..of` 在可以 **Iterable** 的物件上建立一個 loop。

_（Iterator 的詳細介紹請期待下次的讀書會）_ :wink:

```js
const text = 'ES6 線上讀書會';

for (t of text) {
  console.log(t);
}

//

const emoji = String.fromCodePoint(0x1f602);

for (e of emoji) {
  console.log(e); // 😂
}
```

## at 方法

在前面 [codePointAt()](#codepointat-方法) 提到了這個方法可以正確的處理 **4 Byte** 的字元，因為實際上它是被拆成兩組，所以我們在這邊比較一下 ES5 和 ES6 使用的方法差異：

```js
const text = 'abc';
const emoji = '😂';

console.log(text.charAt(0)); // a
console.log(emoji.charAt(0)); // �
console.log(emoji.at(0)); // 😂
```

**at()** 可以正確的回傳大於 `0XFFFF` 的字元。

## normalize 方法

帶有**語調和重音符號**的字元，Unicode 有**兩種**方式可以表示：  

例如 `Ǒ` 符號可以表達成：

1. `Ǒ（\u01D1）` // 直接表達
2. `O（\u004F）` + `ˇ（\u030C）` // 組合字元

---

_所以**直接表達**和**組合字元**是等價的嗎？_

```js
console.log('\u01D1' === '\u004f\u030C'); // false
```
---

`normalize()` 會根據你所指定的 Unicode 正規形式，將字符串正規化。

> - NFC：預設參數，標準形式規範組成。
> - NFD：標準形式規範分解。
> - NFKC：標準形式兼容組成。
> - NFKD：標準形式兼容分解。

_更多參考 MDN：[String.prototype.normalize()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/normalize)_。

加入 `normalize()` 後的比較結果：

```js
console.log(
'\u01D1'.normalize() === '\u004f\u030C'.normalize()
); // true
```

## 字串確認

我們會使用 `indexOf` 來尋找字串首次出現的位置，`lastIndex` 來尋找字串最後出現的位置。

```js
const text = 'ES6 Study';

console.log(text.indexOf('E')); // 0
console.log(text.indexOf('I')); // 不存在回傳 -1

//

const text2 = 'Happy Birthday';

console.log(text2.lastIndexOf('y')); // 13
```

### includes 方法

確認字串是否包含如你參數中所指定的，回傳 `boolean` 值：

```js
// Syntax：str.includes(searchString[, position])

const text = 'ES6 Study';

console.log(text.includes('ES6')); // true
```

### startsWith 方法

確認字串的起始是否如你參數中所指定的，回傳 `boolean` 值：

```js
// Syntax：str.startsWith(searchString[, position])

const text = 'ES6 Study';

console.log(text.startsWith('ES6')); // true
```

### endsWith 方法

確認字串的尾部是否如你參數中所指定的，回傳 `boolean` 值：

```js
// Syntax：str.endsWith(searchString[, position])

const text = 'ES6 Study';

console.log(text.endsWith('Study')); // true
```

## repeat 方法

`repeat()` 方法回傳一個新字串。

```js
const A = 'A';

console.log(A.repeat(2)); // AA
console.log(A.repeat(3.6)); // AAA
console.log(A.repeat(-1.2)); // RangeError
```

## 字串補全

目前 `padStart()` 和 `padEnd()` 還在 ES **proposal** [stage-3](https://github.com/tc39/proposal-string-pad-start-end) 階段。  

這兩個方法可以自動幫我們補全字串的長度，可以在 **start** 或是 **end** 的地方做補全。

### padStart 方法

```js
// Syntax：str.padStart(targetLength [, padString])

'o'.padStart(5, 'Hell'); // Hello
```

### padEnd 方法

```js
// Syntax：str.padEnd(targetLength [, padString])

'123'.padEnd(6, 'x'); // 123xxx
```

## Template Strings

終於來到我們最常使用的 **Template String** 啦！

![](http://i.giphy.com/3NtY188QaxDdC.gif)

我們以前可能都是這樣輸出 HTML template 的：

```js
const t = 'My name is <b>' + name + '</b>, Nice to meet you'; // 不易閱讀！
```

_Template Strings - Example 1：_

```js
const name = 'PJ';
const t = `My name is <b>${name}</b>, Nice to meet you`; // 變數的部份寫在 `${}` 內來顯示
```

_Template Strings - Example 2：_

```js
const t1 = `Hello\nWorld`;
const t2 = `Hello, World
Hello ECMAScript 6
`;

console.log(t1); // 透過 \n 做換行
console.log(t2); // 換行
````

![超方便的！](http://i.giphy.com/bN5n5Yy3up5vO.gif)

也可以用在 HTML 的 style 設定：

```js
const style = {
    color: 'red',
    size: 4
};
let temp = `
    <span>
        <font size="${style.size}" color="${style.color}">Hi！</font>
    </span>
`;

console.log(temp);
```

## Tagged template literals

function 可以處理跟在後面的 template strings：

```js
function tag(str, ...values) {
  console.log(str);
  console.log(values[0]);
  console.log(values[1]);
}

const a = 5;
const b = 10;

tag`I have ${a} and ${b} number.`

// result =>
// [I have, and, number.]
// 5
// 10
```

## raw 方法

`raw` 是 ES6 String 原生的方法，可以用來處理 template stirngs：

```js
const text = String.raw`ECMAScript 6: \n`;
console.log(text === 'ECMAScript 6: \\n') // true
```

## Reference
- [Wiki - Unicode](https://zh.wikipedia.org/wiki/Unicode)
- [Unicode 字元平面對映](http://bit.ly/1TIEHvG)
- [MDN - String](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)
- [Unicode 与 JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)
- [Javascript Unicode](http://andyyou.github.io/javascript/2015/05/21/js-unicode-issue.html)
