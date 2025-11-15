# 09. リファレンス編 - コマンド一覧と用語集

## この章について

この章は、日常的に使うコマンドや用語を素早く調べるためのリファレンスです。ブックマークして、必要な時にすぐ参照できるようにしましょう。

---

## この章の内容

### 📘 [コマンド早見表](./command-cheatsheet.md)
- プラットフォーム別コマンド対照表
- 用途別コマンド分類
- よく使うオプション一覧

### 📗 [用語集](./glossary.md)
- CLI関連用語
- 技術用語の解説
- 略語の正式名称

### 📕 [学習リソース](./resources.md)
- 公式ドキュメント
- おすすめの学習サイト
- コミュニティ

---

## クイックリファレンス

### 基本コマンド対照表

| 機能 | Windows PowerShell | macOS/Linux | 説明 |
|------|-------------------|-------------|------|
| **ナビゲーション** |
| 現在位置 | `Get-Location`, `pwd` | `pwd` | 現在のディレクトリを表示 |
| 移動 | `Set-Location`, `cd` | `cd` | ディレクトリを移動 |
| ファイル一覧 | `Get-ChildItem`, `ls` | `ls` | ファイル・ディレクトリ一覧 |
| **ファイル操作** |
| 作成（ディレクトリ） | `New-Item -ItemType Directory`, `mkdir` | `mkdir` | ディレクトリを作成 |
| 作成（ファイル） | `New-Item -ItemType File` | `touch` | 空ファイルを作成 |
| コピー | `Copy-Item`, `cp` | `cp` | ファイルをコピー |
| 移動/リネーム | `Move-Item`, `mv` | `mv` | ファイルを移動/リネーム |
| 削除 | `Remove-Item`, `rm` | `rm` | ファイルを削除 |
| **ファイル内容** |
| 表示 | `Get-Content`, `cat` | `cat` | ファイル全体を表示 |
| ページ表示 | `more` | `less` | ページごとに表示 |
| 先頭 | `Get-Content -Head 10` | `head -n 10` | 先頭N行を表示 |
| 末尾 | `Get-Content -Tail 10` | `tail -n 10` | 末尾N行を表示 |
| **検索** |
| ファイル検索 | `Get-ChildItem -Recurse -Filter` | `find` | ファイル名で検索 |
| テキスト検索 | `Select-String` | `grep` | ファイル内容で検索 |
| **プロセス** |
| 一覧 | `Get-Process` | `ps` | プロセス一覧 |
| 終了 | `Stop-Process`, `kill` | `kill` | プロセスを終了 |
| **ネットワーク** |
| 接続確認 | `Test-Connection` | `ping` | ネットワーク接続確認 |
| ダウンロード | `Invoke-WebRequest`, `curl` | `curl`, `wget` | ファイルダウンロード |
| **システム** |
| ユーザー | `$env:USERNAME` | `whoami` | 現在のユーザー名 |
| 環境変数 | `$env:PATH` | `echo $PATH` | 環境変数を表示 |
| ディスク使用量 | `Get-PSDrive` | `df -h` | ディスク使用状況 |
| ヘルプ | `Get-Help`, `man` | `man` | コマンドのヘルプ |

---

## よく使うオプション

### ls / Get-ChildItem

```bash
# macOS/Linux
ls              # 基本の一覧
ls -l           # 詳細表示
ls -a           # 隠しファイルも表示
ls -lh          # 人間が読みやすい形式
ls -lt          # 更新日時順
ls -lS          # サイズ順

# Windows PowerShell
Get-ChildItem                  # 基本の一覧
Get-ChildItem -Force           # 隠しファイルも表示
Get-ChildItem -Recurse         # 再帰的に表示
Get-ChildItem -Filter "*.txt"  # パターンで絞り込み
```

### grep / Select-String

```bash
# macOS/Linux
grep "pattern" file.txt       # 基本の検索
grep -i "pattern" file.txt    # 大文字小文字を区別しない
grep -r "pattern" dir/        # 再帰検索
grep -n "pattern" file.txt    # 行番号付き
grep -v "pattern" file.txt    # 一致しない行

# Windows PowerShell
Select-String "pattern" file.txt
Select-String "pattern" file.txt -CaseSensitive
Get-ChildItem -Recurse | Select-String "pattern"
Select-String "pattern" file.txt | Select-Object LineNumber, Line
```

### find / Get-ChildItem

```bash
# macOS/Linux
find . -name "*.txt"           # ファイル名で検索
find . -type f -name "*.txt"   # ファイルのみ
find . -type d -name "test*"   # ディレクトリのみ
find . -mtime -7               # 過去7日間に変更
find . -size +1M               # 1MB以上

# Windows PowerShell
Get-ChildItem -Recurse -Filter "*.txt"
Get-ChildItem -Recurse -File -Filter "*.txt"
Get-ChildItem -Recurse -Directory -Filter "test*"
Get-ChildItem -Recurse | Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-7)}
Get-ChildItem -Recurse | Where-Object {$_.Length -gt 1MB}
```

