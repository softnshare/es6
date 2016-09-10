# Chapter 17. 異步操作和 Async 函数

### 大綱

- [一. Blocking and waiting](#ㄧ-blocking-and-waiting)
- [二. Callback function](#二-callback-function)
- [三. ES2015 Promise](#三-es2015-promise)
- [四. ES2015 Generator function](#四-es2015-generator-function)
- [五. ES2016 Async function](#五-es2016-async-function)
- [六. More Async programming](#六-more-async-programming)
- [七. 參考資源](#七-參考資源)
- [八. Curators](#八-curators)

## ㄧ. Blocking and waiting

#### Blocking (pulling):

```js
function login() {
  const fbUser = fbLogin();
  const user = getCurrentUser(fbUser);
  return user;
}
```

在 Jafar Husain 那部影片中，他提到的 Pulling 概念可以用下面這一句來想像：  
`fbUser <- fbLogin()`  

換成白話文的意思就是 - 我們調用 fbLogin 這個函數，並將 fbUser 的值從中拉 (pull) 出來。

#### Waiting (pushing):

```js
function login() {
  fbLogin((fbUser) => {
    getCurrentUser(fbUser, (user) => {
    });
  });
}
```

如果 fbLogin 是非同步的請求（例如 AJAX），我們沒辦法馬上得到資料，也不能讓程式 blocking 在這一行（如果使用者點了登入按鈕，卻看到頁面卡在那裡是會抓狂的）。

所以我們可以使用 Pushing 的概念，用下面這一行想像：  
`fbLogin( -> fbUser)`  

白話文 - 我們調用 fbLogin，並將結果值推 (push) 給 fbUser。

## 二. Callback function

如果瞭解了 blocking 和 waiting 的概念，在 ES6 以前，如果我們要寫 Async 的邏輯，勢必一定要用 callback。

```js
function login(callback) {
  fbLogin((err, fbUser) => {
    if (err) {
      callback(err);
      return;
    }
    getCurrentUser(fbUser, (err, user) => {
      callback(err, user);
    });
  });
}
```

為了不讓我們的程式停在非同步請求，我們將邏輯拆分成兩段：  
一段是調用請求的主程式；  
一段是處理請求結果的 callback 函數。

不過使用 Callback 處理 Async 的隱憂就是：  

![Callback hell](http://icompile.eladkarako.com/wp-content/uploads/2016/01/icompile.eladkarako.com_callback_hell.gif)

## 三. ES2015 Promise

到了 ES6，想必你也用過 Promise 來避免 Callback hell：

```js
fbLogin()
  .then(getCurrentUser)
  .then((user) => console.log(user))
  .catch((err) => console.log(err));
```

## 四. ES2015 Generator function

ES6 提供了我們另外一個語法糖 - [Generator](../chapter-15)：

```js
function* login() {
  const fbUser = yield fbLogin();
  const user = yield getCurrentUser(fbUser);
  return user;
}
```

這語法糖讓我們可以使用 [Pulling](#blocking-pulling) 的方式撰寫非同步操作，讓編寫同步和非同步程式的思維一致化。

> PS. 如果你把上面那一段程式的 `*` 與 `yield` 拿掉，是不是就跟同步邏輯一樣了呢！

#### 使用 `spawn`

不過 Generator 是一個狀態機，我們必須調用 login 函數後拿到最後 user 的值，而不是停在每一個 `yield` 的中斷點上；因此我們需要一個執行器。

```js
const promise = spawn(login());

promise
  .then((user) => console.log(user))
  .catch((err) => console.log(err));
```

`spawn.js` 幫我們解決了這個問題，我們將 Generator 回傳的 iterator 遞給 spawn，它會幫我們執行每一個非同步操作，直到那到最後的值，並 push 到你的程式中。

#### `spawn.js` 是如何執行這些非同步的？

```js
function spawn(iterator) {
  return new Promise((resolve, reject) => {
    const onResult = (data) => {
      const { value, done } = iterator.next(data);
      if (!done) value.then(onResult);
      else resolve(data);
    };
    onResult();
  });
}
```

不過使用 Generator 來撰寫 push 風格的非同步操作，卻需要額外的執行器（spawn.js）；想像以後我們需要專們處理 Async 的語法糖，不再需要使用者些執行器...

## 五. ES2016 Async function

ES7 的 Async/Await 就是專門解決非同步操作的語法糖，我們不再需要引用額外的程式。

```js
async function login() {
  const fbUser = await fbLogin();
  const user = await getCurrentUser(fbUser);
  return user;
}

login()
  .then((user) => console.log(user))
  .catch((err) => console.log(err));
```

## 六. More Async programming

在 Jafar Husain 的影片末也有提到未來 ES Async programming 的走向，例如 Observable interface：

```js
async function getReplies() {
  for (let reply on new WebSocket('/posts/1/replies')) {
    console.log(reply);
  }
}
```

這部影片值得去看一看，可以了解整個 Async programming 的來龍去脈（[連結](https://www.youtube.com/watch?v=lil4YCCXRYc)）！

## 七. 參考資源

1. [Jafar Husain: Async Programming in ES7 | JSConf US 2015](https://www.youtube.com/watch?v=lil4YCCXRYc)
2. [异步操作和 Async 函数](http://es6.ruanyifeng.com/#docs/async)
3. [Callback Hell - A guide to writing asynchronous JavaScript programs](http://callbackhell.com/)

## 八. Curators

- [shiningjason](http://shiningjason1989.github.io/): Hope this article will inspire you 🍾🍾🍾

