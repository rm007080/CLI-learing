# 05. ツール&ユーティリティ編 - 生産性を高めるツール群

## この章について

CLIの世界には、作業を効率化する強力なツールが数多く存在します。この章では、プロの開発者が日常的に使うツールを学びます。

### 学習目標

- テキスト処理の自動化
- バージョン管理システムの活用
- パッケージマネージャーの使いこなし
- 開発ツールとの連携

---

## この章で学ぶこと

### 📘 [テキスト処理ツール](./text-processing.md)
- `grep`: パターン検索
- `sed`: ストリーム編集
- `awk`: テキスト処理言語
- `cut`, `sort`, `uniq`: データ加工
- PowerShellの`Select-String`, `-replace`

### 📗 [Git - バージョン管理](./version-control-git.md)
- Gitの基本概念
- リポジトリの作成と操作
- コミット、ブランチ、マージ
- リモートリポジトリとの連携
- よく使うコマンド

### 📕 [パッケージマネージャー](./package-managers.md)
- **Linux**: apt, dnf, pacman
- **macOS**: Homebrew
- **Windows**: winget, Chocolatey, Scoop
- **言語別**: npm, pip, cargo, gem

### 📙 [効率化ツール](./productivity-tools.md)
- `tmux` / `screen`: ターミナルマルチプレクサ
- `fzf`: あいまい検索
- `jq`: JSON処理
- `curl` / `wget`: ファイル転送
- モダンCLIツール（ripgrep, fd, batなど）

---

## テキスト処理の実例

### grep: ログからエラーを抽出

```bash
# ERRORを含む行を表示
grep "ERROR" application.log

# 大文字小文字を区別しない
grep -i "error" application.log

# 行番号付きで表示
grep -n "ERROR" application.log

# 再帰検索（ディレクトリ内すべて）
grep -r "TODO" src/

# 特定の拡張子のみ
grep -r "TODO" --include="*.js" src/
```

### sed: テキストの置換

```bash
# 最初の出現を置換
sed 's/old/new/' file.txt

# すべての出現を置換
sed 's/old/new/g' file.txt

# ファイルを直接編集
sed -i 's/old/new/g' file.txt

# 複数の置換
sed 's/foo/bar/g; s/baz/qux/g' file.txt
```

### awk: データの抽出と集計

```bash
# 2列目だけ表示
awk '{print $2}' data.txt

# 条件に一致する行のみ処理
awk '$3 > 100 {print $1, $3}' data.txt

# 合計を計算
awk '{sum += $2} END {print "Total:", sum}' data.txt
```

---

## Git の基本操作

### リポジトリの初期化と基本操作

```bash
# リポジトリ作成
git init

# ファイルをステージング
git add file.txt
git add .  # すべてのファイル

# コミット
git commit -m "Initial commit"

# 状態確認
git status

# 履歴表示
git log
git log --oneline --graph
```

### ブランチ操作

```bash
# ブランチ作成
git branch feature-x

# ブランチ切り替え
git checkout feature-x
# または（新しい方法）
git switch feature-x

# ブランチ作成と切り替えを同時に
git checkout -b feature-x
# または
git switch -c feature-x

# ブランチ一覧
git branch

# マージ
git checkout main
git merge feature-x
```

### リモートリポジトリ

```bash
# リモート追加
git remote add origin https://github.com/user/repo.git

# プッシュ
git push -u origin main

# プル
git pull origin main

# クローン
git clone https://github.com/user/repo.git
```

---

## パッケージマネージャーの使い方

### Homebrew（macOS）

```bash
# インストール
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# パッケージ検索
brew search vim

# パッケージインストール
brew install vim

# パッケージ更新
brew upgrade vim

# インストール済みパッケージ一覧
brew list
```

### apt（Ubuntu/Debian）

```bash
# パッケージリスト更新
sudo apt update

# パッケージインストール
sudo apt install vim

# パッケージ削除
sudo apt remove vim

# システム全体のアップグレード
sudo apt upgrade
```

### winget（Windows）

```powershell
# パッケージ検索
winget search vim

# パッケージインストール
winget install vim.vim

# パッケージアップグレード
winget upgrade vim.vim

# インストール済みパッケージ一覧
winget list
```

---

## モダンCLIツール

### より高速・使いやすい代替ツール

| 従来のツール | モダンな代替 | 特徴 |
|-------------|-------------|------|
| `grep` | `ripgrep` (rg) | 超高速、デフォルトで再帰検索 |
| `find` | `fd` | シンプルな構文、高速 |
| `cat` | `bat` | シンタックスハイライト |
| `ls` | `exa` / `lsd` | カラフル、アイコン表示 |
| `du` | `dust` | 視覚的なサイズ表示 |
| `top` | `htop` / `btop` | インタラクティブ |

### インストール例

```bash
# macOS
brew install ripgrep fd bat exa

# Ubuntu/Debian
sudo apt install ripgrep fd-find bat

# Windows
winget install BurntSushi.ripgrep.MSVC
winget install sharkdp.fd
winget install sharkdp.bat
```

### 使用例

```bash
# ripgrep: 超高速検索
rg "TODO" src/

# fd: ファイル検索
fd "\.txt$"

# bat: シンタックスハイライト付き表示
bat script.py
```

---

## JSON処理 - jq

### jqの基本

```bash
# JSONを整形
echo '{"name":"Alice","age":30}' | jq .

# 特定のフィールドを抽出
echo '{"name":"Alice","age":30}' | jq '.name'

# 配列の処理
echo '[{"name":"Alice"},{"name":"Bob"}]' | jq '.[0].name'

# APIレスポンスの処理
curl -s https://api.github.com/users/github | jq '.name, .public_repos'
```

---

## curlとwget

### curl: データ転送

```bash
# ファイルダウンロード
curl -O https://example.com/file.zip

# APIリクエスト
curl https://api.github.com/users/github

# POSTリクエスト
curl -X POST -H "Content-Type: application/json" \
  -d '{"key":"value"}' \
  https://api.example.com/endpoint

# ヘッダー表示
curl -I https://example.com
```

### wget: ファイルダウンロード

```bash
# ファイルダウンロード
wget https://example.com/file.zip

# 再帰ダウンロード
wget -r -np -k https://example.com/docs/

# バックグラウンドでダウンロード
wget -b https://example.com/largefile.iso
```

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] grepでテキスト検索ができる
- [ ] sed/awkで基本的なテキスト処理ができる
- [ ] Gitの基本操作ができる（add, commit, push, pull）
- [ ] パッケージマネージャーでツールをインストールできる
- [ ] jqでJSON処理ができる
- [ ] curlでAPIリクエストができる

---

**前の章**: [04. シェルスクリプティング編](../04_シェルスクリプティング編/README.md)
**次の章**: [06. セキュリティ編](../06_セキュリティ編/README.md)
