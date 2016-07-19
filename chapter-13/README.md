ES6 讀書會
====
Set 和 Map 數據結構

Doris

---

## 導讀內容 
- Set
- Map
- WeakSet
- WeakMap

---

## Set
- 有序列表集合(a list of values)
	- 類似 Array ，但成員值都是唯一的。
	- 是構造函數，可以用 new 來創建 Set 資料結構。
	- 可接受的參數型別：Array (或類似 Array 的物件)。
	- 加入值時，不會轉換型別；5 和 “5” 視為不同。
	- 內部判斷重複值可視為使用“===”，但有些不同：
		- NaN 只能加入一次
		- “-0”和“0 或 +0”可同時存在
	- 兩個物件總是不相等。

----

## Set 程式範例
```javascript=
//構造函數
var s = new Set([1,2,3,4,4,5,"5",NaN,NaN,0,-0]);
[...s]; //[1,2,3,4,5,"5",NaN,0,-0] 將重複值剔除

//類似 Array 的物件
function divs () {
  return [...document.querySelectorAll('div')];
}

var set = new Set(divs());
set.size;
```

----

## Set 實做的屬性與方法
- 屬性
	- Set.prototype.constructor
	- Set.prototype.size
- 方法
	- add(value):新增某個值，返回 Set 結構本身
	- delete(value):刪除某個值，返回 Boolean
	- has(value):返回 Boolean，表示是否為 Set 的成員
	- clear():清除所有值

----

## Set 實做的屬性與方法程式範例
```javascript=
//add(value) 因為回傳 Set 結構本身，可用 Cascading
var s = new Set();
s.add('a')
 .add('b')
 .add('c');

//delete(value)
s.delete('b');

//has(value)
s.has('a');

//clear()
s.clear();
```

----

## Set 結構轉為 Array
- Array.from()
- Set 與 Array.from() 提供 Array 除去重複值的另一種方法
```javascript=
var array2 = Array.from(new Set([1,1,2,2,3]));
[...array2]
```

----

## Set 的遍歴操作
- Set 遍歴順序就是插入順序，這個特性保證使用 Set 保存一個回調函數列表，調用時可以保證按照新增順序調用。
	- keys():返回鍵名
	- values():返回值
	- entries():返回鍵值對
	- forEach():利用回調函數，遍歴所有成員

----

## keys()，values()，entries()
- Set 結構只有鍵值，或說鍵名與鍵值是同一個值，所以 keys() 與 values() 行為相同。
- Set 結構實作預設遍歷器生成函數就是 values()，表示可省略  values()，直接使用 for...of 歷遍 Set

----

## 程式範例
```javascript=
let set = new Set(['red', 'green', 'blue']);
for (let item of set.keys()) {
  console.log(item);
}
for (let item of set.values()) {
  console.log(item);
}
for (let item of set.entries()) {
  console.log(item);
}
for (let item of set) {
  console.log(item);
}
```

----

## forEach()
```javascript=
let set = new Set([1, 2, 3]);
set.forEach((v, k) => console.log(v * 2));
```

----

## 遍歷的應用
- 擴展運算符(...)
	- 去除 Array 重複的成員
	- Array 的 filter 與 map 可用於 Set
	- 實現聯集(Union),交集(Intersect),差集(Defference)
- 遍歷操作中，同步改變 Set 結構
	- 利用 Set 映射出新的 Set 結構，再賦值給原本的 Set
	- 利用 Array.from()

----

## 程式範例 - 擴展運算符(...)
```javascript=
let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));

let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
```

----

## 程式範例 - 遍歷操作中，同步改變 Set 結構
```javascript=
// 映射新的 Set 再賦值給原本的 Set
let set = new Set([1, 2, 3]);
set = new Set([...set].map(val => val * 2));

// Array.from()
let set = new Set([1, 2, 3]);
set = new Set(Array.from(set, val => val * 2));
```

---

## Map
- 有序鍵值對集合(a collection of keys that correspond to specific values)
	- 類似物件，也是鍵值對的集合。
	- 鍵的範圍不限於字串。
	- 是構造函數，可以用 new 來創建 Map 資料結構。
	- 可接受成員為鍵值對的 Array 作為參數。
	- 對同一個鍵多次賦值，後面的值會覆蓋前面的。
	- key 是物件時，對同一物件的引用才會視為同一個。
	- 若兩個鍵嚴格相等視為同一個鍵，但有例外
		- NaN 視為同一個鍵
		- “-0”和“0 或 +0”視為同一個鍵

----

## Map 程式範例
```javascript=
//Array 中有兩個鍵值對的 Array
var map = new Map([['name', 'Jhon'], ['title', 'test']]);
console.log(map); //Map {"name" => "Jhon", "title" => "test"}

//對同一物件的引用
var map1 = new Map();
var obj = ['a'];
map1.set(obj, 555);
map1.get(obj);
```

