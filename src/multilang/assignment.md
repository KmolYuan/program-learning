# Assignment

**指派 (Assignment)** 是程式語言中最基本的一種語法，亦也是一種最關鍵的邏輯問題，為一種**變數 (variable)** 之間的轉移手段。指派通常有幾種行為：**移動 (move)**、**複製 (copy)** 與**參照計數 (reference counted)**。為了方便說明，現在就以三種都具備的 Rust 程式語言來表示。

首先以下是 Rust 中的指派語句：

```rust
// 指派語句
let mut a = 20.;
// 修改也是指派語句
a = 10.;
assert_eq!(a, 10.);

// pattern matching 也是指派語句
match 10. {
    a => { assert_eq!(a, 10.); }
    _ => {}
}

// if let pattern matching 也是指派語句
if let a = 10. {
    assert_eq!(a, 10.);
}

// while let pattern matching 也是指派語句
let mut iter = [10.].into_iter();
while let Some(&a) = iter.next() {
    assert_eq!(a, 10.);
}

// for-loop 也是指派語句，語意同上
for a in [10.] {
    assert_eq!(a, 10.);
}

// closure 預設是取指標，但是其生命週期必須比原始資料短
let f1 = |b| { assert_eq!(a, b); };
// 使用 move 關鍵字會使用指派語句
let f2 = move |b| { assert_eq!(a, b); };
// 呼叫函式也是指派語句，同於上面的 b = a
f1(10.);
f2(10.);
```

Rust 在預設的情況下選擇使用 move 或 copy 來處理指派問題，再加上必須用關鍵字 `mut` 標示可變變數，因此效能上會有靈活調整的優勢。有趣的是雖然 Rust 是靜態型別的程式語言，但是上面的範例完全不用標明類型，都是自動推導的。

## Move

**移動**算是比較新的概念，而且只發生在語義上。當 `a` 變數移動到 `b` 變數時，可視為資料的完全轉移，因此 `a` 變數就不可使用了。亦即，`a` 的資料的生命週期 (life cycle) 被「延長」到新的變數，直到變數 `b` 被刪除，資料才會被刪除。

編譯器在實作時，其實只要把 `a` 和 `b` 視為相同的記憶體，就可以達成移動的流程。

在 C++ 中，預設都是複製，另外可以使用 `std::move()` 函式來達成變數移動。

## Copy

**複製**的方式固然簡單，但是也意味著造成最大的執行成本：時間和空間都不可避免的浪費掉了，特別是對於唯讀 (read-only) 資料。若是程式中可以標明常量 (constant) 或可變 (mutable) 變數，還可以將常量的複製行為移除。但若是沒有，複製一整個資料可說是非常重的負擔。

在 Rust 中，只有原始類型 (primitive type) 預設是可以複製的，包含布林 (boolean)、整數 (integer)、浮點數 (float)、字元 (character)、單元 (unit)、指標 (pointer)。至於其他類型 struct、enum、union 則必須實作 Clone trait，以達成可複製的條件。

```rust
#[derive(Clone, PartialEq, Debug)]
struct Point {
    x: f64,
    y: f64,
}

let mut p1 = Point {x: 0., y: 0.};
let p2 = p1.clone();
assert_eq!(p1, p2);
p1.x = 10.;
assert_ne!(p1, p2);
```

上面的程式使用派生巨集 (derive macro)，會自動實作複製的行為，但若是其中有欄位 (field) 的類型沒有實作 Clone，就必須手動用實作語法 `impl Clone for Point {...}` 處理。上面其他的 trait 也是相同的道理，`PartialEq` 可以實作「等於」與「不等於」運算子，`Debug` 可以實作運行期間錯誤訊息的格式。達成可複製條件後，就可以手動用 `Clone::clone()` 這個方法來複製。

若要達成跟原始型別一樣，指派時自動複製，則可以實作一個標記用的 Copy trait，這樣編譯器在解析時會自動呼叫 `Clone::clone()`。

```rust
#[derive(Copy, Clone, PartialEq, Debug)]
struct Point {
    x: f64,
    y: f64,
}

let mut p1 = Point {x: 0., y: 0.};
let p2 = p1;
assert_eq!(p1, p2);
p1.x = 10.;
assert_ne!(p1, p2);
```

## Pointer and Reference

在認識參照計數之前，必須先認識**指標 (pointer)**。指標是指向變數的記憶體位置，是用一段整數來當做代號，可以藉由儲存指標來達成在變數之外存取該變數的數值。

然而，指標顯然也是另外一個變數，自然也可以有自己的指標，在語法上較難處理，也很花時間理解。這時候某些程式語言會推出語義上的指標，來參考原本的數據以免去複製的步驟，稱為**參照 (reference)**。

再者，指標顯然有一些致命缺陷，甚至是可被攻擊的目標：

+ 懸掛指標 (hanged pointer)：原始的數值有自己的生命週期，若是在刪除後仍去存取，亦可能取到錯誤的數值。或是創建空指標時沒有指到正確位置就進行讀寫。
+ 記憶體洩漏 (memory leak)：存入數值後忘記刪除，就刪除指標，會造成浪費記憶體空間（因為直到程式執行完才會清除）。
+ 堆棧溢出 (stack overflow)：指標可以索取連續的記憶體陣列，但是如果檢索的長度超出範圍，會得到錯誤數值。

