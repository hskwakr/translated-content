---
title: 'Express 教學 4: 路由與控制器'
slug: Learn/Server-side/Express_Nodejs/routes
translation_of: Learn/Server-side/Express_Nodejs/routes
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/mongoose", "Learn/Server-side/Express_Nodejs/Displaying_data", "Learn/Server-side/Express_Nodejs")}}</div>

<p>在本教程中，我們將為最終在 本地圖書館 網站中需要的所有資源端點，搭配 "空殼" 處理函式來配置路由 (URL handling code) 。完成後，我們的路由處理源碼將會有模組化結構，在接下來的文章中，我們可以用真實的處理函式加以擴充。我們也會對如何使用Express 創建模組化路由，有更好的理解。</p>

<table class="learn-box standard-table">
 <tbody>
  <tr>
   <th scope="row">先備知識:</th>
   <td>閱讀 <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction">Express/Node 介紹</a>。 完成先前教學主題 (包含 <a href="/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose">Express 教學 3: 使用資料庫 (Mongoose)</a>).</td>
  </tr>
  <tr>
   <th scope="row">目標:</th>
   <td>理解如何創建簡易路由配置。我們所有的URL端點。</td>
  </tr>
 </tbody>
</table>

<h2 id="概覽">概覽</h2>

<p>在<a href="/zh-CN/docs/Learn/Server-side/Express_Nodejs/mongoose">上一篇教程文章</a>中，我們定義了Mongoose模型，以與數據庫互動，並使用（獨立）腳本創建一些初始庫記錄。現在我們可以編寫代碼，向用戶展示這些信息。我們需要做的第一件事，是確定我們希望能夠在頁面中顯示哪些信息，然後定義適當的URL，以返回這些資源。然後我們將需要創建路由（URL處理程序）和視圖（模板）來顯示這些頁面。</p>

<p>下圖是作為處理HTTP請求/響應時，需要實現的主要數據流和事項的提醒。除了視圖和路線之外，圖表還顯示“控制器” — 實際處理請求的函數，那些與路由請求分開的代碼。</p>

<p>由於我們已經創建了模型，我們需要創建的主要內容是：</p>

<ul>
 <li>“路由”將支持的請求（以及請求URL中編碼的任何信息）轉發到適當的控制器功能。</li>
 <li>控制器用於從模型中獲取請求的數據，創建一個顯示數據的HTML頁面，並將其返回給用戶，以在瀏覽器中查看。</li>
 <li>視圖（模板）則由控制器用來呈現數據。</li>
</ul>

<p><img src="mvc_express.png"></p>

<p>最終，我們可能會有頁面顯示書籍，流派，作者和書籍的列表和詳細信息，以及用於創建，更新和刪除記錄的頁面。對一篇文章來說，這是很多的內容。因此，本文的大部分內容，都將集中在設置我們的路由和控制器，以返回“虛擬”內容。我們將在後續文章中，擴展控制器方法，以使用模型數據。</p>

<p>下面的第一部分，提供了關於如何使用<a href="http://expressjs.com/en/4x/api.html#router">Express Router</a>中間件的簡要“入門”。當我們設置LocalLibrary路由時，我們將在後面的章節中使用這些知識。</p>

<h2 id="路由入門">路由入門</h2>

<p>路由是Express代碼的一部分，它將HTTP動詞（<code>GET</code>, <code>POST</code>, <code>PUT</code>, <code>DELETE</code>等），URL路徑/模式和被調用來處理該模式的函數，相關聯起來。</p>

<p>有幾種方法可以創建路線。本教程將使用<code><a href="http://expressjs.com/en/guide/routing.html#express-router">express.Router</a></code>中間件，因為它允許我們將站點的特定部分的路由處理程序組合在一起，並使用通用的路由前綴訪問它們。我們會將所有與圖書館有關的路由，保存在“目錄”模塊中，如果我們添加路由來處理用戶帳戶或其他功能，我們可以將它們分開保存。</p>

<div class="note">
<p><strong>備註：</strong> 我們在<a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction#Creating_route_handlers">Express簡介&gt;創建路由處理程序</a>中，簡要討論了Express應用程序路由。除了為模塊化提供更好的支持之外（如下面第一小節所述），使用Router非常類似於直接在Express應用程序對像上定義路由。</p>
</div>

