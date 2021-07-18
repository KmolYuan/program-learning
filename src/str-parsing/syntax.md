# Syntax

本篇將講述常見程式語言的解構方式，以常見的程式語言作為範例。建議先閱讀 [Regular Expression](re.md)。

閱讀此篇有助於在不同語言之間切換，並有助於理解新的語言；無論為結構語言還是程式語言。

## Lark Parser

Python 套件 [lark-parser/lark](https://github.com/lark-parser/lark) 可以自訂程式語言的規則並解析它們。

Lark 基於 EBNF，並使用 Earley Parser，本篇將解說電腦如何閱讀「語言」。

基本元素：

```js
"A" "ABC" "(" ")"  // 字串與符號
"ABC"i  // 不分大小寫的字串
/regular_expression/flag  // Regular Expression
"a".."z" "1".."9"  // 範圍
expression ~ n  // 前一個子句必須出現 n 次
expression ~ n..m  // 前一個子句必須出現 n 到 m 次
expression?  // 前一個子句可以不存在或出現一次
expression*  // 前一個子句可以不存在，或可以無限重複
expression+  // 前一個子句至少出現一次，並可以無限重複
(expression expression ...)  // 提升優先權
[expression expression ...]  // 可選，同於 (expression expression ...)?
```

定義句：

```js
子句一: "1".."9"
子句二: 子句一 "+" 子句一
```

上面的 `子句二` 可以表示 `1 + 1`。

基本上，為了排版，電腦使用的語言會忽略空白：

```js
%ignore /\s+/
```

上面的空白用 RE 表示，意味著一個或重複的不可視字元（包含 Tab）。
忽略後，除非顯式表示，否則解析器不會計算。
例如：`1 + 1` 和 `1+1` 是等效的；`A (B) C` 和 `A(B)C` 是等效的。
但是文字通常有連續性，除非用符號分離，否則一定要至少隔一個空白。

而文字也是定義的一部份，因此當變數名稱訂為：

```js
variable_character: "a".."z" | "A".."Z" | "_"
variable: variable_character+
```

符號 `_` 也會被當成是變數可用的字元，並保持連續性。
一些程式語言也許有例外，如 JavaScript 允許 `$` 作為變數名稱，而 Matlab 不允許 `_` 作為變數的第一個字元。

子句的組成會影響解析的順序，進而影響程式中的優先權 (Priority)。

```js
digit: "0".."9"
int: digit+
plus: int "+" int
strong: "(" (int | plus_statement) ")"
base: int | strong | plus
plus_statement: base ("+" base)+
```

如上所示，`plus_statement` 可以表示 `(1 + 20) + 7`、`(5 + 6)`、`4 + (6 + 9)`、`2 + 777` 等語句。

而以下文法：

```js
digit: "0".."9"
int: digit+
assignment: plus_statement "=" int
```

對於 `2 + 3 = 5` 會先解析 `2 + 3`，而非 `3 = 5`。

## Commonly Used Syntax

可簡單歸類程式語言中的語法構成。

其中 `op1`, `op2`, ... 為使用的符號，可為任何字元或字串。

以下將舉例各式程式語言的部分文法：

### Comment

```js
comment: op1 /[^\n]*/
multiline_comment: op1 /[\s\S]*/ op2
```

註解是程式語言中很重要的一部份，因為他們不會影響執行內容。
不過這些說明內容可以被提取出來做為說明文件等用途。

+ C / C++ / Java / JavaScript / C# / ...

    ```c
    // 單行註解
    /* 區段註解 */
    ```

+ Python

    ```python
    # 單行註解
    """區段註解"""  # 其實是多行字串
    ```

+ Matlab

    ```matlab
    % 單行註解
    %% 段落註解，以下的區塊可以被折疊。
    ```

+ Lua

    ```lua
    -- 單行註解
    --[[
    區段註解
    --]]
    ```

### Line Break

某些語言是會忽略換行記號的，因此可以併成一行也不會影響解析，有助於在只有一行的情況下輸入，如 Bash 中。

```js
%ignore /\r?\n/+
```

要注意有些程式碼並不是結尾有 `;` 註記就是忽略換行記號，
如 Matlab 只是為了隱藏輸出訊息；Lua 沒有結尾記號但是會忽略換行。
而像 JavaScript 會在換行處自動檢查並補上，但是官方仍建議開發者必須打上。
至於 Python 則是加上 `;` 可以併做一行，可是無法加上縮排。

忽略換行記號的語言如 C / C++ 可以這樣寫：

```c
a = (1 + 2)
    * (2 + 3)
    * (3 + 4);
```

可是如 JavaSript 有自動插入的機制，如下會導致強制段行（`return`、`break`、`throw`, `continue` 結尾）：

```js
return
    1 + 3;
```

而在意換行記號的語言因為閱讀需要，會有換行的策略，如：

+ Python：可以在括弧 `()`、`[]`、`{}` 中換行。但是同時因為縮排規定，其中的縮排不能比原始行小。

    ```python
    a = (
        "aaa",
        "bbb",
        "ccc",
    )
    ```

+ Bash：必須使用 `\` 符號在換行結尾。

    ```bash
    rm -f file_a.md \
    file_b.md ${FLAG}
    ```

+ Matlab：可以在 `;` 後換行，除此之外必須使用 `...` 符號在換行結尾，而且其之後的內容會被視為註解。

    ```matlab
    a = (1 + 2)...
        * (2 + 3)...
        * (3 + 4);
    a = [... 這裡要加
        1, 2, 3;
        4, 5, 6;
        7, 8, 9]
    ```

+ Markdown：段落之間必須空一行，否則視為上一行，至於其他如列舉或標題等語法不受限。

    ```markdown
    aaa,
    bbb.

    ccc:
    1. ddd
        with ddd
    1. eee
    1. fff
    ```

### Indentation

縮排通常做為排版使用，不過仍有語言作為程式碼區塊的判定，如 Python。

```js
indentation: " " ~ 4 | /\t/
block: indentation+ code_block
```

若非使用縮排，程式區塊會使用符號或語句作為標記。

+ C / C++ / Java / JavaScript / C# / ...：使用大括弧 `{}`。

    ```c
    if (a < b) { return 0; }
    ```

+ Lua：不需要換行。有對應記號。

    ```lua
    if a < b then return 0 end
    while a < b do a = a + 1 end
    ```

+ Latex：有對應記號。另外括充模組也能夠提供巨集 (Macro) 讓語法比較好看。

    ```latex
    \if \else \fi  % 判斷式
    \loop \if \repeat  % While 迴圈，\repeat 有 \fi 的功能。
    ```

+ Matlab：需要換行。通常搭配 `end` 關鍵字結尾。

    ```matlab
    if a < b
        return
    end
    ```

### Binary Operator

```js
binary: statement op1 statement
```

為兩項運算，如一般的數學式。前後的 `statement` 可以不相同。
在計算的情況，通常可以連續操作如 `a + b + c` 或 `a = b = c`；
但為避免讀錯，如 `a += b` 的縮寫不會允許連續的語句。

+ Common

    ```js
    a + b
    a < b
    a = b
    a += b
    a.b
    ```

+ Python

    ```python
    a and b  # Logical AND
    a in b  # Contain check
    a is b  # Same memory position check
    a := b  # Assignment, but repersent "a" itself (in expression)
    ```

+ C / C++

    ```c
    a && b  // Logical AND
    a->b  // Pointer member
    a::b  // Namespace member
    a, b  // Run "a" and "b" but repersent "b" itself (in expression)
    ```

+ JavaScript

    ```js
    a === b  // Same memory position check
    ```

+ C#

    ```c#
    a ?? b // Repersents "b" if "a" is "null", otherwise repersent "a"
    a?.b  // Repersents "null" if "a" is "null", otherwise repersent the member "b"
    ```

+ R

    ```r
    a <= b  # Assignment
    ```

### Unary Operator

```js
leading_unary: op1 statement
trailing_unary: statement op1
```

分成前置或尾置，為了相容二元運算，通常優先權會最大。
但是因為 C++ 的前車之鑑，發現它們極易破壞可閱讀性，因此新的程式語言就較沒有出現。

+ C / C++

    ```c
    !a  // Logical not
    ~a  // Byte inverse
    *a  // Pointer get value
    &a  // Get position to pointer
    ++a  // Plus 1 to "a", then represent "a"
    a++  // Represents "a", then plus 1 to "a"
    ```

+ Python

    ```python
    not a  # Logical not
    yield a  # Generator get input value (in expression)
    ```

+ C#

    ```c#
    a?  // Type "a" can be "null"
    ```

+ Haskell：函數型程式語言，語法與數學式相仿，函式優先權最高。由 `f x1 x2 ...` 表示函式 `f` 和其參數 `x1`、`x2` 等。

    ```haskell
    f a = sin a * 10  -- Same as "(sin a) * 10", "f" and "sin" is function.
    ```

### Ternary Operator

```js
ternary: statement op1 statement op2 statement
```

較長的句型，由兩個運算子組成。

+ C / C++ / Java / JavaScript / C#

    ```c
    a ? b : c  // If "a" is true, repersents "b", otherwise repersents "c"
    ```

+ Python

    ```python
    b if a else c  # If "a" is true, repersents "b", otherwise repersents "c"
    a for b in c  # An iterator generate "a", extract "b" from "c" during iterating
    a for b in c if d  # Same as above, skip if "d" is "False"
    ```

### Other Statements

至於其他就比較多元了。因此，基礎概念必須先熟悉，如型別標示就會很大程度的影響文法，
但是只要知道該概念會需要哪些參數、選項，自然便會容易理解。

如單純的 while loop 在哪種語言都沒什麼變化：

+ C / C++ / Java / JavaScript / C# / ...

    ```c++
    while (check_statement) {
        run_statements
    }

    // Check after run once
    do {
        run_statements
    } while (check_statement)
    ```

+ Python

    ```python
    while check_statement:
        run_statements
    ```

+ Rust

    ```rust
    while check_statement {
        run_statements
    }
    ```

而物件導向有些卻有可見性，有些會聲明物件本身為 "self"：

+ C++

    ```c++
    public:
    decorator return_type func_name(arg1_type arg1, arg2_type arg2, ...) {
        run_statements
    }
    ```

+ Python

    ```python
    def func_name(self, arg1, arg2, ...):
        """Doc string"""
        run_statements
    ```

最後舉例 Function 的表示百百種：

+ C / C++：有 Closure 語法。

    ```c++
    // Normal Function
    decorator return_type func_name(arg1_type arg1, arg2_type arg2, ...) {
        run_statements
    }

    // Lambda Fuction (Closures, only in C++)
    [refer_mode] (arg1_type arg1, arg2_type arg2, ...) -> return_type {
        run_statements
    }

    // Generic Fuction (only in C++)
    template<template_option>
    decorator return_type func_name(arg1_type arg1, arg2_type arg2, ...) {
        run_statements
    }
    ```

+ Python：沒有 Closure 語法，自動處理。

    ```python
    # Simple Function
    def func_name(arg1, arg2, ...):
        """Doc string"""
        run_statements

    # Lambda Function
    lambda arg1, arg2, ...: return_statement

    # Typed decorated Function
    @decorator
    def func_name(arg1: arg1_type, arg2: arg2_type, ..., *, kward1: kward1_type, ..., **, kward2: kward2_type, ...) -> return_type:
        """Doc string"""
        run_statements
    ```

+ JavaScript：沒有 Closure 語法，自動處理。

    ```js
    // Simple Function
    function func_name(arg1, arg2, ...) {
        run_statements
    }

    // Lambda Function
    function (arg1, arg2, ...) {
        run_statements
    }

    // Lambda Function
    (arg1, arg2, ...) => {
        run_statements
    }

    // Lambda Function
    arg1 => return_statement
    ```

+ Rust：有 Closure 語法。

    ```rust
    // Simple Function
    fn func_name(arg1: arg1_type, arg2: arg2_type, ...) -> return_type {
        run_statements
        return_statement
    }

    // Generic Fuction
    fn func_name<template_option>(arg1: arg1_type, arg2: arg2_type, ...) -> return_type {
        run_statements
        return_statement
    }

    // Closures
    |arg1: arg1_type, arg2: arg1_type, ...| -> return_type {
        run_statements
    }
    ```

+ Go：沒有 Closure 語法，自動處理。

    ```go
    func func_name(arg1 arg1_type, arg2 arg2_type, ...) return_type {
        run_statements
    }
    ```
