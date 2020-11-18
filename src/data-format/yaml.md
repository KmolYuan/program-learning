# YAML

YAML Ain't a Markup Language (YAML) 是一種結構語言。

特點：可讀性高、以樹狀結構為主體。

結構性語言的實作通常沒統一規定，為了謹慎，樹狀結構通常是無序的，鏈狀結構通常是有序的。

與兩大對手的比較：

| 特性 | YAML | TOML | Json |
|:----:|:---:|:-----:|:----:|
| 主結構 | 樹狀 | 淺樹狀、鏈狀 | 樹狀 |
| 語法 | YAML | TOML | JavaScript |

以上三大結構語言都支援樹狀和鏈狀結構。
而基礎類型 bool, int, float, string 和自訂類型（由實作決定）都支援。

通常 TOML 和 YAML 應用於設定檔，Json 應用於網際間的資料傳遞（可以壓縮成一行減少體積）。
其中 Json 是 JavaScript 的 object 物件，所以可以直接被 JavaScript 讀取，有夾帶惡意程式碼的風險。

YAML 檔案的副檔名為 `*.yaml`，某些縮寫會用 `*.yml`。

在往後的修訂中，YAML 會嘗試變成 Json 的子集語言，好讓一般的 Json 解析器可以讀取之。

## Key Value

可以想像成 C++ 的 Map 或 Python 的 dict 容器，YAML 的語法如下：

```yaml
# 註解
key: value
# bool
true
false
# int
123
0o14
0xC
# float（字母不分大小寫）
0.
1e-10
.nan
-.inf
```

其他的語法會被視為 string，而留白會被視為 null, nil 或 None 值（由實作決定）。
會違反 YAML 語法的 string 值必須被雙引號或單引號從頭到尾包起來。

```yaml
version: "3.9"
title: "My: title"
```

# Indentation

縮排是樹狀結構的主要語法，代表子節點。
YAML 的縮排通常為 2 空白，某些實作會對此嚴格限制。

```yaml
A:
  B: B value
  C: C value
```

鏈狀結構則是可選縮排，但必須一致。

```yaml
A:
- 1st
- 2nd
B:
  - 1st
  - 2nd
```

# Flow Style

線性的表示法，與 Json 語法接近。

```yaml
# 樹狀結構
A:
  B: B value
  C: C value
# 同於
A: {B: B value, C: C value}
# 鏈狀結構
B:
  - 1st
  - 2nd
# 同於
B: [1st, 2nd]
```

# Reference

當樹狀結構出現重複內容時可以引用先前的資料。
使用 `<<` 插入內容，而之後的 key 可以更新。

```yaml
- &os
  name: Ubuntu
  version: 20.04
  platform: x64
  source:
    - main.cpp
  target: main
- <<: *os
  platform: x86
test1: &test
  build: true
  override: false
test2:
  <<: *test
  skip failed: true
```

# Multiline String

字串太長時，為方便閱讀，可以手動折行，但是保持原本的單行狀態。

```yaml
doc: aaa bbb
doc:
  aaa bbb
doc: "aaa
  bbb"
doc: >
  aaa
  bbb
doc: >-  # 忽略接續的空白行
  aaa
  bbb
```

想包含換行記號，除了用 `\n` 記號外，可以直接記錄換行狀態，但忽略縮排。

```yaml
doc: aaa\nbbb
doc: |
  aaa
  bbb
```
