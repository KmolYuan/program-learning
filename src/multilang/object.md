# Object-oriented Programming

**物件導向編程 (Object-oriented Programming)** 是一種程式的設計模式。廣義來說，任何圍繞著**結構體類型**設計的寫作方式都可以稱為物件導向，儘管有些程式語言並不習慣以此方式操作或設計。在一般的程式語言中，會有著原始類型 (primitive type)，當要同時操作多個變數，支援**結構體 (structure)** 或**聯合體 (union)** 類型的程式語言可以將多個變數一起儲存。

聯合體與結構體的差別在於，聯合體的欄位都是共用的，以最長的欄位為總空間，結構體則是分開的，消耗的空間是全欄位的總和。聯合體共用欄位會導致二進位數值在每個欄位的意義都不同，所以讀寫都是不安全的。結構體可以沒有欄位，以作為一種泛型標示，後面會提到。

```rust
// 欄位 (field) 結構體
// 每個欄位又稱為成員 (member)
struct Point {
    x: f64,
    y: f64,
}

// 元組 (tuple) 結構體
struct Coordinate(f64, f64);

// 空結構體
struct Empty;

// 為陣列取別名
type Pos1 = [f64; 2];
// 為元組取別名
type Pos2 = (f64, f64);
```

在物件導向編程中，我們習慣以**駝峰體 (camel case)** 命名類型，儘管原始類型通常偏好全部小寫。駝峰體是以大寫字母作為單字開頭，接連著單字寫成，例如 `RedApple`。而類型以外的名稱通常都用**蛇體 (snake case)**，即用 `_` 底線字元連接，如 `red_apple`，大寫表示全域變數或定值。

環繞結構體撰寫的程式如：

```rust
struct Point {
    x: f64,
    y: f64,
}

fn distance(p1: &Point, p2: &Point) -> f64 {
    f64::hypot(p1.x - p2.x, p1.y - p2.y)
}

let p1 = Point {x: 10., y: 0.};
let p2 = Point {x: 40., y: 40.};
println!("{}", distance(&p1, &p2));
```

計算兩點距離的程式碼如 `distance` 函式所示，由於此函式只設計給 `Point` 類型使用。如果有一系列針對此類型的函式，通常會把第一項參數都設定成此類型，這樣就有物件導向的概念了。不過，這樣仍會比較繁瑣，要分別導入一系列函式的名稱才能使用。因此，一些程式語言會藉由名稱空間 (namespace) 的概念，將這些關聯的函式與類型綁定。

```rust
struct Point {
    x: f64,
    y: f64,
}

impl Point {
    fn distance(&self, rhs: &Point) -> f64 {
        f64::hypot(self.x - rhs.x, self.y - rhs.y)
    }
}

let p1 = Point {x: 10., y: 0.};
let p2 = Point {x: 40., y: 40.};
// 呼叫關聯函式 (associated function)
println!("{}", Point::distance(&p1, &p2));
// 呼叫方法 (method)
println!("{}", (&p1).distance(&p2));
// Rust 中的方法可以自動取址和解參照
println!("{}", p1.distance(&p2));
```

在上面的範例中，可以使用 `impl Point` 語句將 `distance` 函式「實作」於 `Point` 類型中，這樣就可以以**關聯函式 (associated function)** 的方式呼叫。不過這樣子稍顯累贅，因此更可以用**方法 (method)** 的樣式呼叫，直接使用 `.` 成員存取運算子呼叫該函式出來。其中，函式的第一項參數已替換為本身，以 `self` 關鍵字代表，而進行二元運算時，習慣把另一方稱為 `rhs` (Right Hand Side)。
