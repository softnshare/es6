ES6 讀書會
== 
![alt](http://ryanchristiani.com/wp-content/uploads/2015/06/js-logo.png)

## 對象的擴展

Ellen

----

參考資料
- [ECMAScript 6 入門](http://es6.ruanyifeng.com/#docs/object)

- [MDN document](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)

---

## 1. 屬性的簡潔表示法

----

物件屬性使用變數簡潔表示

``` javascript
var name = 'Anna',
    age = 25,
    gender = 'female';


var es5Obj = { name: name, age: age, gender: gender };
var es6Obj = { name, age, gender };

console.log(es5Obj);
console.log(es6Obj);
// { name: 'Anna', age: 25, gender: 'female' }
```

----

物件方法使用函數簡潔表示

```javascript
var es5Obj = {
  foo: function() {},
  bar: function() {}
};

var es6Obj = {
  foo() {},
  bar() {}
};
```

----

範例 1
``` javascript
const obj = {
  get phone() {
    return this._phone;
  },
  set phone(phone) {
    if (!this.validatePhone(phone)) {
         this._phone = '+886' + phone.slice(2, 10);
    }
  },
  validatePhone(phone) {
    return (phone.indexOf("+886") > 0) ? true : false;
  },
}
obj.phone = '0931123456';
console.log(obj.phone); // +88631123456
```

----

範例 2
``` javascript
function cube(value) {
  return Math.pow(value, 3);
}

function cubeRoot(value) {
  return Math.pow(value, 1/3);
}

module.exports = { cube, cubeRoot };
// 同下
module.exports = {
  cube: cube,
  cubeRoot: cubeRoot
};
```

---

## 2. 屬性名表達式

----

``` javascript
// ES6
function getCar(make, model, value) {
  return {
    ['make' + make]: true
  };
}

// before ES6
function getCar(make, model, value) {
  var car = {};
  
  // 只能在個別指定值時使用表達式
  car['make' + make] = true;

  return car;
}
```

----

注意：屬性名表達式與簡潔表示法不能同時使用

``` javascript
let key = 'value',

    value = 'hello',

    // 語法錯誤
    // Uncaught SyntaxError: Unexpected token }
    errObj = { [key] };
    
    // 可寫為
    obj = { [key] : value }
```

---

## 3. 方法的 name 屬性

----

name 屬性回傳函數的名稱

``` javascriptvar
var object = {
  someMethod: function test() {}
};

console.log(object.someMethod.name); // 印出 "test"

object.someMethod.name = 'hello' // 僅供讀取

console.log(object.someMethod.name); // 依舊印出 "test"

```

----

匿名函數 name 屬性

``` javascript
var object = {
  someMethod: function() {}
};

console.log(object.someMethod.name); // ES6: someMethod
```

---

## 4. Object . is ( )

----

同值相等

``` javascript
Object.is('foo', 'bar');     // false
Object.is([], []);           // false

var test = { a: 1 };
Object.is(test, test);       // true

Object.is(null, null);       // true

// 特殊例子
Object.is(0, -0);            // false
Object.is(-0, -0);           // true
Object.is(NaN, 0/0);         // true
```

----

改善 `==` & `===` 比較運算符

- 相等運算符 (`==`)

:::danger
`"" == false // true`
:::

- 嚴格運算符 (`===`) 

:::danger
`-0 === +0 // true`
:::

---

## 5. Object . assign ()

----

將來源物件的所有可枚舉屬性，複製到目標物件

``` javascript
Object.assign(target, ...sources)
```

----

- 物件的每個屬性都有一個描述對象（Descriptor），控制該屬性的行為

```javascript
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

----

常見用法

----

拷貝物件

```javascript
var obj = { a: 1 };

var copy = Object.assign({}, obj);

console.log(copy); // { a: 1 }
```

----

合併多個物件

```javascript
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 }, 目標物件已被改變
```

----

複製 Symbol 型別屬性

```javascript
var o1 = { a: 1 };
var o2 = { [Symbol('foo')]: 2 };

var obj = Object.assign({}, o1, o2);
console.log(obj); // { a: 1, [Symbol("foo")]: 2 }
```

----

- 限制：
只拷貝物件自身屬性 ( 不拷貝繼承屬性 )
不拷貝不可枚舉 ( `enumerable: false` ) 的屬性

```javascript
var obj = Object.assign({b: 'c'},
  Object.defineProperty({}, 'invisible', {
    enumerable: false,
    value: 'hello'
  })
)
// { b: 'c' }
```

----

- 注意：
Primitives 型別會被包裹成物件
(string, number, boolean, null, undefined, symbol)
```javascript
var v1 = 'abc';
var v2 = true;
var v3 = 10;
var v4 = Symbol('foo');

var obj = Object.assign({}, v1, null, v2, undefined, v3, v4); 
// null & undefined 會被忽略
// 字串會以數組形式，複製進目標物件
console.log(obj); // { "0": "a", "1": "b", "2": "c" }
```

```javascript
Object(true) // {[[PrimitiveValue]]: true}
Object(10)  //  {[[PrimitiveValue]]: 10}
Object('abc')
// {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
```

---

## 6. 屬性的遍例

----

ES6 有五種方式可遍歷物件屬性：

1. for...in

2. Object.keys(obj)

3. Object.getOwnPropertyNames(obj)

4. Object.getOwnPropertySymbols(obj)

5. Reflect.ownKeys(obj)

----

1. `for...in`
循環遍歷物件本身的屬性和繼承的可枚舉屬性
（不含Symbol屬性）

```
注意：操作中引入繼承的屬性會讓問題複雜化，通常我們只關心物件本身的屬性
儘量少用for...in循環，而用 Object.keys() 代替
```

----

2. `Object.keys(obj)`
返回一個陣列
包括物件本身的（不含繼承的）所有可枚舉屬性
（不含Symbol屬性）

```javascript
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };

