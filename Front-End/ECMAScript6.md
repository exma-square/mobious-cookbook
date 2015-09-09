# ECMAScript 6
ECMAScript 6（以下簡稱 ES6）是 JavaScript 語言的下一代標準，已經在 2015 年 6 月正式發佈了。

ES6 標準制定的目標是為了讓 JavaScript 語言可以用來建構大型且複雜的前後端應用程式，成為高活且可靠的企業級語言。

ECMAScript 標準往後將計劃每年發佈一次新標準，使用年份作為標準的版本。因為當前版本的 ES6 是在 2015 年發佈的，所以又稱 ECMAScript 2015。

## ECMAScript 與 JavaScript 的關係
ECMAScript 是 JavaScript 語言的國際標準，JavaScript 是 ECMAScript 標準的實作品語言。

JavaScript 原名 LiveScript，最早是瀏覽器製造商 Netscape 公司為旗下的瀏覽器產品 Netscape Navigator 所開發來添加一些網頁效果的腳本語言，後來因為 Netscape 與 Sun 公司合作而改名成為 JavaScript。

1996 年 11 月，Netscape 公司決定將 JavaScript 提交給國際標準化組織 ECMA（歐洲計算機製造商協會），希望這種語言能夠成為國際標準，以保證這門語言的開放性和中立性。次年，ECMA 發佈262 號標準文件（ECMA-262）的第一版，規定了瀏覽器腳本語言的標準，並將這種語言稱為 ECMAScript。這個版本就是 ECMAScript 1.0 版，並持續演進到現在。因此，ECMAScript 和 JavaScript 的關係是，前者是後者的規格，後者是前者的一種實現。在日常場合，這兩個詞是可以互換的。

前端工程日漸崛起，以及 Node.js 讓 ECMAScript 在後端嶄露頭角，使得 JavaScript 語言的地位漸漸抬頭，對於建構大型且複雜的前後端應用程式的需求提高，在這樣的背景之下催生了 ES6 的演進。在經過了各方多次的爭論與協調共識，新一代標準 ES6 的草案終於定案，並且在 2015 年 6 月正式通過，成為國際標準。

## 現在就開始使用 ECMAScript 6
### 瀏覽器端
各家主流瀏覽器皆已經開始實作 ES6 的新功能與特性，但目前還未到完善的程度，因此我們可以使用像是 [Babel](https://babeljs.io/) 這種編譯器，幫助我們將寫好的 ES6 程式碼，轉換成瀏覽器可以正常運作無虞的 ES5 程式碼。

更多Babel的細節請見 [Assets處理 ─ Babel](/Assets/Babel.html) 章節。

### 伺服器端
Node.js 和 io.js（一個部署新功能更快的 Node.js 分支）是 ECMAScript 語言的在伺服器端環境的實現。它們目前對 ES6 的支持度比瀏覽器更高，通過它們將可以體驗更多 ES6 的功能與特性。

--------------------------
在 Mobious 中，我們全面使用 ES6 語法進行開發。

若您想了解一些 ES6 語法細節，推薦您閱讀 [ECMAScript 6 入門](http://es6.ruanyifeng.com/)。
