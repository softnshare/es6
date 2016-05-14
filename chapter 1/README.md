
讀書會第一章內容 請參考 http://es6.ruanyifeng.com/#docs/intro  阮一峰大大

# 筆記整理
* 主講 polo
* 文字資料補充 james yang

## 第1節  ECMAScript和JavaScript的關係
ECMAScript和JavaScript的關係是，前者是後者的規格，後者是前者的一種實現

* 導讀影片 : https://youtu.be/sqh41P1FMbs  

## 第2節  ECMAScript的歷史  

2000開始 -> 2013凍結  -> 20015正式通過

![參考圖片](http://image.slidesharecdn.com/es6-140516103511-phpapp02/95/ecmascript-6-the-future-is-here-4-638.jpg?cb=1400236673)

* 導讀影片 :  https://youtu.be/aPVJ-8dHSns

## 第3節  部署進度

這個章節主要是講各平台目前對es6的支援度

* Node.js

* npm install 跟 使用
``` bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.1/install.sh | bash   
nvm ls-remote
nvm ls
nvm use
```

* 導讀影片 : https://youtu.be/RRN7yiGX9Ec

## 第4節  Babel 轉碼器   

* 配置文件 `.babelrc`

* 各種轉換方式：

 * 命令行轉碼 `babel-cli`
   - babel-node      (babel-cli安裝時就會裝)
   - babel-register  (程式直接轉不用另外產出)
   - babel-core      (使用Babel的API轉碼)
   - babel-polyfill  (讓 es6 api 與 es5 相容)  
>  由於 babel 預設不轉換 es6 相關的物件, 只轉語法 , 透過 import babel-polyfill 可以讓 es5 執行環境支援相關的物件,像是 generator , promise...)


* 瀏覽器環境   
  - 在線轉換 https://babeljs.io/repl/  


* 導讀影片 : https://youtu.be/eOw7cp_JXTQ

## 第5節 Traceur 轉碼器

* 安裝 `npm install -g traceur`
* 使用 `traceur calc.js`
* `node` 中使用 `traceur`

* 導讀影片 :  https://youtu.be/Cjb4-3r4DLk

## 第6節 ECMAScript 7
任何人都可以向TC39提案，從提案到變成正式標準，需要經歷五個階段。每個階段的變動都需要由TC39委員會批准。

* Stage 0 - Strawman（展示階段）
* Stage 1 - Proposal（徵求意見階段）
* Stage 2 - Draft（草案階段）
* Stage 3 - Candidate（候選人階段）
* Stage 4 - Finished（定案階段）

> 一個提案只要能進入Stage 2，就差不多等於肯定會包括在ES7裡面。
