# Object-oriented Programming

**物件導向編程 (Object-oriented Programming)** 是一種程式的設計模式。廣義來說，任何圍繞著**結構體類型**設計的寫作方式都可以稱為物件導向，儘管有些程式語言並不習慣以此方式操作或設計。在一般的程式語言中，會有著原始類型 (primitive type)，當要同時操作多個變數，支援**結構體 (structure)** 或**聯合體 (union)** 類型的程式語言可以將多個變數一起儲存。

聯合體與結構體的差別在於，聯合體的欄位都是共用的，以最長的欄位為總空間，結構體則是分開的，消耗的空間是全欄位的總和。聯合體共用欄位會導致二進位數值在每個欄位的意義都不同，所以讀寫都是不安全的。結構體可以沒有欄位，以作為一種泛型標示，後面會提到。

**類型 (type)** 準確來說是一種**記憶體布局 (memory layout)**，從系統中索取記憶體大小，再將資料存入。這些各別存入的資料又稱為**實例 (instance)**。例如儲存整數時使用 8 位元，雖然記憶體用量很小，但是其數值無法超過 2 的 8 次方 256，所以後續還有更長的記憶體空間的類型。又以結構體來說，若定義一個「平面點座標」由兩個 64 位元的浮點數構成，記憶體長度就會是兩倍，且創造出每個點都會有兩個 64 位元的浮點數，此時這些點便稱為「平面點座標」的實例。

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
// 以成員存取運算子呼叫該函式
println!("{}", (&p1).distance(&p2));
```

在上面的範例中，可以使用 `impl Point` 語句將 `distance` 函式「實作 (implement)」於 `Point` 類型中，這樣就可以用**關聯函式 (associated function)** 的方式呼叫，這些函式又稱為**方法 (method)**。不過這樣子稍顯累贅，對於已創造的實例，可以直接使用 `.` 成員存取運算子呼叫該函式。

實作語句中為了方便，可以直接使用大寫開頭的 `Self` 關鍵字稱呼自己的類型，用小寫開頭的 `self` 關鍵字稱呼自己的實例。函式的第一項參數可以替換為自己的實例，而進行二元運算時，習慣把另一方稱為 `rhs` (Right Hand Side)。

在 Rust 中的方法有下列幾種：

```rust
struct Dummy {
    value: f64
}

impl Dummy {
    fn new() -> Self {
        Self { value: 0. }
    }

    fn builder(mut self, value: f64) -> Self {
        self.value = value;
        self
    }

    fn into_value(self) -> f64 { self.value }

    fn setter(&mut self, value: f64) {
        self.value = value;
    }

    fn getter(&self) -> f64 {
        self.value
    }
}

let mut d = Dummy::new().builder(20.);
println!("{}", d.getter());
d.setter(30.);
println!("{}", d.into_value());
```

由上而下分別是：

+ **靜態方法 (static method)**，又稱為**類型方法 (class method)**：其主要是進行與此類型相關的計算，或是實例的生成，如 `new` 函式是用來產生此類型的實體。
+ **建造方法 (builder method)**：傳入自身實例再傳出，藉由將實例標示為可變的 (mutable)，可以作為初始化專用的方法。如 `builder` 函式可以用在 `new` 函式之後接連呼叫。
+ **消耗方法 (consuming method)**：傳入自身實例，但是沒有傳出。這導致實例本身會在函式結束時被刪除，這種方法通常用做構建其他類型。如 `into_value` 函式會消耗自身，回傳攜帶的數值。若要定義刪除時的行為，應該實作 [`Drop`](https://doc.rust-lang.org/std/ops/trait.Drop.html) trait。
+ **可變實例方法 (mutable instance method)**：以可變參照的方式參與函式，實例必須先標記為可變。如 `setter` 函式途中變更攜帶的數值。
+ **實例方法 (instance method)**：以唯讀參照的方式參與函式。如 `getter` 函式僅讀取攜帶的數值。

在範例程式中可發現，Rust 使用成員存取運算子 `.` 時可以自動解開參照或取址，這樣就不用特別寫 `(*x).method()` 或 `(&x).field` 的語法了。另外，Rust 中欄位的名稱和方法的名稱不會衝突，端看使用的語法，如 `x.method()` 和 `X::method` 會認為是方法，而 `x.field` 會被認為是欄位成員。至於可呼叫的成員，呼叫時必須特別加優先權運算子 `(x.field)()`。

## Inheritance

**繼承**是一種方便將多個類型混合的概念，這樣父類型能夠相容子類型，達到函式能接受多類型的條件。下面用 Python 舉例：

```python
class Point2D:
    x: float
    y: float

class Point3D(Point2D):
    z: float
```

但是，繼承產生的結構會變得很複雜：父類型可以被多個子類型繼承，但是如果要附加某些功能，勢必要父類型去繼承更多，但是父類型間很可能也不相容。通常這樣的解法會是使用多重繼承，允許有多個父類型，各類型間會變成跳躍式的樹狀關係。例如 Python 要製作整數列舉類型，會同時繼承整數與列舉類型：

```python
from enum import auto, Enum

class IntEnum(int, Enum):
    FIRST = auto()
    SECOND = auto()
```

然而，這之中可以形成一個閉迴路，會讓衝突更難解決：若兩個父類型有相同的方法或成員，究竟該使用哪個呢？順序繼承？要如何處理重複的名稱呢？可見性？而且，設計 API 時若是粗心大意，就一定要從上游解決了。

在 Rust 中沒有繼承，當然想要手動實作是可以：

```rust
struct Point2D {
    x: f64,
    y: f64,
}

struct Point3D {
    base: Point2D,
    z: f64,
}
```

嗯…語法上會比較繁瑣，不過倒是沒有上述的問題了，因為「父類型」已經變成自己的成員了，呼叫方法一定要從 `base` 欄位。當然這也不是建議的作法，而是要從強大的泛型下手。