<p>本節的其餘部分，概述瞭如何使用路由器<code>Router</code>來定義路由。</p>

<h3 id="定義和使用單獨的路由模塊">定義和使用單獨的路由模塊</h3>

<p>下面的代碼提供了一個具體示例，說明我們如何創建路由模塊，然後在Express應用程序中使用它。首先，我們在一個名為<strong>wiki.js</strong>的模塊中創建一個wiki的路由。代碼首先導入Express應用程序對象，使用它獲取一個</p>

<p><code>Router</code>對象，然後使用<code>get()</code>方法向其添加一對路由。所有模塊的最後一個導出路由器<code>Router</code>對象。</p>

<pre class="brush: js"><code>// wiki.js - Wiki route module.

var express = require('express');
var router = express.Router();

// Home page route.
router.get('/', function (req, res) {
  res.send('Wiki home page');
})

// About page route.
router.get('/about', function (req, res) {
  res.send('About this wiki');
})

module.exports = router;</code>

</pre>

<div class="note">
<p><strong>備註：</strong> 上面我們直接在路由器函數中定義路由處理程序回調。在LocalLibrary中，我們將在一個單獨的控制器模塊中，定義這些回調。</p>
</div>

<p>要在主應用程序文件中使用路由器模塊，我們首先<code>require()</code>路由模塊（<strong>wiki.js</strong>）。然後，我們在Express應用程序上調用<code>use()</code>，將路由器添加到中間件處理路徑，並指定一個'wiki'的URL路徑。</p>

<pre class="brush: js"><code>var wiki = require('./wiki.js');
// ...
app.use('/wiki', wiki);</code></pre>

<p>然後可以從<code>/wiki/</code>和<code>/wiki/about/</code>，訪問我們的wiki路由模塊中定義的兩個路由。</p>

<h3 id="路由函數">路由函數</h3>

<p>我們上面的模塊，定義了幾個典型的路由功能。使用<code>Router.get()</code>方法定義“about”路由（在下面），該方法僅響應HTTP GET請求。此方法的第一個參數是URL路徑，而第二個參數是一個回調函數，如果收到帶有路徑的HTTP GET請求，將會調用該函數。</p>

<pre class="brush: js"><code>router.get('/about', function (req, res) {
  res.send('About this wiki');
})</code>
</pre>

<p>回調函數接受三個參數（通常如下所示命名：<code>req</code>, <code>res</code>, <code>next</code>），它將包含HTTP請求對象，HTTP響應，以及中間件鏈中的下一個函數。</p>

<div class="note">
<p><strong>備註：</strong> 路由器功能是<a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction#Using_middleware">Express中間件</a>，這意味著它們必須完成（響應）請求或調用鏈中的下一個功能<code>next</code>。在上面的例子中，我們使用<code>send()</code>完成了請求，所以下一個參數<code>next</code>沒有被使用（我們選擇不指定它）。</p>

<p>上面的路由器函數只需要一次回調，但您可以根據需要指定任意數量的回調參數，或一組回調函數。每個函數都是中間件鏈的一部分，並且將按照添加到鏈中的順序調用（除非前面的函數完成請求）。</p>
</div>

<p>這裡的回調函數，在響應中調用<code><a href="https://expressjs.com/en/4x/api.html#res.send">send()</a></code>，當我們收到帶有路徑（' <code>/about'</code>）的GET請求時，返回字符串“About this wiki”。有<a href="https://expressjs.com/en/guide/routing.html#response-methods">許多其他響應方法</a>，可以結束請求/響應週期。例如，您可以調用<code><a href="https://expressjs.com/en/4x/api.html#res.json">res.json()</a></code>，來發送JSON響應，或調用<code><a href="https://expressjs.com/en/4x/api.html#res.sendFile">res.sendFile()</a></code>來發送文件。構建庫時，我們最常使用的響應方法是<a href="https://expressjs.com/en/4x/api.html#res.render">render()</a>，它使用模板和數據創建並返回HTML文件—我們將在後面的文章中，進一步討論這個問題！</p>

<h3 id="HTTP_動詞">HTTP 動詞</h3>

