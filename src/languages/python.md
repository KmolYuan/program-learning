# Python

本篇簡介基礎概念。

主張：Python 為一種基於**易於撰寫與閱讀**且擁有**強大功能性**的程式語言。

常用的應用為：

+ 網際應用程式：伺服器後端程式與溝通。
+ 桌面腳本：方便操作檔案或系統排程而撰寫的編程。
+ 科學用途：進行較深入的數學運算，如矩陣、代數、微積分等等。
+ 非高效使用者界面：反應不須即時的圖形化使用者界面 (GUI, Graphical User Interface)。
+ 內嵌語言：主程式需要一種高階語言作為編程使用。

官方使用 C 語言來做實作[直譯器][interpretor]，稱為 CPython，實現如下重要的概念：

+ 得益於 C 語言程式庫的延伸性，提昇執行效能。
+ 原型 (Prototype) 規範，可以與多種程式語言交換資料。
+ WSGI (Web Server Gateway Interface) 協定可以使網頁框架變得更簡單。
+ 模組 (Module) 規範使製作與分享擴充功能更容易，令 Python 擁有大量的模組社群。
+ 語法相較於其他多功能的程式語言而言更簡單明確，除了完整的物件導向功能，
    還具有 Bootstrapping 的特性，並且可以使用標準庫內的高階功能。
    其中魔法 (Magic Function / Method / Name) 名稱（如 `__init__`）的概念是非常獨特的。
+ 能夠自解譯，當 Python 作為字串 (String) 輸入時，可以直接執行。

使用 Python 必須先知道它的**缺點**:

+ 速度不夠快：跟所有高階程式語言一樣，Python 的速度源自於底層優化，當程式碼複雜度已經是極限低時，
    因為那些方便安全的功能，Python 的速度永遠不是它的強項，你只能等待 CPython 更新或改善程式庫來強化性能。
    根據測試，它在相同邏輯下會比優化的 C 語言慢 20~200 倍。
+ 相對龐大：同於高階程式語言的問題，Python 包裝成產品是 Byte Code 加上虛擬層，仍沒有機器碼小。

[interpretor]: ../it-intro.md#compile

## Syntax