在 Rust 中，指標與參照是相同的，統一稱為**指標 (pointer)**。不能保證生命週期的指標稱為**原始指標 (raw pointer)**，寫作 `*T`；能保證生命週期的稱為**智慧指標 (smart pointer)**，寫作 `&T`，也是最常用的一種。能保證生命週期的行為也稱為**記憶體安全 (memory safe)**。記憶體安全也是 Rust 語言的一大賣點，所以預設不能使用原始指標，除非開啟 **unsafe** 選項。

原始指標操作簡單，但是較「危險」。智慧指標在 Rust 則是採取**所有權 (ownership)** 系統。

```rust
let mut a = 10.;
{
  // b 借走所有權，不可使用 a
  let b = &mut a;
  // 修改記憶體
  *b = 20.;
}
// b 生命週期提前結束，所有權還給 a
assert_eq!(a, 20.);
```

在上面的程式碼中，Rust 可以使用一個大括弧 `{}` 在執行區創造出一個**範圍 (scope)**，這個範圍跟函式、判斷式等 runtime 要素是一樣的。當使用 `let` 關鍵字指派一個新的變數，若變數沒有被移動，則都會在括弧關閉的地方被刪除。此概念在 C / C++ 中也有。

當使用 `&a` / `&mut a` 運算子，表示對變數 `a` 索取智慧指標，若有關鍵字 `mut`，則代表這個指標可以修改變數，前提是變數 `a` 也是可變的。索取指標會造成所有權被借走，導致 `a` 不能使用，這在編譯期間就會檢查，並且指出原始資料與指標之間的關係。

值得注意的是，這邊 `b` 不用加上 `mut` 標示為可變，因為指標僅修改指向的內容，而非自身。

所有權系統是為了保障**執行序安全 (thread safe)**，因此智慧指標只能在單一執行序使用，如果要在多執行序使用，比如說平行處理，則必須使用參照計數。

## Reference Counted

在程式語言中，通常會有**記憶體管理 (memory management)** 系統。短暫的執行區稱為 stack，用來作為部份資料生命週期的判定；而必須跨越多個執行區、太龐大或長期使用的資料則會放置在 heap。Rust 的所有權系統都是在 stack 間移動，不過為了達成跨執行序存取，必須將資料轉移至 heap，這時就必須使用原始指標達成。

**參照計數**的概念在於，所有的資料都存於 heap，當新增「變數」（Python 稱為「名稱」）時，就會增加計數器；當參照的數量變成 0 時，就會刪除資料。例如 Java、JavaScript 或 Python 這種只有參照計數系統的程式語言，就是用模擬 stack 的方式移除資料的引用。不過，完全以參照計數為主的程式語言便需要一套**垃圾收集 (garbage collection)** 系統，移除長久未使用的變數，它們可能在載入的過程中遺留下來，造成另類的記憶體洩漏。

在 Rust 中，[`std::rc::Rc`](https://doc.rust-lang.org/std/rc/struct.Rc.html) 和 [`std::sync::Arc`](https://doc.rust-lang.org/std/sync/struct.Arc.html) 是標準庫 (standard library) 提供的參照計數器，`Rc` 是單執行序使用的，可以應用於在容器之間分享資料；`Arc` 則是多執行序使用的，可以在不同執行序間分享資料。若沒有多執行序用途，`Rc` 存取的速度會較好。

另外得注意的是，`Rc::new()` 和 `Arc::new()` 會移動資料到內部，因此一開始就要用參照計數器的話就必須將資料包裝起來。而解除時是使用 `Arc::try_unwrap` 和 `Rc::try_unwrap`，可以直接將內部資料移動出來，不過必須在計數器為 1 的情況下。

```rust
use std::{
    sync::Arc,
    rc::Rc,
};

// 建立資料
let a = Arc::new(0);
// 增加引用
let b = a.clone();
// 檢查計數器
assert_eq!(Arc::strong_count(&a), 2);

let a = Rc::new(0);
let b = a.clone();
assert_eq!(Rc::strong_count(&a), 2);
```

至於跨執行序分享資料時，若需要修改該資料，會造成**資料競爭 (data racing)**，產生不同步的情況，因此 `Arc` 無法像 `Rc` 一樣輕鬆的改變 heap 上的數值。為了解決資料競爭，可以加上一個**互斥鎖 (mutex lock)**，使用一個**原子化 (atomic)** 的布林值（通常是 `u8`）來鎖住資料，執行序必須檢查鎖是否開啟，等到開啟後才可以鎖住並修改資料。

```rust
use std::sync::{Arc, Mutex};

let a = Arc::new(Mutex::new(0));
*a.lock().unwrap() = 10;
assert_eq!(*a.lock().unwrap(), 10);
```

不過對於原始類型，原子化類型 [`std::sync::atomic::Atomic*`](https://doc.rust-lang.org/std/sync/atomic/index.html) 應該更容易使用，而完全不需要互斥鎖。因為在支援的平台上，處理器可以自動排程寫入狀態，以避免底層的資料競爭，不過浮點數是不支援的。

除了普通持有資料的計數外，還有可懸掛空值的**弱參照 (weak reference)**，因此前者相對之下稱為**強參照 (strong reference)**。弱參照持有的原始指標可以修改，類似於 `mut &T` 或 `mut &mut T`，可以改變指向，而強參照持有原始資料的指標，因此只能固定。`Rc::downgrade()` 和 `Arc::downgrade()` 可以將強參照降級為弱參照，不過由於弱參照會有空值，使用數值時都需要重新檢查，較花時間。