<p>上面的示例路由使用<code>Router.get()</code>方法，響應具有特定路徑的HTTP GET請求。路由器<code>Router</code>還為所有其他HTTP動詞提供路由方法，這些方法多數以完全相同的方式使用：<code>post()</code>, <code>put()</code>, <code>delete()</code>, <code>options()</code>, <code>trace()</code>, <code>copy()</code>, <code>lock()</code>, <code>mkcol()</code>, <code>move()</code>, <code>purge()</code>, <code>propfind()</code>, <code>proppatch()</code>, <code>unlock()</code>, <code>report()</code>, <code>mkactivity()</code>​​​​​​, <code>checkout()</code>, <code>merge()</code>, <code>m-</code><code>search()</code>, <code>notify()</code>, <code>subscribe()</code>, <code>unsubscribe()</code>, <code>patch()</code>, <code>search()</code>,和<code>connect()</code>。</p>

<p>例如，下面的代碼就像上一個<code>/about</code>路由一樣，但只響應HTTP POST請求。</p>

<pre class="brush: js"><code>router.post('/about', function (req, res) {
  res.send('About this wiki');
})</code></pre>

<h3 id="路由路徑">路由路徑</h3>

<p>路由路徑定義可以進行請求的端點。我們到目前為止看到的例子，都是字符串，並且完全按照字符串的寫法使用：'/'，'/ about'，'/ book'，'/any-random.path'。</p>

<p>路由路徑也可以是字符串模式。字符串模式使用正則表達式語法的子集，來定義將匹配的端點模式。下面列出了子集（請注意，連字符（<code>-</code>）和點（<code>.</code>）由字符串路徑字面解釋）：</p>

<ul>
 <li>? :端點在?號前面的那個字符，必須為0個或1個。例如。<code>'/ab?cd'</code>的路徑路徑將匹配端點<code>acd </code>或<code>abcd</code>。</li>
 <li>+ :端點在+號前面的那個字符，必須為1個或多個。例如，<code>'/ab+cd'</code>的路徑路徑將與端點<code>abcd</code>，<code>abbcd</code>，<code>abbbcd</code>等匹配。</li>
 <li>* :端點在放置*字符的地方，可以代換為任意字符串。例如。<code>'ab\*cd'</code>的路由路徑，將匹配端點<code>abcd</code>, <code>abXcd</code>, <code>abSOMErandomTEXTcd</code>等。</li>
 <li>() :將一組字符進行匹配，以執行上面三個操作。例如。<code>'/ab(cd)?e'</code>，表示以？號對（cd）進行匹配-它會匹配<code>abe</code>和<code>abcde</code>。（譯註：即（cd）必須為0個或1個。若為0，匹配<code>abe</code>。若為1，匹配<code>abcde</code>）</li>
</ul>

<p>路由路徑也可以是<a href="/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions">JavaScript正則表達式</a>。例如，下面的路由路徑將與鯰魚<code>catfish </code>和角鯊魚<code>dogfish</code>相匹配，但不包括鯰魚<code>catflap</code>、鯰魚頭<code>catfishhead</code>等。請注意，正則表達式的路徑使用正則表達式語法（它不像以前那樣，是帶引號的字符串）。</p>

<pre class="brush: js"><code>app.get(/.*fish$/, function (req, res) {
  ...
})</code></pre>

<div class="note">
<p><strong>備註：</strong> LocalLibrary的大部分路由，都只使用字符串，而不是字符串模式和正則表達式。我們還將使用下一節中討論的路由參數。</p>
</div>

<h3 id="路由參數">路由參數</h3>

<p>路徑參數是命名的URL段，用於捕獲在URL中的位置指定的值。命名段以冒號為前綴，然後是名稱（例如。捕獲的值，使用參數名稱作為鍵，存在對像中（例如）。<code>/<strong>:</strong>your_parameter_name/</code><code>req.params</code><code>req.params.your_parameter_name</code></p>

<p>例如，考慮一個編碼的URL，其中包含有關用戶和書本的信息：<code>http://localhost:3000/users/34/books/8989</code>。我們可以使用<code>userId</code>和<code>bookId</code>路徑參數，提取如下所示的信息：</p>

