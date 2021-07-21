# Git

## GitHub

GitHub 只是使用 Git 的網路倉儲服務，這兩個是不同東西。

GitLab、BitBucket、Microsoft Azure 等都有提供類似的功能。

## Git

[Git](https://git-scm.com/) 是一種**分散**式**版本控制**架構。

在 Git 系統中第一個 Branch 通常稱為 master 或 trunk。
而分支跟標籤類似，只差在分支會跟隨新提交而往前移動，最新的提交稱為 **ORIG_HEAD**，
而當前的提交稱為 **HEAD**。
如果一個分支被刪除，它的 Base 以外的提交也會被刪除，除非它的 ORIG_HEAD 有掛標籤。

每個提交會有一個 Hash code，
可以視為一個**修正 (Patch)**。意即，如果當前這個分支修正了某個錯誤，其他分支還沒，
它們可以進行**合併 (Merge)**，產生**衝突 (Conflict)**。
此時可以透過人為或自動修正，創造一個合併提交 (Merge Commit)，即可解決。
於是 Base 就納入了該 Hash code，
在比較時就會認為此修正已經加入，即使目前版本中可能被改掉（Debug 就是另一個故事了）。
而因為有「合併」的動作，所以版本控制並非樹狀的結構。

**分散**式架構可以視為一個**遠端 (Remote side)** 與多個**近端 (Client side)**，以遠端為基準，通常不包含 stash。
遠端為主要儲存地，其他近端可以透過**克隆 (Clone)** 在線下修改後，再**推送 (Push)** 回去。
要注意所有人必須跟遠端的所有 Branch 有相同的 Base 或不存在，才可以進行推送。
當遠端不存在時，其中一個近端如果變成伺服器，其他近端將推送目標指向它，也可以做為遠端（當然也要注意 Base 問題）。

當推送時 Base 不符合會產生衝突。代表開發者私底下竄改了某些提交，必須選擇放棄近端或遠端其中一邊的歷史。
若放棄遠端，將會執行**強制推送 (Force Push)**，將會把遠端的目標分支強制同步成近端的樣子。

### Remote & Client

從遠端下載 master 分支到近端。
其他分支會在開發者切換時下載下來。

```bash
git clone https://github.com/MMD-Training-2019/SupplementaryMaterial.git
```

更新當前的分支。會比較 Base，如果近端比較新，會讓近端移轉到匿名分支上執行合併。

```bash
git pull
```

推送當前的分支。會比較 Base，如果遠端比較新，會叫近端手動執行 Pull（遠端最大）。

```bash
git push
```

強制推送。請先跟其他近端好好溝通過，否則應該將你的改變成為新分支。

```bash
git push -f
```

### Commit

啟動演算法，顯示單一檔案與上一次提交的改變。按下 `q` 可以退出。

```bash
git diff fileA.md
```

啟動演算法，掃描並記錄改變。其中 "." 表示當前資料夾，可以為倉儲內任何檔案名稱。
如果是資料夾，則會包含子資料夾和所有檔案。
而刪除指令 `git rm`、移動與重新命名指令 `git mv` 皆會更新狀態，同於 `git add`。

```bash
git add .
git add fileA.md fileC.py
```

如果在子資料夾中不想退出可以使用全域掃描。

```bash
git add -A
```

包裝提交（剛剛記錄到的改變）。如果沒有改變就不行新增。
每個提交都需要加上訊息。

```bash
git commit -m "First commit."
```

如果沒有給訊息，會進入 [Vim](https://www.vim.org/) 畫面，
按下任意字母鍵可以啟動輸入模式打入多行訊息。

輸入完畢後按下 Esc，輸入 `:wq` 離開 (Write & Quit)。

```bash
git commit
```

檢視近端的提交。可以選擇看所有的分支或圖形化顯示。按下 `q` 可以退出。

```bash
git log
git log --all --graph
```

切換當前提交。要注意如果不是分支是無法新增提交的。

```bash
git checkout aaaaaaa
```

若想要取消改變，可以使用 Stash，讓檔案回到上一個提交的狀態。

```bash
git stash
```

清除已記錄的改變 (`git add`)，不會影響檔案狀態。可以針對特定檔案操作。

```bash
git reset
git reset fileA.md fileC.py
```

從最近的提交開始拆除，一直拆到指定的提交（不包含）。這個動作不會影響檔案狀態。

```bash
git reset aaaaaaa
```

復原拆除。

```bash
git reset 'HEAD@{1}'
```

### Tag

顯示近端的標籤。

```bash
git tag
```

幫當前提交取名稱 `v1.0`。

```bash
git tag -a v1.0 -m "version 1.0"
```

推送新的標籤到遠端。如果要更新，可以加上 `-f`，不會動到提交的部分。

```bash
git push --tag
git push --tags
```

刪除近端標籤。

```bash
git tag -d v1.0
```

刪除遠端標籤。語法跟分支是一樣的。

```bash
git push origin :v1.0
```

移動近端標籤到某個提交，相當於刪除再新增。推送時用更新的方式。

```bash
git tag -f v1.0 aaaaaaa
```

### Branch

顯示近端的分支。

```bash
git branch
```

新增分支 "new-branch" 在近端。如果名稱存在不會新增。

```bash
git branch new-branch
```

切換到 "new-branch"。

```bash
git checkout new-branch
```

第一次推送 "new-branch" 到遠端。遠端分支前面會掛一個 origin。
遠端創立之後就不用特別講明了。

```bash
git push origin new-branch
```

刪除近端分支 "new-branch"。

```bash
git branch -d new-branch
```

刪除遠端分支。要注意遠端的提交會被移除！

```bash
git push origin :new-branch
```

移動近端分支到某個提交，相當於刪除再新增。推送時用強制推送。

```bash
git branch -f new-branch aaaaaaa
```

### Merge

合併 "new-branch" 到當前分支。
如果成功，可以直接做其他事；如果失敗，需要手動解決，額外新增一個合併提交。

```bash
git merge new-branch
```

舉手投降，還原到還沒合併的狀態。

```bash
git merge --abort
```

### Rebase

修改特定提交。輸入指令預覽最近的 3 個提交，
這時會開啟 Vim，把想要更改的提交狀態改為 "edit"，輸入 `:wq` 離開。

```bash
git rebase -i HEAD~3
```

這時會切換到該提交，並且可以更改，改完後執行繼續。

```bash
git rebase --continue
```

會疊加其他提交，有衝突的話需要解決。或是選擇放棄也可以。

```bash
git rebase --abort
```

### Submodule

子倉儲就是新增一個參考倉儲到倉儲裡，只要切換目錄到裡面，基本操作都一樣。
通常被需綁定版本的相依套件會使用。

不過子倉儲的提交會在主倉儲綁定，所以改變時主倉儲必須新增提交來更新。
子倉儲被視為檔案，可以被 `git add`、`git rm`、`git mv` 等指令操作。

將倉儲加到當前倉儲的當前目錄中。

```bash
git submodule add https://github.com/MMD-Training-2019/SupplementaryMaterial.git
```

克隆後下載子倉儲。加上 `--recursive` 會套用到子倉儲的子倉儲。

```bash
git submodule update --init --recursive
```
