# IT Introduces

請查詢關鍵字並瞭解以下分類的技術或概念，並思考問題。

+ 每個關鍵字皆附上較常用的中文翻譯。

+ '=' 符號為相同概念。

## Programing Languages

### Compile

+ Compile 編譯
    + Compiler 編譯器
    + Machine code 機器碼 = Binaries
    + Executable Files 可執行檔
    + Libraries 程式庫
    + Byte Codes
    + Entry Point
+ Interprete 直譯
  + Interpreter 直譯器
+ JIT (Just In Time) 及時編譯

- Compile Time
- Run Time

+ Bootstrapping / Self-hosting

#### Question

1. <details><summary>一種語言的編譯方式只有一種嗎？</summary>否。</details>
1. <details><summary>CPython 的編譯方式為？</summary>先編譯為 Byte Codes，再對 Byte Codes 做直譯。</details>
1. <details><summary>Windows 的命令提示字元 (Command Prompt) 的編譯方式為？</summary>直譯式。</details>
1. <details><summary>使用一款軟體突然當機，是 Compile Time 還是 Run Time 錯誤？</summary>Run Time</details>
1. <details><summary>C++ 是否有 Bootstrapping 的實作？</summary>GCC Compiler</details>

### Type Anotation

+ Strong Typing 強型別
+ Weak Typing 弱型別

#### Question

1. <details><summary>Python 的類型標示為？</summary>Weak Typing</details>
1. <details><summary>除了註解功能，Python 可以表示類型嗎？</summary>可以，參見 PEP 484。</details>
1. <details><summary>一種程式語言的類型標示是否會導致編譯方式不同？</summary>否。會因為實作困難度影響，但是仍可克服。</details>

### Language Concepts

+ Variables 變數 / Literatures / Functions 函數
+ Structures 結構體
+ Control Flow 控制流程
    + Go to
    + While Loop
    + For Loop
    + If Else Condition
    + Switch Case
    + Recursive 遞迴
+ Stack 棧 / Heap 堆
    + Life Cycle 生命週期
    + Variables Visibility 變數可見性
    + Pointer 指標 / Reference 參照 / Reference Counter 參照計數器
    + Garbage Collection 垃圾回收
+ Function Signature 函式簽章
+ Anonymous Functions 匿名函式 / Function Pointer 函式指標
+ Objective Orientation 物件導向
+ Syntactic Sugar 語法糖 / Syntactic Salt 語法鹽
+ Unit Test 單元測試
+ Version Control 版本控制
+ Doc String

- Standard Libraries 標準庫
- Third Party Libraries 第三方程式庫
- Dependencies 相依性

+ Compiler Macros 編譯器巨集
+ Compiler Optimizations 編譯器最佳化

- String Encode 字串編碼
- RE (Regular Expression) 正規表達式
- EBNF (Extended Backus–Naur Form) 擴充巴克斯範式

+ Writing Style Guide 寫作風格指南
+ "Pythonic"

#### Question

1. <details><summary>「宣告 (Declaration)」主要是為了上述哪種概念？</summary>Life Cycle</details>
1. <details><summary>組合語言 (Assembly Language) 中有哪些概念？</summary>Variables, Go to, Pointer</details>
1. <details><summary>可以用組合語言實現更抽象的概念嗎？</summary>可以。</details>
1. <details><summary>Pointer 使用後未清除會造成何種問題？</summary>Memory Leak</details>
1. <details><summary>RE 和 EBNF 皆可定義文字規則，若發生 Miss Match，差別在於？</summary>RE 只會忽略。</details>
1. <details><summary>透過 EBNF，可以實作自己的程式語言，然而 Syntactic Sugar 或 Syntactic Salt 過多分別可能造成何種問題？</summary>Syntactic Sugar 過多會導致不易閱讀且有多種寫法；Syntactic Salt 過多會導致開發效率低下。</details>
1. <details><summary>規定 Writing Style Guide 主要是為了？</summary>易於讓協同者閱讀與方便修改。</details>
1. <details><summary>使用 Unicode 而非其他語系的目的為？</summary>可以使用所有語系的文字。</details>

### Objective Orientation

+ Members 成員 / Methods 方法
+ Property 屬性 = Getter & Setter
+ Inherit 繼承 / Virtual Inheritance 虛繼承
+ Interface 介面 = Abstract Class 抽象類別
+ Generic Type 泛型

#### Question

1. <details><summary>定義 Members 的可見性是否對編譯器最佳化有幫助？</summary>否，屬於編譯檢查機制。</details>
1. <details><summary>虛繼承除了放棄多重繼承功能，還可以怎麼解決？</summary>建立抽象方法 (Abstract Methods / Virtual Methods)。</details>
1. <details><summary>Generic Type 的概念是否只有在 Strong Typing？</summary>否，Weak Typing 會在 Compile Time 推導類別；或 Run Time 檢查類別。</details>

## Data Structure

+ Chain 鍊
  + Array 陣列
  + Linked List
  + Record
+ Tree 樹
  + Binary Search Tree 二元搜尋樹
  + Red–black Tree 紅黑樹
  + Map 映射
+ Hashable Value 可雜湊值

#### Question

1. <details><summary>Python 有哪些類型的容器？</summary>Linked List (List), Record (Tuple), Red–black Tree (Set), Mapping (Dictionary)</details>
1. <details><summary>樹狀結構是否有排列順序？</summary>否。</details>
1. <details><summary>樹狀結構如何記錄重複的值？</summary>可以使用 Map 存放計數器。</details>

## Structural Languages

+ CSV
+ XML / HTML
+ CSS / SASS
+ YAML / TOML
+ Markdown
+ Latex / Bibtex
+ STL

#### Question

1. <details><summary>瀏覽器 (Browser) 本身是複合式的軟體，舉例其可以直譯的結構語言和程式語言？</summary>HTML, CSS, Java Script</details>

# Platforms

### File System

+ Root = Super User = Administrator
+ File Mode 檔案權限
+ File Operate 檔案操作
+ Path Separator
+ Shell Script / Bash / Command Prompt
+ Environment Variables 環境變數
+ File Suffix 副檔名
+ Link File 捷徑 / Soft Link 軟連結 / Hard Link 硬連結

#### Question

1. <details><summary>Linux 可執行檔的附檔名通常為何？</summary>沒有副檔名。</details>
1. <details><summary>Windows、Linux、mac OS 的檔案系統，哪兩個最相近？</summary>Linux 和 mac OS。</details>
1. <details><summary>Soft Link 較 Hard Link 的好處有哪些？</summary>不會被誤認成實體檔案、可以跨磁區。</details>
1. <details><summary>若不使用 File Browser，Text-Based File 會因為附檔名而無法開啟嗎？</summary>不會。</details>
