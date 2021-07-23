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

在 Rust 中沒有繼承，當然想要手動作是可以：

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

嗯…語法上會比較繁瑣，不過倒是沒有上述的問題了，因為「父類型」已經變成自己的成員了，呼叫方法一定要從 `base` 欄位。當然這也不是建議的作法，而是要用強大的泛型下手。

## Generic

**泛型**的主要目的是**藉由參數創造類型**。打個比方，在 Rust 中 32 位元整數的陣列類型寫為 `[i32]`，而 32 位元浮點數的陣列 (array) 類型寫為 `[f32]`，而陣列寫做 `[T]`，其中 `T` 是一個類型的代號，用來「泛化」的指稱任何陣列類型。除了用類型作為參數，還可以用定值作為參數，例如陣列是有長度的，不同長度的陣列算是不同類型，記為 `[T; N]`，而 `N` 就是一種定值參數。這些參數又稱為**模板參數 (template parameter)**。

另一個功用在於解決繼承的煩惱，如果我們為類型設下了**條件**，那程式碼就能允許使用這個類型了，以下用「支援加法運算子」來當範例。

先從傳統有繼承系統的程式語言為例，Python 支援加法運算子主要是覆蓋 `__add__` 方法，這種名稱樣式稱為**魔法 (magic)** 名稱，可以想像成 Python 的潛規則。`__add__` 方法也能視為 C++ 中的 `operator+` 方法。

```python
from typing import TypeVar, Protocol

T = TypeVar("T")
R = TypeVar("R")

class Add(Protocol[T, R]):
    def __add__(self, rhs: T) -> R:
        ...

def plus(a: Add[T, R], b: T) -> R:
    return a + b
```

在 Python 3.8 中終於推出了「協議 (protocol)」類型，當用作類型標示時，代表只要輸入的類型有該方法或成員即滿足條件。不過得重申一次 Python 是動態語言，以上僅對靜態分析有用。C++20 則是稱為「概念 (concept)」，詳見：<https://en.cppreference.com/w/cpp/language/constraints>。在這之前，C++ 的泛型功能僅支援模板，所有類型都能接受，且在編譯時才會提示錯誤。

不過這些都只是附加一個條件在類型標註上。Rust 延伸自 Java 的介面 (interface) 概念，稱為特徵 (trait)，讓這些條件參與到程式設計中。順帶一題，Java 的繼承系統沒有多重繼承，算是兼容兩種概念。

以下是 Rust 中對加法運算子的 trait。

```rust
pub trait Add<Rhs = Self> {
    type Output;

    #[must_use]
    fn add(self, rhs: Rhs) -> Self::Output;
}
```

其中，「實作」`Add` trait 的類型可視作 `A`；上面的 `Rhs` 模板參數可視作 `B`；最後 `Add::Output` 這個類型別名 (type alias) 可視作 `C`，即 `A + B = C` 的關係。以下為實作範例：

```rust
# use std::ops::Add;
#[derive(Debug)]
struct Time(u64);

struct Duration{
    sec: u64,
};

impl Add for Time {
    type Output = Time;

    fn add(self, rhs: Self) -> Self::Output {
        // Tuple structure 的成員存取是用欄位順序
        Self(self.0 + rhs.0)
    }
}

impl Add<Duration> for Time {
    type Output = Time;

    fn add(self, rhs: Duration) -> Self::Output {
        Self(self.0 + rhs.sec)
    }
}

println!("{:?}", Time(20) + Time(30));
println!("{:?}", Time(20) + Duration { sec: 30 });
```

上面的範例可以看到，`impl A for B` 語句僅能為兩者實作一次，不過有模板參數的 trait 可以實作多次，第一次為 `Add<Time>`，第二次為 `Add<Duration>`，因為類型和 trait 只要搭配不同模板參數，就會被視為不一樣。

至於要將 trait 應用到類型標註中，觀念就很重要了：

