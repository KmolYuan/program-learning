# Functional Programming

**泛函編程 (Functional Programming)** 是一種程式的設計模式。相較於一般的順序控制流程，將指令包裝成**函式 (function)**，可以用來簡化重複的程式碼。函式得名自數學的**函數 (function)**，可以假想程式的計算過程都是「函數」，為區別而在程式中稱之為「函式」，諸如三角函數、費式數列等。函數會有自身輸入的**參數 (argument)**，以及**回傳值 (returned value)**，兩者稱為**簽名 / 簽章 (signature)**。一旦開始計算，便會需要填入參數、取用或捨棄回傳值，這個動作稱為**呼叫 (call)**。

以下使用 Rust 程式語言作為範例，是一個經典的費式數列。

```rust
fn fibonacci(n: u32) -> u32 {
    match n {
        0 => 0,
        1 => 1,
        _ => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

println!("{}", fibonacci(5));
```

以上函式可以計算出第 n 項的費式數列，只需要寫下 `fibonacci(n)` 即可。其定義為，除了 0 或 1，其數值是前兩項數值的和。在 Rust 的語法中，使用 `fn` 關鍵字開頭定義一個函式的簽名，有一個參數 `n`，類型為 `u32`（32 位元正整數），回傳值也是 `u32`，可以簡記為 `fn(u32) -> u32`。在 Rust 中，程式碼最後會用分號 `;` 標記，若最後一行沒有分號標記，則被認為是回傳值。這個規則可以套用到除了 `while` 或 `for` 迴圈以外的任何流程控制語法，不過 `loop` 迴圈必須使用 `break` 關鍵字回傳。

以上的程式相當直觀，因為數值是由自身的運算所構成，只需要加法即可。不過在泛函編程中，加法運算子 `+` 也是函式，只是可以數學化表示。

```rust
println!("{}", 2 + 2);
// 導入隱藏的加法運算子
use std::ops::Add;
println!("{}", 2.add(2));
```

會呼叫自身函式的行為稱為**遞迴 (recursion)**。前面介紹過記憶體管理的部份，由於函式都會擁有暫時的執行區 stack，直到函式結束才會刪除其中的資料，因此遞迴其實比較浪費資源，也較難估測。當然，遞迴是可以展開成迴圈的，如以下範例：

```rust
fn fibonacci(n: u32) -> u32 {
    if n < 2 {
        return n;
    }
    let mut sum = 0;
    let mut last = 0;
    let mut curr = 1;
    for _ in 1..n {
        sum = last + curr;
        last = curr;
        curr = sum;
    }
    sum
}

println!("{}", fibonacci(5));
```

## Callable

再更進階的應用中，**可呼叫 (callable)** 類型被指稱作為函式的類型，由於可以被標記，函式也可以當作是參數或回傳值，記作如下：

```rust
fn run_me(s: &str) {
    println!("Hello {}!", s);
}

fn give_world(f: fn(&str)) {
    f("World");
}

give_world(run_me);
```

在 Rust 中，靜態函式可被記作 `fn(參數類型) -> 回傳類型`，不回傳則會回傳單元 (unit) 類型 `()`，可記作 `-> ()` 或直接省略。上面的例子中可以將靜態函式傳送給另一個函式呼叫。

## Closure

**閉包**是一種動態產生的函式，意旨在於，執行的時候想產生需要客製化的程式碼，例如有很多想操作的參數，但是不方便一起作為參數輸入。閉包可藉由將外部變數傳入的方式取得其值或是參照，這個行為稱為**捕獲 (capture)**，來動態執行更多內容。

捕獲變數是有條件的，因為閉包產生後，不會馬上被呼叫。被呼叫的次數也是關鍵，如果只需要呼叫一次，大可直接刪除閉包，將捕獲對象歸還，但是閉包若是需要執行多次、改變捕獲對象的值，或甚至作為回傳值，條件都將更苛刻。

在 Rust 中，閉包雖然是可呼叫的，但是和靜態函式不一樣，擁有更多限制，像是捕獲對象的生命週期。大部分的情況下，閉包可以自動判斷條件和類型標示，只需要下列的定義：

+ 預設：捕獲對象的參照，對象的生命週期必須大於閉包。
+ 使用 `move` 關鍵字：指派對象到閉包中，而進行移動的對象會與閉包的生命週期同步。要注意實作 `Copy` trait 的對象會進行複製。
+ 沒有捕獲行為：沒有捕獲行為的閉包行為上可視為普通的靜態函式，不過還是視為閉包。這種閉包可以直接用 `fn` 關鍵字定義成靜態函式。

```rust
let mut a = 10.;
{
    // f1 會借走唯讀所有權
    let f1 = |b| a + b;
    println!("{}", f1(20.));
    // f2 採用複製，複製允許在唯讀所有權借走後
    let f2 = move |b| a + b;
    println!("{}", f2(20.));
}
// 借走修改所有權
let mut f3 = |b| {
    let a = &mut a;
    *a += b;
    *a
};
println!("{}", f3(20.));
```

接收端若不是單純呼叫，而是傳送到其他函式或函式外，則須類型標示，且已確保參照的生命週期同步或大於閉包。

+ [`Fn`](https://doc.rust-lang.org/core/ops/trait.Fn.html)：最普通的閉包，只對參照讀取。
+ [`FnMut`](https://doc.rust-lang.org/core/ops/trait.FnMut.html)：會修改參照的閉包，自身必須標示為可修改。
+ [`FnOnce`](https://doc.rust-lang.org/core/ops/trait.FnOnce.html)：在呼叫一次後就會刪除自身的閉包，修改和讀取皆可。

由於閉包在 Rust 沒有可描述的類型名稱，因此通常寫成已實作 `Fn` trait 的樣子，如 `impl Fn(u32) -> u32`。以下範例中，藉由對閉包取址，可以不斷呼叫會修改參照的閉包。

```rust
fn closures(f: &mut impl FnMut(f32) -> f32) {
    println!("{}", f(20.));
}

let mut a = 10.;
let mut f1 = |b| {
    let a = &mut a;
    *a += b;
    *a
};
closures(&mut f1);
closures(&mut f1);
```