----

## Map 實做的屬性與方法
- 屬性
	- size
- 方法
	- set(key, value):設置 key 對應的 value，返回整個 Map 資料結構。
	- get(key):讀取 key 對應的 value。
	- has(key):返回 Boolean，表示某個 key 是否在 Map 結構中。
	- delete(key):返回 Boolean，刪除某個 key。
	- clear():清除所有成員。

----

## Map 實做的屬性與方法程式範例
```javascript=
let m = new Map();
m.set("edition", 6)        // 鍵是字符串
m.set(262, "standard")     // 鍵是數值
m.set(undefined, "nah")    // 鍵是undefined

var hello = function() {console.log("hello");}
m.set(hello, "Hello ES6!") // 鍵是函數

m.size // 4
m.get(hello); // Hello ES6!
m.has(262); // true
m.has(123); // false
m.delete(262); // true
m.clear();
```

----

## Map 的遍歴操作
- Map 遍歴順序就是插入順序。
	- keys():返回鍵名
	- values():返回值
	- entries():返回鍵值對
	- forEach():利用回調函數，遍歴所有成員

----

## entries()
- Map 結構實作預設遍歷器生成函數就是 entries()，表示可省略  entries()，直接使用 for...of 歷遍 Map
```javascript=
let map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
```

----

## forEach
```javascript=
let map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

map.forEach(function(value, key, map){
  console.log("Key: %s, Value: %s", key, value);
});

//可以接受第二個參數，用來綁定 this
var map2 = new Map().set(1, 'a').set(2, 'b').set(3, 'c');
var reporter = {
	report: function(x, y){
		return x + y;
	}
}
map2.forEach(function(v, k){
    console.log(this.report(1, 2)); //this 指向 reporter
}, reporter);
```

----

## 與其他資料結構的互相轉換
- Map 與 Array
- Map 與 Object
- Map 與 JSON

----

## Map 與 Array
- Map 轉為 Array >> 使用擴展運算符(...)
- 結合 Array 的 map() 與 filter()，可實現 Map 的遍歷(map)與過濾(filter)

----

## Map 轉為 Array
```javascript=
let map = new Map([
  [1, 'a'],
  [2, 'b'],
  [3, 'c'],
]);

// 產生 Map 結構 {1 => 'a', 2 => 'b'}
let map1 = new Map(
  [...map].filter(([k, v]) => k < 3)
);

// 產生 Map 結構 {2 => '_a', 4 => '_b', 6 => '_c'}
let map2 = new Map(
  [...map].map(([k, v]) => [k * 2, '_' + v])
    );
```

----

## Array 轉為 Map
- 將 Array 轉入 Map 構造函數即可
```javascript=
var map3 = new Map([[true, 7], [{foo: 3}, ['abc']]]);
```

----

## Map 與 Object
- 若 Map 的 key 都是字串，可以轉為 Object；key 不為 字串，轉換後的物件 key 會不正確。
```javascript=
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToObj(myMap)
```

----

## Map 與 JSON
- Map 轉 JSON (JSON.stringify)
	- Map 的 key 都是字串符，可轉為物件 JSON。
	- Map 的 key 有非字串符，可轉為陣列 JSON。
```javascript=
//key 都是字串
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)

//key 有非字串符
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
```

---

## WeakSet
- 與 Set 結構相似，也是不重複的值的集合。
- 區別:
	- 成員只能是物件類型
	- 成員都是弱引用，這個特性意味 WeakSet 不可遍歷
- 可用在 DOM 節點的儲存，不需擔心節點被刪除，引發記憶體洩漏(Memory Leak)
```javascript=
var ws = new WeakSet();
ws.add(1) // TypeError: Invalid value used in weak set

//以下範例，a 是陣列，有兩個成員，都是陣列
//a 的成員會成為 WeakSet 的成員，不是 a 陣列本身
//這意味陣列的成員只能是物件類型
var a = [[1,2],[3,4]];
ws.add(a);
```

----

## WeakSet 方法
- WeakSet.prototype.add(value)
- WeakSet.prototype.delete(value)
- WeakSet.prototype.has(value)


---

## WeakMap
- 與 Map 結構相似。
- 區別:
	- 只接受非空物件 (non-null object) 為 key
- key 指向的物件不計入垃圾回收機制。
- 設計目的:
	- key 是物件的弱引用，物件回收後 WeakMap 自動移除對應的鍵值對
- 典型應用於一個 DOM 元素的 WeakMap 結構。
- 有助於防止記憶體洩漏(Memory Leak)。

----

## WeakMap 與 Map 在 API 上的區別
- 沒有遍歷操作(沒有 keys(), values(), entries())，也沒有 size 屬性
- 無法清空，不支持 clear()












