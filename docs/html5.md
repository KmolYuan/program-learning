# HTML5 & CSS & Markdown

介紹一種廣泛使用的結構性語言：SGML (Standard Generalized Markup Language)，也是 HTML 和 XML 語言的基礎。

由於 SGML 本身十分複雜不易理解，出現了較嚴謹的子集版本 XML (Extensible Markup Language) 方便使用：

```xml
<!-- 註解 -->
<tag attribute1="attr1 value" attribute2="attr2 value">My <tag>text</tag>.</tag>
```

相較於 JSON 或 YAML 的語法，XML 顯然較難閱讀，但是在一行內可以夾帶較多資訊，而不需要更多節點。
除了 text 屬性外，XML 的語法會忽視不可視字元 (invisible characters) 方便壓縮大小（除非是分隔必要）。
特別的是，text 屬性會忽略頭尾的不可視字元，而且可以包含子節點。
SGML 主要攜帶的是 text 屬性，也就是文本。其延伸的結構語言主要都是為了呈現文本內容，如網路協定或是靜態文章。

!!!節點
  在結構語言中稱為節點 (node)，在 SGML 中稱為元素 (element)，不過為明白講解，還是稱其為節點。

網頁所使用的就是 HTML (HyperText Markup Language)，是使用特定 tag 標記特定用途的區域，由網頁瀏覽器渲染產生網頁靜態外觀的。
至於動態外觀呢？就是由瀏覽器內的腳本程式語言來修改 HTML 的節點，重新局部渲染所達成的，目前是由 JavaScript 獨挑大樑。

本章先不介紹 JavaScript，而是注重於靜態網頁。

## HTML Basic Structure

基本的 HTML 架構為：

```html
<!doctype html>
<html>
<head>...</head>
<body>...</body>
</html>
```

其中 head 包含頁面資訊，如標題、圖示、編碼等等資訊。
而 body 則是頁面內容，藉由特定節點排版。
第一行的 `<!doctype html>` 標示文件內容為 HTML，以提醒其他 XML 解譯器，此標示不分大小寫。

body 包含 h1 到 h6 的標題、p 段落、a 超連結、span 內聯、img 圖片、pre 預格式化、code 程式碼、li 條列等區塊；
也可以包含 div 布局容器，甚至是 script 在載入時執行 JavaScript。
更多可以參考 <https://www.w3schools.com/tags/ref_byfunc.asp>。

為了能夠快速檢索，每個 body 裡的節點可以設置 id 與 class 兩種屬性，如下：

```html
<p id="p1" class="myclass"></p>
<p id="p2" class="myclass"></p>
```

差別在於，每個節點的 id 是唯一的，且不可重複定義相同的 id，檢索速度也較快；
而 class 則可以簡單地用名稱從複雜的結構中篩選出來。
實作上則因解譯器不同而異。

對於瀏覽器，`http://mypage#p1` 可以支援直接將顯示區 (view part) 直接捲動到該 id 節點的位置。

## CSS

單純 HTML 只會直接將各區塊「排列」出來，若要調整大小、排列順序、顏色、字型等外觀，
就要依賴階層樣式表 (Cascading Style Sheets, CSS) 的設定。

在 body 裡的每種子節點都有 style 屬性，可以設定單獨的節點（包含 body 本身）。
而 head 裡的 style 節點可以設定多個節點的外觀。

單獨的樣式設定如下：

```html
<p style="color: red">Red text</p>
```

而 style 節點中則是正規的 CSS 語法：

```css
/* 註解 */
p {
  color: red;
}
```

上面的語法可以將所有 p 節點的文字顏色都變成紅色。
跟 HTML 一樣，CSS 會忽略不可視字元，後面的定義會覆蓋前面的定義。

而 id 和 class 屬性也可以用來篩選節點，`.` 用來指定 class，`#` 用來指定 id。

```css
.myclass {
  color: red;
}
#p1 {
  color: blue;
}
/* 特定節點的 class */
p.myclass {
  color: yellow;
}
/* 多個節點的定義 */
#p1, h2 {
  color: green;
}
```

要注意的是，HTML 和 CSS 會有相容性問題，和 JavaScript 一樣，較舊的瀏覽器可能無法支援某些排版方式。

## Markdown

然而 HTML 諸多功能有時候反而會讓純文章寫作有點麻煩，因此有了 Markdown 的產生，並且能夠支援內聯 HTML 語法。
基本上，就是用簡單的標記代表 HTML 語法，沒有支援的就直接用 HTML 補上。
詳細的語法可以參考 <https://daringfireball.net/projects/markdown/syntax>。

實際應用上，可以用編譯的方式從 Markdown 轉為 HTML，而使用靜態轉換或 JavaScript 在載入時轉換都可以達成。