<pre><code>app.get('/users/:userId/books/:bookId', function (req, res) {
  // Access userId via: req.params.userId
  // Access bookId via: req.params.bookId
  res.send(req.params);
})
</code></pre>

<p>路由參數的名稱，必須由“單詞字符”（AZ，az，0-9和_）組成。</p>

<div class="note">
<p><strong>備註：</strong> URL <em>/book/create</em>將與<code>/book/:bookId</code> 之類的路由匹配（它將提取要創建' <code>create</code>'的“bookId”值）。將使用與傳入URL匹配的第一個路由，因此，如果要單獨處理<code>/book/create</code>URL，則必須在<code>/book/:bookId</code>路由之前，先定義其路由處理程序。</p>
</div>

<p>這就是您開始使用路由所需的全部內容-如果需要，您可以在Express文檔中找到更多信息：<a href="http://expressjs.com/en/starter/basic-routing.html">基本路由</a>和<a href="http://expressjs.com/en/guide/routing.html">路由指南</a>。以下部分顯示了我們如何為LocalLibrary設置路由和控制器。</p>

<h2 id="本地圖書館需要的路由">本地圖書館需要的路由</h2>

<p>下面列出了我們最終需要用於頁面的URL，其中object被替換為每個模型的名稱（book，bookinstance，genre，author），objects是對象的複數，id是默認情況下，為每個Mongoose模型實例指定的唯一實例字段（<code>_id</code>）。</p>

<ul>
 <li><code>catalog/</code> — 主頁/索引頁面。</li>
 <li><code>catalog/&lt;objects&gt;/</code>—所有書本，書本實例，種類或作者的列表（例如/ <code>catalog/books/</code>, / <code>catalog/genres/</code>等）</li>
 <li><code>catalog/&lt;object&gt;/<em>&lt;id&gt;</em></code>—具有給定<code><em>_id</em></code>字段值的特定書本，書本實例，種類或作者的詳細信息頁面（例如<code>/catalog/book/584493c1f4887f06c0e67d37</code>）。</li>
 <li><code>catalog/&lt;object&gt;/create</code>—用於創建新的書本，書本實例，種類或作者的表單（例如<code>/catalog/book/create</code>）。</li>
 <li><code>catalog/&lt;object&gt;/<em>&lt;id&gt;</em>/update</code>—使用給定的<code><em>_id</em></code>字段值更新特定書本，書本實例，種類或作者的表單（例如<code>/catalog/book/584493c1f4887f06c0e67d37/update</code>）。</li>
 <li><code>catalog/&lt;object&gt;/<em>&lt;id&gt;</em>/delete</code>—刪除具有給定<code><em>_id</em></code>字段值的特定書本，書本實例，種類或作者的表單（例如<code>/catalog/book/584493c1f4887f06c0e67d37/delete</code>）。</li>
</ul>

<p>第一個主頁和列表頁面，不編碼任何其他信息。雖然返回的結果，將取決於模型類型和數據庫中的內容，但為了獲取信息所運行的查詢，將始終相同（類似地，用於創建對象的代碼將始終類似）。相反的，其他URL用於處理特定文檔/模型實例—這些將項目的標識編碼在URL中（如上面的<code><em>&lt;id&gt;</em></code>）。</p>

<p>我們將使用路徑參數，來提取編碼信息，並將其傳遞給路由處理程序（在稍後的文章中，我們將使用它來動態確定從數據庫獲取的信息）。通過對我們的URL中的信息進行編碼，我們只需要一個路由，用於特定類型的每個資源（例如，一個路由來處理每個書本項目的顯示）。</p>

<div class="note">
<p><strong>備註：</strong> Express允許您以任何方式構建URL -您可以在URL正文中編碼信息，就像上面一樣，或使用URL <code>GET</code>參數（例如<code>/book/?id=6</code>）。無論您使用哪種方法，URL都應保持乾淨，合理且可讀（請在此處查看<a href="https://www.w3.org/Provider/Style/URI">W3C建議</a>）。</p>
</div>

<p>接下來，我們為所有上述URL，創建路由處理程序回調函數和路由代碼。</p>

<h2 id="創建路由-handler回調函式">創建路由-handler回調函式</h2>