console.log(Object.keys(an_obj)); // console: ['2', '7', '100']
```

----

將非物件傳入函式

```javascript
Object.keys("foo");
// TypeError: "foo" is not an object (ES5 code)

Object.keys("foo");
// ["0", "1", "2"]                   (ES6 code)
```

----

3. `Object.getOwnPropertyNames(obj)`
返回一陣列，包含對象自身的所有屬性
（不含Symbol屬性，但包括不可枚舉屬性）

```javascript
var my_obj = Object.create({}, {
  getFoo: {
    value: function() { return this.foo; },
    enumerable: false
  }
});
my_obj.foo = 1;

console.log(Object.getOwnPropertyNames(my_obj).sort()); 
// logs ["foo", "getFoo"]
```

----

將非物件傳入函式

```javascript
Object.getOwnPropertyNames('foo');
// TypeError: "foo" is not an object (ES5 code)

Object.getOwnPropertyNames('foo');
// ["0", "1", "2", "length"]  (ES6 code)
```

----

4. `Object.getOwnPropertySymbols(obj)`
返回一陣列，包含物件本身的所有Symbol屬性

```javascript
var obj = {};
var a = Symbol('a');
var b = Symbol.for('b');

obj[a] = 'localSymbol';
obj[b] = 'globalSymbol';

var objectSymbols = Object.getOwnPropertySymbols(obj);

console.log(objectSymbols.length); // 2
console.log(objectSymbols);        // [Symbol(a), Symbol(b)]
```

----

5. `Reflect.ownKeys(obj)`
返回一陣列，包含物件本身的所有屬性，無論屬性名是Symbol或字符串，以及是否可枚舉

```javascript
Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 })
// ['2', '10', 'b', 'a', Symbol()]
```

---

## 7. Object Prototype

----

1. __proto__屬性

2. Object.setPrototypeOf()

3. Object.getPrototypeOf()

----

1. `__proto__` 屬性
用來讀取或設置當前物件的 `prototype` 物件

```javascript
var shape = {};
var circle = new Circle();

// 設定物件原型
// 不建議正式使用
shape.__proto__ = circle;

// 拿取物件原型
console.log(shape.__proto__ === circle); // true
```

----

- 為一內部屬性，不正式對外
由於瀏覽器廣泛支持，才被加入了ES6

----

2. Object.setPrototypeOf()
同`__proto__`，用來設置一物件的 prototype 物件

```javascript
Object.setPrototypeOf(obj, prototype);
```

----

範例
```javascript
var o = Object.setPrototypeOf({}, null);