+ Trait **不是**類型，而是類型的**條件**。因此 `impl A for B` 的 `A` 必須是 trait，`B` 必須是類型（`struct`、`enum`、`union` 皆可），在這個區塊內，`self` 和 `Self` **永遠**是指 `B`，只是剛好也滿足 `A`。定義 `A` 時也是如此，不過 `B` 還不存在，只會有自身條件可用。
+ 條件可以有**先決條件**，如 `Copy` trait 必須先實作 `Clone` trait，`Clone` 提供了複製的能力，`Copy` 表示在指派過程中會採取複製。
+ 條件可以**累加**，所以 trait 可以用加法運算子相加，這樣必須滿足全部要求。如 `Copy + Debug` 代表類型必須實作 `Copy` trait 和 `Debug` trait。由於 trait 可能有先決條件，這些行為算是聯集，重複的要求不會影響條件。
+ 模板參數是**類型**，可以加上條件。
+ 類型別名也是**類型**，在定義 trait 時可以加上條件。
+ 條件裡的相關名稱衝突時，可以使用 `<A as Add>::Output` 語法指定名稱。

實際應用中，函數可以加上模板：

```rust
# use std::ops::Add;
fn plus<A, B>(a: A, b: B) -> A::Output
where
    A: Add<B>,
{
    a + b
}

println!("{}", plus(10., 20.));
```

模板參數的條件也可以都寫在尖括弧中，如 `<A: Add<B>, B>`，不過巢括弧比較難看，可以用 `where` 關鍵字統一擺到最後面條列。

若不使用 trait，單純使用類型的模板也可以，以下是範例：

```rust
struct Handler<T> {
    inner: T,
}

impl<T> Clone for Handler<T>
where
    T: Clone,
{
    fn clone(&self) -> Self {
        Self { inner: self.inner.clone() }
    }
}
```

上面的實作區塊中新增了模板參數，以及 `where` 的補充語句。在函式中的模版參數，是來自於呼叫時；而實作區塊的模版參數，則是在創造實例或呼叫靜態方法時。上面的實作區塊表示，當 `inner` 欄位的類型有實作 `Clone` trait 的話，`Handler` 類型也會實作 `Clone`。

當然，如果一開始限制 `inner` 的話也可以，這樣直接由派生巨集來產生實作區塊就好。

```rust
#[derive(Clone)]
struct Handler<T: Clone> {
    inner: T,
}
```

在大多情況下，Rust 會自動填入模板參數的類型，如容器類型 `Vec<T>` 可以在 `Vec::push` 呼叫時推斷類型。若要主動填入模板參數，可以使用名稱空間存取運算子 `::` 在後方，如呼叫 [`Vec::<i32>::new`](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.new) 能夠填入 `Vec` 類型的模板；[`Iterator::collect::<Vec<_>>`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.collect) 能夠填入 `Iterator::collect` 的模板，底線的部份則是交由編譯器推導的必填欄位，可選欄位可不輸入。

上面提到的「先決條件」則是在 trait 定義時就要使用，如 `Copy` trait 的定義：

```rust
pub trait Copy: Clone {}
```

由於 `Copy` trait 是一種**標記 (marker)** 用的 trait，因此沒有任何要求，它的用途之前也都提過了。類似的例子如 `Sized` trait，表示已知大小的類型，動態大小的類型會寫上 `?Sized`，而反向條件目前還未支援。要創造一種標記式的 trait，除了定義先決條件外，還可以實作給全部符合條件的對象，如下面所示：

```rust
pub trait Marker: Clone {}

impl<T: Clone> Marker for T {}
```

在 Rust 中，實作語句 `impl A for B` 除了僅能實作一次，且只能在 `A` 或 `B` 其一是在與定義位於同一個專案（稱為 crate）中才行，以防尋找語句上的問題。但是 `impl A` 語句倒是不用配合 trait 必須一次實作完的限制，所以可以分散在同一個專案內的不同模組。在語法上，實作語句存在於任意模組即可，是不用導入的，不過與 trait 有關的方法必須導入該 trait。