Python 是看[換行記號](../multilang/syntax.md#line-break)的，換而言之，你可以將每一行程式碼視為一條指令，
而不需要擔心它還有其他部分突然出現在檔案的其他地方，影響閱讀。

```python
# 不會視為 1 + 1 - 2 * 9，而是兩次運算
1 + 1
-2 * 9

# 若括弧 ([{ 為開啟狀態，可以做換行
# 1 + 1 - 2 * 9
(
    1 + 1
    -2 * 9
)
(1 + 1
    - 2 * 9)
```

程式碼區塊 (Code Block) 如果位階不是 Global，則**必須**[縮排](syntax.md#indentation)，縮排符號為 `\t` 或 4 個空白，
且同個檔案中只能擇一。官方建議使用後者。

如果手動換行，新的一行必須跟上一行有一樣或更多的縮排。

```python
def func(a, b):
    return a + b  # func 的子程式碼
```

「優先權」運算子大幅減少。為了長得像英文書寫，捨棄了很多老派程式語言強制使用的括弧 `()` 符號。
當然如果括弧會讓程式碼更好看，仍建議放置。

```python
# 舊習慣
if (font_size > 10):
    font_size = 10
    (a, b) = (b, a)

# 建議使用
if font_size > 10:
    font_size = 10
    a, b = b, a
```

Python 沒有 [Entry Point](IT-intro.md#compile) 的設計，所以可以理解成由上而下的執行，就跟直譯模式一樣。
不過如果是撰寫檔案，還是建議將程式碼封裝在 Function 中來呼叫，避免先後順序產生問題。
以下則是「模擬」Entry Point 的做法，假裝有一個入口程式可以執行。
這個方法也是官方建議撰寫龐大架構（多個模組）時的守則：如果沒有需求，程式碼不應該在 Global 執行。

```python
# main.py
def main():
    print("Hello World!")

if __name__ == '__main__':
    main()
```

## Builtins

本節介紹程式碼常用的內建功能。

### Data Types

Python 有一般程式語言常見的數據種類：

| 名稱 | 語法 | 說明 |
|:----:|:----:|:-----|
| bool | `True`, `False` | Boolean 布林值，代表 0 和 1。 |
| int | `-10`, `0b010`, `0x010`, `100_000` | Integer 整數，支援二進位、十六進位和長度標記。 |
| float | `10.0050`, `30.`, `.02`, `5e-2` | Floating Point 浮點數，支援科學記號標示。 |
| complex | `1j`, `10.j`, `.02j` | Complex Number 複數，可以與其他數字運算，有實部和虛部。 |
| bytes | `b"x00"` | Bytes 字節，尚未編碼的字串，除非壓縮需求，否則建議不要使用。 |
| str | `"sss"`, `'a1'`, `"''"`, `"\""`, `f"{n} mm"` | String 字串，代表一個或多個文字，使用反斜線代表特殊記號。Python 3.6 開始支援 Format Literal。 |

與原始的數據類型相比，Python 的數據沒有 `unsigned` 的種類，也沒有 `long` 的長度調整，增加了方便性。
而且，`bool`、`int`、`float`、`complex` 都可以混和運算。

至於字串則是 Unicode 8 編碼而非二進制編碼，本身就具備各種常用的演算法且支援檢索功能，
並可以藉由[物件導向](../it-intro.md#objective-orientation)的方式呼叫，如 `'sbc'.find('c')` 可以獲得 2。
而多行字串可以紀錄換行字元和縮排，用三個引號 `"""` 表示。

```
s = """
line1
line2
"""
```

在 Python 的物件導向概念中，數據類型也是物件，所以可以繼承作為更多用途。

### Indexing

使用檢索運算子 (index operator) `[]` 按順序取值。
索引跟大部分的程式語言一樣都是從 0 開始，並且之支援反向檢索，-1 代表最後一項。
如果檢索失敗會引發 `IndexError`。

```python
a = 'abc'
ac = a[0] + a[-1]  # 'ac'
```

檢索功能不只有數字，通過運算子自訂，也可以使用任何物件。

另一種內建的語法為分割器 (slice)，使用分號 `:` 在檢索運算子中。
分割器的第一項為開始，第二項為結束，第三項為間隔數；如果沒有標示則默認為頭尾和間隔 1。
透過這種方法，可以將內容切片為新的複製品或參照。

```python
bcd = 'abcdef'[1:4]  # 'bcd'
abcd = 'abcdef'[:-2]  # 'abcd'
fedcba = 'abcdef'[::-1]  # 'fedcba'
```

其實相當於建立 `slice` 物件填入而已。

```python
fedcba = 'abcdef'[slice(None, None, -1)]
```

像是知名模組 NumPy 使用 Tuple 容器一樣：

```python
matrix[:, -1] == matrix[(slice(None), -1)]
```

### Containers

除了特殊需求，Python 內建了各種完善且快速的容器。

Python 的記憶體都不是連續規劃的，所以可以儲存不同類型的內容，但是仍建議儲存有規則的內容。

而容器的大小稱為長度 (Length)，可以由 `len()` 函式獲得。

#### Tuple

元組 (Tuple) 是一種唯讀的鏈狀容器。
使用工廠函式 `tuple` 可以建立空的或轉換可迭代物件。

```python
# 小括弧表示 Tuple，某些情形下可以省略但不建議
a = ()
a = (1, 2, 3)
a = (
    "abc",
    "def",
)
a = 1,
a = tuple([1, 2, 3])
```

常用來解構和包裝物件，其他容器也可以，但是 Tuple 的成本最低。

```python
# 解構，按順序分配給 b=1, c=2, d=3
# 如果數量錯誤會出錯
b, c, d = a
# 回傳兩個值，其實是用 Tuple 包裝
return a, b
# For 迴圈解構
for a, b in [(1, 2), (3, 4)]:
    ...
```

檢索使用整數 `int` 和 `slice`。

```python
a = ('a', 10, 'bc')
abc = a[0] + a[-1]  # 'abc'
```

比較演算法可以直接用運算子達成，可是必須兩方皆為 Tuple。

```python
(1, 2, 3) == (4, 5, 6)
```

#### List

清單 (List) 是能夠修改的鏈狀容器，支援排序演算法等操作。
使用工廠函式 `list` 可以建立空的或轉換可迭代物件。

```python
# 中括弧表示 List
a = []
a = [1, 2, 3]
a = [
    "abc",
    "def",
]
a = list((1, 2, 3))
```

檢索使用整數 `int` 和 `slice`。

```python
a = ['a', 10, 'bc']
abc = a[0] + a[-1]  # 'abc'
```

透過 `append`、`insert` 方法來新增內容。

```python
a.append(5)  # 新增 5 到最尾端
a.insert(0, '888')  # 在 0 處插入 '888'，後方的索引都會移位
```

透過索引 `del` 運算子和 `pop` 方法來刪除內容。索引不存在會引發 `IndexError`。

```python
a.pop()  # 刪除最後一項
a.pop(0)  # 刪除第一項
b = a.pop(0)  # 刪除第一項，該值交給 b
del a[5:10]  # 刪除 5 到 9
```

透過索引指派運算子來改變內容。索引不存在會引發 `IndexError`。

```python
a[3] = 'gcc'
a[1:6] = b[7:12:-1]  # 長度必須一樣
a[:] = 'gcc'  # 從其他可迭代物件複製值，長度忽略，a = ['g', 'c', 'c']
```

比較演算法可以直接用運算子達成，可是必須兩方皆為 List。

```python
[1, 2, 3] == [4, 5, 6]
```

List 還支援排序，一般是使用 Hash 函式獲得的值，或可以自訂。

```python
a.sort()  # 由小到大
a.sort(reverse=True)  # 由大到小
a.sort(key=getitem_func)  # 由指定函式給值，該函式輸入一個項目後返回可分辨大小的數值
```

其他容器若想要排序，可以使用內建的 `sorted` 函式，可以獲得一個排列好的新 List。
`sorted` 函式支援任何可迭代物件，而且支援 `reverse` 和 `key` 選項。

```python
new_list = sorted((5, 6, 8, 7))  # [5, 6, 7, 8]
```

#### Dict

字典 (Dict) 是一種可修改的樹狀容器。
使用工廠函式 `dict` 可以建立空的或轉換兩兩一對的可迭代物件。
任何可雜湊的 (Hashable) 物件都可以作為索引，每個索引都會配一個值，
值可以是任何物件。這種關係稱為鍵值 (Key Value)。

內建的可雜湊值諸如前面介紹過的數據類型、Tuple 還有等等提到的 Frozen Set。
其他自訂類型可以實作雜湊用的魔法函數 `__hash__` 達成。
相同雜湊值的物件會被視為相同值，因此可修改物件都不支援雜湊值。

```python
# 大括弧表示 Dict
# 每一項依序由 Key、冒號、Value 組成
a = {}
a = {2: "2", 4: "abc", "U": {0: [1, 2, 3]}}
a = {
    "Name": "GOGO",
    "Old": 80,
}
a = dict([(1, 2), (3, 4)])
```

檢索使用 Key，不存在會引發 `KeyError`。
若要避免空索引，可以使用 `get` 方法。

```python
a = {'key': 10, 'a': 20}
n30 = a['key'] + a['a']  # 30
a.get('r', 70)  # 70
```

透過索引指派運算子來新增和改變內容，另外還有 `update` 方法可以更新。

```python
a['new'] = 50  # 更新 Key 和 Value
a.update([('n': 600)])  # 從兩兩相對的可迭代物件更新 Key 和 Value
```

用索引 `del` 運算子和 `pop` 方法來刪除內容。索引不存在會引發 `KeyError`。

```python
del a['old']
b = a.pop('old')  # 刪除 'old' 並回傳它的 Value
```

比較演算法可以直接用運算子達成，可是必須兩方皆為 Dict。

```python
{'a': 10} != {'a': 6}
```

使用包含運算子可以檢查 Key 是否存在。

```python
'a' in {'a': 5}
```

一般的迭代器只有一個項目，但是 Dict 有兩個，因此有三種方法。

```python
for k in a:  # 遍歷 Key
    ...
for k in a.keys():  # 遍歷 Key
    ...
for v in a.values():  # 遍歷 Value
    ...
for k, v in a.items():  # 遍歷 Key 和 Value
    ...
```

#### Set

集合 (Set) 為可修改的樹狀容器，其實就是 Dict 的 Key 部份，用來比較不可重複或無序的物件。
使用工廠函式 `set` 可以建立空的或轉換可迭代物件。

```python
# 大括弧表示 Set
a = set()
a = {1, 2, 3}
a = {
    "Jay",
    "Joy",
}
a = set([1, 2, 2, 3])
```

Set 無序不可檢索，使用 `add` 和 `update` 新增可雜湊物件。

```python
a = {12, 13, 14}
a.add(5)  # 新增 5
a.update([1, 2, 3])  # 從可迭代物件新增
```

使用 `pop` 方法從 Set 移除末端的一個物件，不過使用者無法掌握。

```python
a.pop()
```

還有比較用的運算子，同於數學運算。

```python
# 產生新的容器
a | b  # 聯集
a ^ b  # 補集
a & b  # 交集
a - b  # 差集
a < b  # 子集
# 產生布林值
a <= b  # 包含子集
a > b  # 超集
a >= b  # 包含超集
a == b  # 相等
```

其中聯集、補集、交集、差集可以使用指派運算子將自身替換掉。

```python
a |= b  # 同於 update，但是限定 Set 類型
```

另外還有唯讀的版本 Frozen Set（工廠函式 `frozenset`），支援雜湊功能。

```python
a = frozenset({1, 2, 3})
```

#### Closure & Iterator

當我們想表示一個容器是從另一個容器轉換過來時，
會需要複製和處理的過程。
如果手續不是很複雜，Python 提供了語法糖表示。

在下面的語法中，可以挑選大於 0 的數值，建立出新的 List。

```python
a = [i for i in old_list if i > 0]
```

等同：

```python
a = []
for i in old_list:
    if i > 0:
        a.append(i)
```

而其他容器也是類似。注意優先權運算子必須在混合逗號和指派時使用，因此 Tuple 必須使用工廠函式。

```python
a = {k: v << 1 for k, v in old_dict.items() if k in test_set}  # dict
func((i for i in my_list if i is not None), 70)  # Iterator
a = tuple(i * i for i in range(20))  # tuple
```

這種直接將 For 迴圈的語法變成表達式的方式稱為 Closure。
其實它跟 Function 是有點關係的，因為它代表一連串的指令，只是延後執行了。

在 Python 中 Function 也是物件，因此你可以在任何時候定義它，甚至作為輸入和輸出的變數。
這樣子就可以延遲執行或多次執行一種只在特定期間的任務，例如 `sort` 函式的 `key` 選項。
所以會有巢狀式：

```python
def func(...):
    def wrapper(...):
        ...
    return wrapper
```

一般過濾的程式用迴圈遍歷即可，但是如果有多道手續，就會做很多次。
為了節省時間，便使用了迭代器 (Iterator)。

Iterator 會在處理完一個之後等待，直到下一次呼叫再繼續執行。
這裡便會使用關鍵字 `yield`，可以視為一次 `return`，等完後才會繼續執行。
將這些內容取出來的方法有兩種，`next` 函式和 For 迴圈，
前者可以逐步取出；後者則是嵌套成迴圈，可以用 `break` 關鍵字中斷。

```python
def iterator():
    yield 0  # 第一次傳 0
    yield 10  # 第二次傳 10

r = iterator()  # 創造一個一次性實體
n0 = next(r)  # 0
n10 = next(r)  # 10
# 再呼叫會引發 StopIteration 錯誤

for i in iterator():  # 執行兩次的迴圈
    print(i)  # 0 10
```

關鍵字 `yield` 有傳出和傳入的功能，統稱為 Generator，只有傳出的稱為 Iterator。

```python
b = yield  # 傳入 b
b = (yield i) + 1  # 傳出 i 再傳入，加 1 指派給 b
```

使用 `send` 方法來傳入物件，按順序配合 `next` 函式傳出。不像單純傳出可以用 For 迴圈連續取值。

```python
r = iterator()
r.send(20)
v = next(r)
```

換句話說 Closure 語法其實只是匿名 Iterator 的縮寫。
而一般 For 迴圈會留下指派的名稱，Iterator 和 Closure 不會影響現有的名稱，可視為 Nested Local。

```python
# Closure
r = (i for i in my_list if i is not None)
# Iterator
def _iter():
    for i in my_list:
        if i is not None:
            yield i
r = _iter()
```

#### Builtins Iterator

內建迭代器 `range` 提供數字的計數，參數規則與分割器一樣。

```python
list(range(3))  # [0, 1, 2]
list(range(3, 7))  # [3, 4, 5, 6]
list(range(3, 8, 2))  # [3, 5, 7]
```

內建迭代器 `enumerate` 提供可迭代物件的計數器。

```python
for i, (x, y) in enumerate([(12., 20.05), (4., 60.5)]):
    print(i, x, y)  # 0 12.0 20.05, 1 4.0 60.5
```

更多迭代器可以從 `itertools` 模組導入。

## Function Arguments

函式 (Function) 是 Python 中非常普遍的存在。
Python 的函式不支援重載 (Overloading)，所以是使用引數 (Arguments) 的規則管理，迭代器也相同。

### Call and Return

呼叫 (Call)，類似數學的語法，函式名稱在前，小括弧在後。
即使沒有引數，仍需要寫上，不然只代表函式本身（注意函式也是物件），而非它的計算值。

```python
func()
```

呼叫之後，函式會帶入引數，運算並回傳 (Return) 回傳值 (Return Value)。
在定義函式時，`def` 關鍵字定義**引數類型**；`return` 關鍵字就是表示**計算結束並回傳回傳值**。

```python
def func():
    ...
    return return_value
```

函式中可以有多個回傳點，通常在判斷式中。
若不寫回傳值，將會回傳 `None`；若不寫回傳點，將會在最後一行回傳。

回傳值可以自行決定須不需要保留。

```python
a = f()  # 使用指派式會保留結果
g(f() + 10)  # 寫在表達式中可以參與運算後捨棄
f()  # 若都沒運算會直接捨棄
```

### Positional & Keyword Arguments

依照位置或關鍵字輸入。
按語法順序如下：

```python
f(a, b, c, ..., d=d, e=e, f=f, ...)
```

定義上就比較多元，基本語法跟輸入一樣。
不過 `=` 語法代表**預設值**，因此不會規定使用的語法。

```python
def f(a, b, c, ..., d=1, e=2, f=3, ...):
    ...
```

而擴充語法為 `*` 和 `**`，代表收集多餘的引數，`*` 代表多餘的位置引數；`**` 代表多餘的關鍵字引數。
在 `*` 前的為位置優先輸入，`*` 後的為強制關鍵字輸入。

其中 `*` 的值為 Tuple；`**` 的值為 Dict。前者可以不寫名稱，這樣多餘的引數就不允許存在。

```python
def f(a, b, ..., c=1, d=2, *args, e, f, ..., g=3, h=4, ..., **kwrds):
    ...
```

Python 3.8 後可以使用 `/` 語法強制前面的引數依順序輸入，不可以使用關鍵字語法。

```python
def f(a, b, ..., c=1, d=2, ..., /, e, f, ..., g=3, h=4, ...):
    ...
```

### Decorator

Python 提供一種比較抽象的方式處理函式包裝的需求（如前處理和後處理），稱為裝飾器 (Decorator)。
其語法為前方一個 `@` 符號，置於函式或類型上方一行，可以多個，每個一行，由外層包向內層。

裝飾器就是函式，只是它們只能填入一個函式引數，且回傳函式。

```python
def unit(func):
    def wrapper(s):
        f0(s + " mm")
    return wrapper

@unit
def f0(s):
    print(s)

f0('50')  # 50mm
```

裝飾器可以是任何可呼叫 (Callable) 物件，所以其實類型也可以作為裝飾器。
如 Python 3.7 提供的 Data Class。

```python
from dataclasses import dataclass


@dataclass
class MyData:
    a: int = 10
```

## Reference Counter

其實 Python 中並不叫做「變數 (Variables)」，而是「名稱 (Names)」，這麼做是為了區分 C 語言中的概念。

在較低層級的程式語言中，為了能夠自由調整記憶體的使用時機，於是分為「Stack」和「Heap」。
其中 Stack 為 Code Block 內的行程，分為一個 Global 與多個 Local，
Global 為整個程式的執行時間；Local 則是暫時的執行期間，如函式和類型定義。
而 Heap 則是自訂的存活期間，在 C 語言中是使用指標 (Pointer) 表示。

```c
// 規劃 50 個連續的 int 空間，並將第一個位址回傳給變數 ptr
int *ptr = (int *)malloc(sizeof(int) * 50);
...;
// 釋放從 ptr 開始的連續記憶體空間
free(ptr);
```

在 Python 中，使用 Reference Counter 的概念。
可以將「名稱」視為自動化的「指標」，從「指派」開始規劃，直到其結束 Steak。
如果該值曾經轉移給其他名稱，只要「名稱」存在，該記憶體就會保存，直到所有名稱被刪除。
使用 `is` 運算子可以檢查記憶體是否相同。

```python
a = [1, 2, 3]  # 規劃容器
b = a  # b 與 a 共用相同記憶體位置
print(b is a)  # True
b.pop(0)  # a 的第一項也會移除
del a  # a 被移除，但是容器依然存在於 b
```

然而，這些操作只要使用到「指派運算子」，都會**重新規劃記憶體**。
如一些唯讀的類型，大部分不會有編輯的方法，重新指派後就會刪除舊的、變成新的，就沒有「取別名」的效果。
而檢索指派運算子 `[]`、屬性存取運算子 `.` 也是同理，只是父名稱不會變。

```python
a = 10  # 規劃 10
b = a  # b 與 a 共用相同記憶體位置
print(b is a)  # True
# a = a + 20 的縮寫，將 a 從 10 轉移到 30
# 由於 10 還有 b 一個名稱，因此不會被刪除
a += 20
print(b is a)  # False
```

Python 的區域示意如下，比較特別的是類別的定義區是屬於其屬性 (Attributes) 的。

```python
# (Global)

def f1():
    # (Local)

    def f2():
        # (Nested Local)
        pass

# (Global)

class Object:
    # (Object Attributes)
    pass

# (Global)
```

如果在 Nested Local 沒有找到名稱的話，會從上一層 Local 尋找，以此類推，直到在 Global 尋找。
最後如果都找不到，會引發 `NameError`。

在 Local 中可以使用 `global` 關鍵字宣告此區段的某些名稱是屬於 Global 的，這樣整個範圍內就可以修改 Global 的名稱。
而在 Nested Local 可以使用 `nonlocal` 關鍵字宣告上一層 Local 的名稱。
至於單純引用**沒有做指派**就不用特別宣告。

```python
g = 20

def f():
    global g
    g = 60

f()
print(g)  # 60
```

## Modules

本節要介紹模組導入的概念。
Python 預設有一組搜尋路徑，分別是執行位置、程式庫位置（包含內建和外部的）、執行檔位置、自訂位置。
比較重要的是第一個，因為在執行期間，可以透過修改 `sys.path` 這個 List 來增加搜尋位置（但是不建議）。
這些搜尋位置稱為 Root，可以使用絕對導入 (Absolute Import) 語法。

導入時相當於把整個 `py` 檔執行一遍並記住，所以只會執行一次，其他次只會提取名稱而已。
導入後稱為模組 (Module)，通常是一個 `py` 檔或是目錄 (Directory)。
可以使用屬性存取運算子 `.` 來存取內容，能夠修改現有名稱的內容，但是不能使用指派語法。

如果導入失敗會發生 `ImportError`，如果是模組名稱找不到，Python 3.6 開始會引發 `ModuleNotFoundError`。

```python
import os
import sys

print(sys.path)
```

如果模組是一個目錄，則會導入它的魔法模組 `__init__.py`，如果沒有就會視為空模組。
不過目錄內的其他模組可以使用屬性存取運算子 `.` 來存取。

不過使用 `import` 語法導入時必須在引用時使用他的全名，因此會使用 `from` ... `import` 語法省去其前綴模組。
如果名稱重複會有被蓋掉的風險，可以使用 `as` 語法更改名稱，不用擔心導入前的物件會被刪除。

```python
import os.path
my_path = os.path.join("aaa", "bbb")
from os.path import join
my_path = join("aaa", "bbb")
from os.path import join as pth_join
my_path = pth_join("aaa", "bbb")
```

而且 `from` ... `import` 語法的方便之處是可以導入多個同模組的名稱，而且加上括弧後可以換行放置。

```python
from os.path import join as pth_join, sep
from os.path import (
    join as pth_join,
    sep,
)
```

因為只有 Root 可以使用絕對導入的語法，前綴會變得比較長，因此另外還有相對導入 (Relative Import) 語法。
此語法是在開頭加上一個 `.` 符號，表示從此模組的上一層模組（也就是 `py` 檔的目錄）開始尋找。
再增加 `.` 就會再往上一層，不過不建議使用以免誤導。

```python
from .module_a import my_list
```

使用絕對導入搭配魔法模組 `__init__.py` 還可以避免遞迴導入 (Recursive Import)，也就是兩個模組互相導入的狀況，會無法分辨該先執行誰。
可以在 `__init__.py` 中導入全部的子模組，在其他模組只要導入該目錄就好。
而共用的內容通常也會獨立放置以降低相依性，避免遞迴導入。

而定義魔法名稱 `__all__`（List 物件）成為公開名稱清單也有助於 API 的開發。

```python
from .module_a import my_list

__all__ = ['my_list', 'f1', 'f2', 'f3']

def f1(): pass
def f2(): pass
def f3(): pass
```

從 Python 3.7 提供延遲載入功能，藉由定義魔法名稱 `__getattr__` (PEP 562) 這個函式來動態導入某些較費時的物件。

```python
def __getattr__(name):
    return ...
```

## Objective Orientation

經過前面章節的介紹，屬性存取運算子 `.` 是物件導向程式設計非常重要的一部分。
如果 Function 是為了某個類型而運作，則稱為 Method。
可以簡單的理解成：

```python
def f1(me, she):
    ...
def f2(me, he):
    ...
def f3(me, they):
    ...
```

可以注意到，「me」的角色是主角，有三件事情環繞著它做，而且不能假他人之手。
因此在 Python 中，第一項參數被稱為「self」，且這「類」角色會變成一個「類型」。

下面這個範例中，將示範如何使用 Python 的物件定義功能。

```python
from math import hypot


class Point:

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def distance(self, p):
        return hypot(self.x - p.x, self.y - p.y)


if __name__ == '__main__':
    p1 = Point(0, 0)
    p2 = Point(30, 40)
    print(type(p1))  # <class '__main__.Point'>
    # 原形表示法，self 表示 p1
    print(Point.distance(p1, p2))  # 50
    # 常用表示法
    print(p1.distance(p2))  # 50
```

上面的魔法方法 (Magic Method) `__init__` 代表初始化 Point 物件時的參數，
並且方法中能用指派運算式 `self.x = x` 存入參數。
而一般方法 `distance` 可以計算並得到結果。

還有繼承概念在物件導向也是非常重要的。在不寫的情況下，所有的類型皆繼承自 `object` 類型。

```python
class A:

    def method1(self):
        print("A.method1")

    def method2(self):
        print("A.method2")


class B(A):

    def method1(self):
        super(B, self).method1()
        print("B.method1")

    def method3(self):
        print("B.method3")
```

Python 中不存在重載，因此全部都會覆蓋 (Overriding)。
在上面的範例中，B 繼承 A 會把所有內容獲得，但是因為定義 `method1` 會掩蓋原本的功能，
因此要使用 `super(Type, obj)` 這個函式透過載入上一個類型來呼叫其方法。
或直接寫 `A.method1(self)` 也可以，可是就會定死型別，擴充時就會不方便。
也因為無法重載，Python 的類型定義不可以分開寫，除非用繼承的方式。

很多初始化方法都會先載入上一個類型的內容。

```python
class SpecialItem(Item):

    def __init__(self, name):
        super(SpecialItem, self).__init__(name)
        ...
```

另外還有抽象類別 (Abstract Class) 和抽象方法 (Abstract Method) 以避免[菱形繼承問題](IT-intro.md#objective-orientation)。
在名字好記的內建模組 `abc` 中就有提供抽象類別的功能，簡單來說就是建立「有名無實」的方法，以確定其可以被父類別呼叫，再由子類別做實作。

```python
from abc import ABC, abstractmethod


class Interface(ABC):

    def method1(self):
        self.method2()

    @abstractmethod
    def method2(self):
        raise NotImplementError
```

再來是魔法方法，它們負責實作**運算子**、工廠函數轉型、一些內建函式的功能。
向之前提過的 `__hash__` 就是這個例子。

```python
class Point:

    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, p):
        return self.__class__(self.x + p.x, self.y + p.y)


if __name__ == '__main__':
    p1 = Point(20, 50)
    p2 = Point(30, 40)
    p3 = p1 + p2
    print(p3.x, p3.y)  # 50 90
```

上面的魔法屬性 `__class__` 會回傳自己的類型本身，而 `__add__` 會提供相加運算子的功能。
還有 `__int__`、`__str__`、`__iter__` 等可以轉型、變成迭代物件等功能可以使用。
而指派運算子 `=` 是 Reference Counter 的一環，所以不提供實作。
更多魔法方法可以看[官方網站](https://docs.python.org/3/library/operator.html)的介紹。

其實，魔法屬性 `__dir__` 是一個 Dict，而它會紀錄所有的屬性名稱。這是一個遞迴概念，因為 Dict 物件也有 `__dir__`。
所以可以簡單理解為，Python 的物件導向是由 Dict 容器做出來的。
因此每次 Dict 類型效能增加，都會使整個性能大幅提昇。

## Exceptions

在 Python 中不會發生真的「崩潰」錯誤，至少在純 Python 的情況下。
因為 Python 有一套例外機制，任何錯誤都是有來頭的，像之前提到的 `IndexError` 等。

如果在一個可能會發生錯誤的地方想要避免例外，可以提前檢查，如檢查 Key 的存在以避免 `KeyError`。
但是某些情況是無法掌握的，如 API 的輸入是不合法的，就必須提前阻止使用者輸入的正確性。
在 Python 中使用 `raise` 語法引發例外。

```python
def f(t):
    if t < 1:
        raise ValueError("times must larger or equal than 1")
    ...
```

其實所有的例外都是繼承自 `Exception` 這個內建類型，相關的也會用繼承的方式表示，
如 `ModuleNotFoundError` 繼承自 `ImportError`。
開發者可以製作自己的例外，`raise` 語法中可以用 `Exception` 或任何子類型，以及其物件（加上訊息字串初始化）。

如果要避免一個不確定例外的產生，可以使用 `try` ... `except` ... `else` ... `finally` 語法。
這個語法有四個部分，有 `try` ... `except` 和 `try` ... `finally` 兩種簡單組合，而前者可選 `else` 和 `finally` 兩個區塊。
其中 `try` 區塊可以允許引發錯誤；`except` 區塊會捕抓例外類型（包含子類型）的實體並執行對應指令，沒捕抓到會視為例外引發；
`else` 區塊會在沒有發生例外時執行；`finally` 區塊是 `try` 區塊執行完後一定會執行的部分，即使已經執行 `return`。

```python
try:
    if n < 0:
        raise ValueError("n must be positive")
except ValueError as e:  # 使用 as 關鍵字導入例外實體
    print(f"value wrong! {e}")
except (IndexError, NameError):  # 多個例外
    pass
else:
    print("you're right!")
finally:
    print("done!")
```

### Context Manager

單純的 `try` ... `finally` 可以在 `return` 後執行額外的工作，例如關閉檔案。

```python
f = open("f.txt", "w+")
try:
    return f.read()
finally:
    f.close()
```

而這個行為容易產生一些誤解，因此設計了語法糖，也就是 `with` 語法。
當一個類型擁有 `__enter__(self)` 和 `__exit__(self, type, value, traceback)` 兩個魔法方法時，
可以使用 `with` 語法建立實體，此實體可以幫忙在錯誤發生時完成 `finally` 的工作。

以下用 `open` 工廠函數建立的 `File` 類型物件為例，這也是最常用的應用。
這樣就會達到上面的效果。

```python
with open("f.txt", "w+") as f:
    return f.read()
```

## Doc String

Python 提供使用註解轉換為說明手冊的功能，這些註解稱為 Doc String。
這些「註解」其實是普通字串，只是沒有指派而已，使用多行字串或是單行皆可以通過。

由於是註解，不可以使用運算前的樣式，如 `+` 運算子或 Format Literal。
而且必須擺在有文字的第一行。

以下是模組的 Doc String。

```python
# example.py
# -*- encoding: utf-8 -*-

"""About my module:

...
"""

__email__ = "example@gmail.com"

from ... import ...
```

以下是函式和類型的 Doc String。

```python
def foo():
    """About my function ..."""
    ...

class A:

    """About my object ..."""

    def __init__(self):
        """Startup!"""
```

取得文字則是從魔法名稱 `__doc__` 字串物件中取得，函式和類型則是從魔法屬性。
而內建函式 `help` 可以在直譯器中顯示 Doc String。

## Annotations

Python 3.5 開始逐漸重視類型標注的重要性，之前都使用較狹隘的函式註解標示。

```python
def add_unit(*ns: float, unit: str) -> str:
    s = ""
    for n in ns:
        s += f"{n} {unit}"
    return s
```

或純註解 `# type: int` 等。因此在 Python 3.6 提供如下語法：

```python
a: int  # 名稱標示
a: int = 10  # 結合指派表達式
```

注意 Python 是不會在執行期間檢查類型的，必須手動做。而官方提供了 MyPy 這個工具協助檢查類型標示。

雖然所有的物件都繼承自 `object` 類型，但是為避免誤導，MyPy 使用 `Any` 作為未標示的基底。
Python 3.5 提供了內建模組 `typing`，從裡面可以導入泛型的容器類別。
至於自訂的類型也可以直接使用。

```python
from typing import Dict, Set

a: Dict[str, Set[str]] = {'classmate': {'Ann'}}
```

Python 3.7 提供了 Python 4 的未來功能 `annotations` 來支援遞迴標示。

```python
# 使用前
class A:
    def method(self) -> 'A':
        ...

from __future__ import annotations

# 使用後
class A:
    def method(self) -> A:
        ...
```

而 Python 3.7 的魔法名稱 `__annotations__` 和類型的魔法屬性是 Dict 物件，可以用來檢查，但是函式中無法收集。
建議交給靜態分析器做就好了。

```python
a: int = 10
print(__annotations__['a'] is int)
```