// 同下
function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}
```

----

:exclamation:

改變一物件的 [[Prototype]] 需較長作業時間
如有性能考量建議使用 `Object.create()` 建一新物件設定相關 [[Prototype]]

----

3. Object.getPrototypeOf()
用於讀取物件的原型

```javascript
var proto = {};
var obj = Object.create(proto);
Object.getPrototypeOf(obj) === proto; // true
```

----

傳入非物件參數
```javascript
Object.getPrototypeOf("foo");
// TypeError: "foo" is not an object (ES5 code)
Object.getPrototypeOf("foo");
// String.prototype                  (ES6 code)
```

---

:microscope: ES7

---

## 8. Object.value() & Object.entries()

----

ES7提議引入 Object.keys() 的配套

Object.value()
```javascript
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };

console.log(Object.values(an_obj));
// ['b', 'c', 'a']
```

Object.entries()
```javascript
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };

console.log(Object.entries(an_obj));
// [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]
```

----

- Object.entries()
可用於將物件轉換為 Map 結構

```javascript
var obj = { foo: "bar", baz: 42 }; 

var map = new Map(Object.entries(obj));
console.log(map); // Map { foo: "bar", baz: 42 }
```

---

## 9. 物件的擴展運算符

----

ES7 提案
將 Rest 解構賦值 / 擴展運算符 (...) 引入物件

----

1. Rest 解構賦值
從一個物件取值，將所有可遍歷的、但尚未被讀取的屬性，分配到指定的物件上面。所有的鍵和它們的值，都會拷貝到新物件上面

```javascript
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x; // 1
y; // 2
z; // { a: 3, b: 4 }
```

----

- Rest 解構賦值的拷貝為淺拷貝

```javascript
let obj = { a: { b: 1 } };
let { ...x } = obj;
obj.a.b = 2;
x.a.b // 2
```

如果一個鍵的值是復合類型的值（陣列、物件、函數）
那麼Rest解構賦值拷貝的是這個值的引用，而不是這個值的副本

----

- Rest 解構賦值不會拷貝繼承自原型物件的屬性

```javascript
let o1 = { a: 1 };

let o2 = { b: 2 };
o2.__proto__ = o1;

let o3 = { ...o2 };
o3 // { b: 2 }
```

----

2. 擴展運算符 (...)
用於取出參數物件的所有可遍歷屬性，拷貝到當前物件中

```javascript
let n = { x, y, ...z };
n; // { x: 1, y: 2, a: 3, b: 4 }
```

:arrow_right: 相當於使用 Object.assign()

----

可用於合併兩物件

```javascript
let ab = { ...a, ...b };

// 同下
let ab = Object.assign({}, a, b);
```

---

### 10. Object.getOwnPropertyDescriptors()

----

ES7 提案新增一方法：
返回指定物件所有自身屬性（非繼承屬性）的描述物件

```javascript
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};

Object.getOwnPropertyDescriptors(obj)
// { foo:
//    { value: 123,
//      writable: true,
//      enumerable: true,
//      configurable: true },
//   bar:
//    { get: [Function: bar],
//      set: undefined,
//      enumerable: true,
//      configurable: true } }
```

----

- 目的：為解決 Object.assign()
無法正確拷貝get屬性和set屬性的問題

- 配合Object.defineProperties方法實現正確拷貝
```javascript
const source = {
  set foo(value) {
    console.log(value);
  }
};

const target = {};
Object.defineProperties(target, Object.getOwnPropertyDescriptors(source));
Object.getOwnPropertyDescriptor(target, 'foo')
// { get: undefined,
//   set: [Function: foo],
//   enumerable: true,
//   configurable: true }
```

----

- 可配合 Object.create()，將物件屬性複製到一個新物件（屬於淺拷貝）


```javascript
Object.create(
  Object.getPrototypeOf(obj), 
  Object.getOwnPropertyDescriptors(obj) 
);
```

---

Thanks :bow: 