<p>在我們定義路由之前，我們將首先創建它們將調用的所有虛擬/骨架回調函數。回調將存在Books，BookInstances，Genres 和Authors 的單獨“控制器” 模塊中（您可以使用任何文件/模塊結構，但這似乎是該項目的適當粒度）。</p>

<p>首先在項目根目錄（<strong>/controllers</strong>）中，為我們的控制器創建一個文件夾，然後創建單獨的控制器文件/模塊，來處理每個模型：</p>

<pre>/express-locallibrary-tutorial  //the project root
  <strong>/controllers</strong>
    <strong>authorController.js</strong>
    <strong>bookController.js</strong>
    <strong>bookinstanceController.js</strong>
    <strong>genreController.js</strong></pre>

<h3 id="作者控制器">作者控制器</h3>

<p>打開<strong>/controllers/authorController.js</strong>文件，並複制以下代碼：</p>

<pre class="brush: js">var Author = require('../models/author');

// Display list of all Authors.
exports.author_list = function(req, res) {
    res.send('NOT IMPLEMENTED: Author list');
};

// Display detail page for a specific Author.
exports.author_detail = function(req, res) {
    res.send('NOT IMPLEMENTED: Author detail: ' + req.params.id);
};

// Display Author create form on GET.
exports.author_create_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Author create GET');
};

// Handle Author create on POST.
exports.author_create_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Author create POST');
};

// Display Author delete form on GET.
exports.author_delete_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Author delete GET');
};

// Handle Author delete on POST.
exports.author_delete_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Author delete POST');
};

// Display Author update form on GET.
exports.author_update_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Author update GET');
};

// Handle Author update on POST.
exports.author_update_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Author update POST');
};
</pre>

<p>該模塊首先導入我們稍後將使用的模型，來訪問和更新我們的數據。然後它為我們希望處理的每個URL，導出函數（創建，更新和刪除操作使用表單，因此還有其他方法，來處理表單發布請求- 我們將在稍後的“表單文章” 中討論這些方法） 。</p>

<p>所有函數都具有Express中間件函數的標準形式，如果方法沒有完成請求週期，則會調用請求，響應和<code>next</code>下一個函數的參數（在所有這些情況下，它都會執行！）。這些方法只返回一個字符串，表明尚未創建關聯的頁面。如果期望控制器函數接收路徑參數，則在消息字符串中，輸出這些參數（參見上面的<code>req.params.id</code>）。</p>

<h4 id="書本實例控制器">書本實例控制器</h4>

<p>打開<strong>/controllers/bookinstanceController.js</strong>文件，並將其複製到以下代碼中（它遵循與<code>Author</code>控制器模塊相同的模式）：</p>

<pre class="brush: js">var BookInstance = require('../models/bookinstance');

// Display list of all BookInstances.
exports.bookinstance_list = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance list');
};

// Display detail page for a specific BookInstance.
exports.bookinstance_detail = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance detail: ' + req.params.id);
};

// Display BookInstance create form on GET.
exports.bookinstance_create_get = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance create GET');
};

// Handle BookInstance create on POST.
exports.bookinstance_create_post = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance create POST');
};

// Display BookInstance delete form on GET.
exports.bookinstance_delete_get = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance delete GET');
};

// Handle BookInstance delete on POST.
exports.bookinstance_delete_post = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance delete POST');
};

// Display BookInstance update form on GET.
exports.bookinstance_update_get = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance update GET');
};

// Handle bookinstance update on POST.
exports.bookinstance_update_post = function(req, res) {
    res.send('NOT IMPLEMENTED: BookInstance update POST');
};
</pre>

<h4 id="種類控制器">種類控制器</h4>

<p>打開<strong>/controllers/genreController.js</strong>文件，並複制以下文本（這與<code>Author</code>和<code>BookInstance</code>文件的模式相同）：</p>

<pre class="brush: js">var Genre = require('../models/genre');

// Display list of all Genre.
exports.genre_list = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre list');
};

// Display detail page for a specific Genre.
exports.genre_detail = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre detail: ' + req.params.id);
};

// Display Genre create form on GET.
exports.genre_create_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre create GET');
};