---

## パイプライン便利パターン

### カウント

```bash
# macOS/Linux
ls | wc -l                     # ファイル数
grep "ERROR" log.txt | wc -l   # マッチ行数

# Windows PowerShell
Get-ChildItem | Measure-Object
Select-String "ERROR" log.txt | Measure-Object
```

### ソート

```bash
# macOS/Linux
ls -l | sort -k5 -n           # サイズでソート
cat file.txt | sort           # アルファベット順
cat file.txt | sort -u        # ユニーク値でソート

# Windows PowerShell
Get-ChildItem | Sort-Object Length
Get-Content file.txt | Sort-Object
Get-Content file.txt | Sort-Object -Unique
```

### 重複削除・集計

```bash
# macOS/Linux
cat file.txt | sort | uniq              # 重複削除
cat file.txt | sort | uniq -c           # 出現回数付き
cat file.txt | sort | uniq -c | sort -rn # 多い順

# Windows PowerShell
Get-Content file.txt | Sort-Object -Unique
Get-Content file.txt | Group-Object -NoElement
Get-Content file.txt | Group-Object -NoElement | Sort-Object Count -Descending
```

---

## 特殊変数

### Bash/Zsh

```bash
$HOME       # ホームディレクトリ
$USER       # ユーザー名
$PWD        # 現在のディレクトリ
$OLDPWD     # 前のディレクトリ
$PATH       # 実行ファイル検索パス
$?          # 直前のコマンドの終了ステータス
$$          # 現在のプロセスID
$!          # 最後のバックグラウンドプロセスID
$0          # スクリプト名
$1, $2, ... # コマンドライン引数
$@          # すべての引数
$#          # 引数の数
```

### PowerShell

```powershell
$HOME                  # ホームディレクトリ
$env:USERNAME          # ユーザー名
$PWD                   # 現在のディレクトリ
$env:PATH              # 実行ファイル検索パス
$?                     # 直前のコマンドの成功/失敗
$PID                   # 現在のプロセスID
$PSVersionTable        # PowerShell バージョン情報
$args                  # コマンドライン引数の配列
$args[0], $args[1]...  # 個別の引数
$args.Count            # 引数の数
```

---

## エスケープシーケンス

### 特殊文字

| 文字 | 意味 | エスケープ（bash） | エスケープ（PowerShell） |
|------|------|-------------------|----------------------|
| スペース | 区切り | `\ ` または `" "` | `" "` |
| `*` | ワイルドカード | `\*` | `` `* `` |
| `?` | 1文字マッチ | `\?` | `` `? `` |
| `$` | 変数 | `\$` | `` `$ `` |
| `&` | バックグラウンド | `\&` | `` `& `` |
| `;` | コマンド区切り | `\;` | `` `; `` |
| `\|` | パイプ | `\|` | `` `| `` |

---

## 終了ステータス

### 一般的な終了コード

| コード | 意味 |
|-------|------|
| 0 | 成功 |
| 1 | 一般的なエラー |
| 2 | コマンドの誤用 |
| 126 | 実行権限なし |
| 127 | コマンドが見つからない |
| 130 | Ctrl+C で中断 |

### 確認方法

```bash
# macOS/Linux
echo $?

# Windows PowerShell
$LASTEXITCODE
```

---

## 主要な環境変数

### Unix系（bash/zsh）

```bash
PATH        # コマンド検索パス
HOME        # ホームディレクトリ
USER        # ユーザー名
SHELL       # 使用中のシェル
EDITOR      # デフォルトエディタ
LANG        # ロケール設定
```

### Windows PowerShell

```powershell
$env:PATH           # コマンド検索パス
$env:USERPROFILE    # ユーザープロファイル
$env:USERNAME       # ユーザー名
$env:COMPUTERNAME   # コンピュータ名
$env:TEMP           # 一時ファイルディレクトリ
```

---

## 学習リソース

### 公式ドキュメント

**Windows PowerShell**:
- [PowerShell Documentation | Microsoft Learn](https://learn.microsoft.com/en-us/powershell/)

**macOS**:
- [Terminal User Guide | Apple](https://support.apple.com/guide/terminal/)

**Linux**:
- [The Linux Command Line (日本語版)](https://linuxcommand.org/)
- [GNU Bash Manual](https://www.gnu.org/software/bash/manual/)

### おすすめサイト

- [Zenn - CLI関連記事](https://zenn.dev/topics/cli)
- [Qiita - コマンドライン](https://qiita.com/tags/コマンドライン)
- [SS64 Command Line Reference](https://ss64.com/)

### インタラクティブ学習

- [Command Line Challenge](https://cmdchallenge.com/)
- [Terminus - CLIゲーム](http://www.mprat.org/Terminus/)

---

**前の章**: [08. プラットフォーム固有編](../08_プラットフォーム固有編/README.md)
**次の章**: [10. 演習編](../10_演習編/README.md)
