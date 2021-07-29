# Generic

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