// Handle Genre create on POST.
exports.genre_create_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre create POST');
};

// Display Genre delete form on GET.
exports.genre_delete_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre delete GET');
};

// Handle Genre delete on POST.
exports.genre_delete_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre delete POST');
};

// Display Genre update form on GET.
exports.genre_update_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre update GET');
};

// Handle Genre update on POST.
exports.genre_update_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Genre update POST');
};
</pre>

<h4 id="書本控制器">書本控制器</h4>

<p>打開<strong>/controllers/bookController.js</strong>文件，並複制以下代碼。它遵循與其他控制器模塊相同的模式，但另外還有一個<code>index()</code>函數，用於顯示站點歡迎頁面：</p>

<pre class="brush: js">var Book = require('../models/book');

<strong>exports.index = function(req, res) {
    res.send('NOT IMPLEMENTED: Site Home Page');
};</strong>

// Display list of all books.
exports.book_list = function(req, res) {
    res.send('NOT IMPLEMENTED: Book list');
};

// Display detail page for a specific book.
exports.book_detail = function(req, res) {
    res.send('NOT IMPLEMENTED: Book detail: ' + req.params.id);
};

// Display book create form on GET.
exports.book_create_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Book create GET');
};

// Handle book create on POST.
exports.book_create_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Book create POST');
};

// Display book delete form on GET.
exports.book_delete_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Book delete GET');
};

// Handle book delete on POST.
exports.book_delete_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Book delete POST');
};

// Display book update form on GET.
exports.book_update_get = function(req, res) {
    res.send('NOT IMPLEMENTED: Book update GET');
};

// Handle book update on POST.
exports.book_update_post = function(req, res) {
    res.send('NOT IMPLEMENTED: Book update POST');
};
</pre>

<h2 id="創建目錄路由模組">創建目錄路由模組</h2>

<p>接下來，我們為LocalLibrary 網站，創建所需全部URL 的路由，這將調用我們在上一節中定義的控制器功能。</p>

<p>骨架網站已經有一個<strong>./routes</strong>文件夾，其中包含索引和用戶的路由。在此文件夾中，創建另一個路徑文件— <strong>catalog.js</strong> —如下圖所示。</p>

<pre>/express-locallibrary-tutorial //the project root
  /routes
    index.js
    users.js
    <strong>catalog.js</strong></pre>

<p>打開<strong>/routes/ </strong><strong>catalog.js</strong>，複製下面的代碼：</p>

<pre class="brush: js"><strong>var express = require('express');
var router = express.Router();
</strong>
// Require controller modules.
var book_controller = require('../controllers/bookController');
var author_controller = require('../controllers/authorController');
var genre_controller = require('../controllers/genreController');
var book_instance_controller = require('../controllers/bookinstanceController');

/// BOOK ROUTES ///

// GET catalog home page.
router.get('/', book_controller.index);

// GET request for creating a Book. NOTE This must come before routes that display Book (uses id).
router.get('/book/create', book_controller.book_create_get);

// POST request for creating Book.
router.post('/book/create', book_controller.book_create_post);

// GET request to delete Book.
router.get('/book/:id/delete', book_controller.book_delete_get);

// POST request to delete Book.
router.post('/book/:id/delete', book_controller.book_delete_post);

// GET request to update Book.
router.get('/book/:id/update', book_controller.book_update_get);

// POST request to update Book.
router.post('/book/:id/update', book_controller.book_update_post);

// GET request for one Book.
router.get('/book/:id', book_controller.book_detail);

// GET request for list of all Book items.
router.get('/books', book_controller.book_list);

/// AUTHOR ROUTES ///

// GET request for creating Author. NOTE This must come before route for id (i.e. display author).
router.get('/author/create', author_controller.author_create_get);

// POST request for creating Author.
router.post('/author/create', author_controller.author_create_post);

// GET request to delete Author.
router.get('/author/:id/delete', author_controller.author_delete_get);

// POST request to delete Author.
router.post('/author/:id/delete', author_controller.author_delete_post);

// GET request to update Author.
router.get('/author/:id/update', author_controller.author_update_get);

// POST request to update Author.
router.post('/author/:id/update', author_controller.author_update_post);

