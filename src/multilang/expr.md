# Expression and Statement

表達式 (Expression) 和陳述式 (Statement) 的差別在於，
前者為一種值 (value)，後者不代表任何運行結果，範例如下：

```c
a = sin(pi);
```

先不管是什麼程式語言，上面的意思假設是「用 pi 參數呼叫 sin 函式，將結果存入 a 名稱中」，
那 `pi`、`sin(pi)` 是一種表達式，而 `a = sin(pi)` 是一種陳述式。

單純的陳述式例如模組導入：

```python
from sys import exit
```

然而如 C/C++ 的指派 (assignment) 式有表達式的功能，指派的同時代表右值。

```c
if (i = true)
    always_go();
```

原本 Python 的指派式並非表達式，但是 Python 3.8 新增的 assignment expression ([PEP 572](https://www.python.org/dev/peps/pep-0572/))
讓指派也有代表右值的功能。

```python
if i := True:
    always_go()
```

另外 C/C++ 的逗號運算子 (comma operator) 代表最後項的值。
不過這個運算子優先權太低，導致必須用括弧提昇優先權，酷似函式呼叫容易搞混，因此不常用。

```c
int will_be_10 = (first(), second(), 10);
```

類似的特性被 Rust 引用，任何 code block 都是表達式（代表末尾值，加分號 `;` 成為陳述式）；反而使用 `return` 關鍵字為陳述式，必須加分號。

```rust
/// code block 可以不寫 return
fn my_function(condition: bool) -> u8 {
    if condition {
        // Statement
        return 10;
    } else {
        // Expression
        20
    }
}

let a = if {
    let b = true;
    b
} {
    10
} else {
    20
};
```

JavaScript 的函式也是很經典的例子，總共有三種函式寫法：

```js
/// 這邊只是示範，const 變數不可以重複指派
// 函式陳述式（不用結尾分號）
function f() {}
// 函式表達式
const f = function () {};
// 無 this 的箭號函式 (arrow function)
const f = () => {};
// 異步函式的版本
async function f() {}
const f = async function () {};
const f = async () => {};
// 無指派，執行後回收函式本體
(function () {})();
```
