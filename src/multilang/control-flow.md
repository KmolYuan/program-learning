# Control Flow

**控制流程 (Control Flow)** 是順序控制程式中不可或缺的一部分。以下將介紹程式語言中會出現的流程語法。所謂順序控制，表示程式是依序執行，習慣會從上到下、從左到右，以利閱讀。以下會搭配可執行的 Rust 語言程式碼來示範。

```ignore
do first;
do next;
first; second; third;
```

## Goto Statement

跳轉語句是較為原始的控制方式，然而這種直接跳轉的方式比較破壞閱讀和程式的嚴謹，所以通常只在底層使用。此語法需要設定一個位置（如 C / C++ 中稱為 label）再藉由 `goto` 語句跳轉，由於此位置可以在 `goto` 前後，較不易控制。在迴圈語句中可用來跳脫迴圈，參見該章節以取得更多說明。

關於更多 `goto` 語法，可以參考 [ANSI C](http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf) 對於跳轉語句 (jump statement) 的說明。

## Condition Statement

**判斷句**是由一個布林值 (boolean)，也就是 true 和 false 兩個值判斷程式的前進方向，藉此組合出不同的執行效果，最常見的就是 `if` `else` 語句。判斷句可以藉由交疊形成多種判斷，如 C / C++ 中的 `switch` `case` 語句等等。

```rust
// 一次判斷
if true {
    println!("do something if true");
}

// 一次判斷，包含例外
if true {
    println!("do something if true");
} else {
    println!("do something if false");
}

// 兩次判斷，包含例外
if false {
    println!("case 1");
} else if true {
    println!("case 2");
} else {
    println!("case 3");
}
```

### Pattern Matching

類似於 `if` `else` 的組合，但是通常配合列舉 (enumeration) 的類型做主軸，並且可以設定保護條件 (guard condition)。

```rust
enum Color {
    RED,
    BLACK,
    WHITE,
    OTHER(u8, u8, u8),
}

let color = Color::OTHER(162, 33, 22);
match color {
    Color::RED => { println!("red"); }
    Color::BLACK => { println!("black"); }
    Color::WHITE => { println!("white"); }
    Color::OTHER(r, g, b) => { println!("color ({}, {}, {})", r, g, b); }
}
```

在單一匹配的時候，Rust 可以使用 `if let` 語句。

```rust
struct Color(u8, u8, u8);

let color = Color(162, 33, 22);
if let Color(r, g, b) = color {
    println!("color ({}, {}, {})", r, g, b);
}
```

## Loop

**迴圈**可以一直重複程式區段，技術上來說是無法中斷的，必須依靠跳轉語句。為了迴圈而設計的跳轉語句通常為兩種，只跳過當前區段的為 `continue`；離開迴圈的為 `break`。

```rust
loop {
    if true {
        println!("break now!");
        break;
    }
}
```

延伸條件的迴圈為 `while` 迴圈，即為在一開始檢查條件，條件為 `true` 時就會繼續循環。

```rust
let mut b = true;
while b {
    println!("run once");
    b = false;
}
```

另外迭代器 (iterator) 便是一種針對迴圈設計的資料結構，能夠按順序取得數值。序列 (sequence) 和映射 (map) 都可以轉換成迭代器，在 Rust 中，`into_iter()` 和 `iter()` 等方法可以轉換成迭代器。

```rust
for i in [1, 2, 3] {
    println!("{}", i);
}

// for-loop 等同於
let mut iter = [1, 2, 3].into_iter();
while let Some(&i) = iter.next() {
    println!("{}", i);
}
```

上面的 `while let` 語句是結合 `while` 迴圈與 `if let`，直到匹配失敗才會跳脫迴圈。