// GET request for one Author.
router.get('/author/:id', author_controller.author_detail);

// GET request for list of all Authors.
router.get('/authors', author_controller.author_list);

/// GENRE ROUTES ///

// GET request for creating a Genre. NOTE This must come before route that displays Genre (uses id).
router.get('/genre/create', genre_controller.genre_create_get);

//POST request for creating Genre.
router.post('/genre/create', genre_controller.genre_create_post);

// GET request to delete Genre.
router.get('/genre/:id/delete', genre_controller.genre_delete_get);

// POST request to delete Genre.
router.post('/genre/:id/delete', genre_controller.genre_delete_post);

// GET request to update Genre.
router.get('/genre/:id/update', genre_controller.genre_update_get);

// POST request to update Genre.
router.post('/genre/:id/update', genre_controller.genre_update_post);

// GET request for one Genre.
router.get('/genre/:id', genre_controller.genre_detail);

// GET request for list of all Genre.
router.get('/genres', genre_controller.genre_list);

/// BOOKINSTANCE ROUTES ///

// GET request for creating a BookInstance. NOTE This must come before route that displays BookInstance (uses id).
router.get('/bookinstance/create', book_instance_controller.bookinstance_create_get);

// POST request for creating BookInstance.
router.post('/bookinstance/create', book_instance_controller.bookinstance_create_post);

// GET request to delete BookInstance.
router.get('/bookinstance/:id/delete', book_instance_controller.bookinstance_delete_get);

// POST request to delete BookInstance.
router.post('/bookinstance/:id/delete', book_instance_controller.bookinstance_delete_post);

// GET request to update BookInstance.
router.get('/bookinstance/:id/update', book_instance_controller.bookinstance_update_get);

// POST request to update BookInstance.
router.post('/bookinstance/:id/update', book_instance_controller.bookinstance_update_post);

// GET request for one BookInstance.
router.get('/bookinstance/:id', book_instance_controller.bookinstance_detail);

// GET request for list of all BookInstance.
router.get('/bookinstances', book_instance_controller.bookinstance_list);

<strong>module.exports = router;</strong>
</pre>

<p>該模塊導入Express，然後使用它來創建一個<code>Router</code>對象。路由都在路由器上設置完成，然後導出。</p>

<p>路由是使用路由器對像上的<code>.get()</code>或<code>.post()</code>方法定義的。所有路徑都是使用字符串定義的（我們不使用字符串模式或正則表達式）。作用於某些特定資源（如書籍）的路由，則使用路徑參數從URL中獲取對象標識id。</p>

<p>處理程序函數，都是從我們在上一節中，創建的控制器模塊導入的。</p>

<h3 id="更新_index_路由模組">更新 index 路由模組</h3>

<p>我們已經設置了所有新路由，但我們仍然有一個到原始頁面的路由。讓我們將其重定向，到我們在路徑'/ catalog' 創建的新索引頁面。</p>

<p>打開<strong>/routes/index.js</strong>並使用下面的函數，替換現有路由。</p>

<pre class="brush: js">// GET home page.
router.get('/', function(req, res) {
  res.redirect('/catalog');
});</pre>

<div class="note">
<p><strong>備註：</strong> 這是我們第一次使用<a href="https://expressjs.com/en/4x/api.html#res.redirect">redirect()</a>響應方法。這會重定向到指定的頁面，默認情況下會發送HTTP狀態代碼“302 Found”。您可以根據需要，更改返回的狀態代碼，並提供絕對路徑或相對路徑。</p>
</div>

<h3 id="更新app.js">更新app.js</h3>

<p>最後一步，是將路由，添加到中間件鏈。我們在<code>app.js</code>這樣做。</p>

<p>打開<strong>app.js</strong>，並要求其他路由下方的目錄路由（添加下面顯示的第三行，在其他兩個路由下面）：</p>

<pre class="brush: js">var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');
<strong>var catalogRouter = require('./routes/catalog');  //Import routes for "catalog" area of site</strong></pre>

<p>接下來，將目錄路由，添加到其他路由下面的中間件堆棧（添加下面顯示的第三行，在其他兩行下面）：</p>

