# 02. ファイル操作編 - 高度なファイル・ディレクトリ操作

## この章について

基礎編でファイルの基本操作を学びました。この章では、より実践的で効率的なファイル操作方法を学びます。

### 学習目標

- ファイルの検索と絞り込み
- 複数ファイルの一括操作
- ファイル内容の検索と置換
- ディレクトリツリーの表示
- シンボリックリンクの理解

---

## この章で学ぶこと

### 📘 [ファイル検索](./file-search.md)
- `find`コマンドで条件指定検索
- `locate`コマンドで高速検索
- Windows PowerShellでの検索
- 検索結果の活用

### 📗 [テキスト検索 - grep](./text-search-grep.md)
- `grep`コマンドの基本
- 正規表現パターン
- 再帰検索とファイル絞り込み
- Windows PowerShellの`Select-String`

### 📕 [一括操作とパイプライン](./batch-operations.md)
- パイプライン（|）の活用
- `xargs`で一括処理
- ループによる繰り返し処理
- 実践例：ファイルのリネーム、バックアップ

---

## 実践的なファイル操作例

### 例1: 最近変更されたファイルを探す

**macOS/Linux**:
```bash
# 過去7日間に変更された.txtファイル
find . -name "*.txt" -mtime -7
```

**Windows PowerShell**:
```powershell
# 過去7日間に変更された.txtファイル
Get-ChildItem -Recurse -Filter "*.txt" |
  Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-7)}
```

### 例2: 特定の文字列を含むファイルを探す

**macOS/Linux**:
```bash
# "TODO"を含むすべての.pyファイル
grep -r "TODO" --include="*.py"
```

**Windows PowerShell**:
```powershell
# "TODO"を含むすべての.pyファイル
Get-ChildItem -Recurse -Filter "*.py" |
  Select-String -Pattern "TODO"
```

### 例3: ファイルサイズの大きい順に表示

**macOS/Linux**:
```bash
# サイズの大きい順に上位10ファイル
find . -type f -exec ls -lh {} + | sort -k5 -hr | head -10
```

**Windows PowerShell**:
```powershell
# サイズの大きい順に上位10ファイル
Get-ChildItem -Recurse -File |
  Sort-Object Length -Descending |
  Select-Object -First 10 FullName, Length
```

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] ファイル名で検索できる（find, Get-ChildItem）
- [ ] ファイル内容で検索できる（grep, Select-String）
- [ ] パイプラインで複数コマンドを連結できる
- [ ] ワイルドカードと正規表現を使い分けられる
- [ ] 検索結果を加工・集計できる

---

**前の章**: [01. 基礎編](../01_基礎編/README.md)
**次の章**: [03. 応用技術編](../03_応用技術編/README.md)