<pre class="brush: js">app.use('/', indexRouter);
app.use('/users', usersRouter);
<strong>app.use('/catalog', catalogRouter);  // Add catalog routes to middleware chain.</strong></pre>

<div class="note">
<p><strong>備註：</strong> 我們已在路徑<code>'/catalog'</code>中添加了目錄模塊。它預先添加到目錄模塊中定義的所有路徑。例如，要訪問書本列表，URL將為：<code>/catalog/books/</code>。</p>
</div>

<p>就是這樣。現在應該為我們最終在LocalLibrary 網站上支持的所有URL，啟用路由和框架功能。</p>

<h3 id="測試路由">測試路由</h3>

<p>要測試路由，首先使用您通常的方法啟動網站</p>

<ul>
 <li>預設方法
  <pre class="brush: bash"><code>// Windows
SET DEBUG=express-locallibrary-tutorial:* &amp; npm start

// macOS or Linux
DEBUG=express-locallibrary-tutorial:* npm start</code>
</pre>
 </li>
 <li>如果您先前設置了<a href="/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website">nodemon</a> ，則可以使用：
  <pre><code>// Windows
SET DEBUG=express-locallibrary-tutorial:* &amp; npm <strong>run devstart</strong>

// macOS or Linux
</code>DEBUG=express-locallibrary-tutorial:* npm <strong>run devstart</strong>
</pre>
 </li>
</ul>

<p>然後瀏覽一些上面的LocalLibrary URL，並驗證您沒有收到錯誤頁面（HTTP 404）。為方便起見，下面列出了一小組網址：</p>

<ul>
 <li><a href="http://localhost:3000/">http://localhost:3000/</a></li>
 <li><a href="http://localhost:3000/catalog">http://localhost:3000/catalog</a></li>
 <li><a href="http://localhost:3000/catalog/books">http://localhost:3000/catalog/books</a></li>
 <li><a href="http://localhost:3000/catalog/bookinstances/">http://localhost:3000/catalog/bookinstances/</a></li>
 <li><a href="http://localhost:3000/catalog/authors/">http://localhost:3000/catalog/authors/</a></li>
 <li><a href="http://localhost:3000/catalog/genres/">http://localhost:3000/catalog/genres/</a></li>
 <li><a href="http://localhost:3000/catalog/book/5846437593935e2f8c2aa226/">http://localhost:3000/catalog/book/5846437593935e2f8c2aa226</a></li>
 <li><a href="http://localhost:3000/catalog/book/create">http://localhost:3000/catalog/book/create</a></li>
</ul>

<h2 id="總結">總結</h2>

<p>我們現在為網站創建了所有的路由，在稍後的教程，我們可以將實作完成的代碼，填入到空殼控制器函式。以這樣的方式，我們學到了許多關於Express 路由的基本信息，以及一些組織路由和控制器的方式。</p>

<p>下一篇文章，我們將使用視圖(模板) 和存在模型裡的信息，為網站創建一個合適的歡迎頁面。</p>

<h2 id="參閱">參閱</h2>

<ul>
 <li><a href="http://expressjs.com/en/starter/basic-routing.html">Basic routing</a> (Express docs)</li>
 <li><a href="http://expressjs.com/en/guide/routing.html">Routing guide</a> (Express docs)</li>
</ul>

<p>{{PreviousMenuNext("Learn/Server-side/Express_Nodejs/mongoose", "Learn/Server-side/Express_Nodejs/Displaying_data", "Learn/Server-side/Express_Nodejs")}}</p>

<p> </p>

<h2 id="本教程連結">本教程連結</h2>

<ul>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Introduction">Express/Node introduction</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment">Setting up a Node (Express) development environment</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Tutorial_local_library_website">Express Tutorial: The Local Library website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/skeleton_website">Express Tutorial Part 2: Creating a skeleton website</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose">Express Tutorial Part 3: Using a Database (with Mongoose)</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/routes">Express Tutorial Part 4: Routes and controllers</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/Displaying_data">Express Tutorial Part 5: Displaying library data</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/forms">Express Tutorial Part 6: Working with forms</a></li>
 <li><a href="/en-US/docs/Learn/Server-side/Express_Nodejs/deployment">Express Tutorial Part 7: Deploying to production</a></li>
</ul>

<p> </p>