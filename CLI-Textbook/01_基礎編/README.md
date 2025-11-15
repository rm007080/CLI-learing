# 01. 基礎編 - プラットフォーム別CLI入門

## この章について

この章では、Windows、macOS、Linuxの各プラットフォームで実際にCLIを使い始める方法を学びます。

### 各プラットフォームの標準シェル

| プラットフォーム | 標準シェル | ターミナル |
|-----------------|-----------|-----------|
| **Windows** | PowerShell | PowerShell / Windows Terminal |
| **macOS** | zsh | Terminal.app |
| **Linux** | bash | GNOME Terminal / Konsoleなど |

### 学習の進め方

1. **自分のOSに該当するセクション**から始めてください
2. **他のOSのセクション**も読むと、違いを理解できます
3. **実際に手を動かして**練習しましょう
4. **練習用フォルダ**を作って、安全に実験してください

### ⚠️ 重要な注意事項

- **練習用フォルダを作成**してから実験する
- **大切なファイルがある場所**では試さない
- **削除系のコマンド**は特に慎重に
- **確認オプション**（-Confirm、-iなど）を活用する

---

## この章で学ぶこと

### 📘 [Windows PowerShell 基礎](./windows-powershell-basics.md)
- PowerShellの起動方法
- 基本的なコマンド（Get-ChildItem、Set-Locationなど）
- ファイル・ディレクトリ操作
- Tab補完とワイルドカード
- エクスプローラーとの連携

### 📗 [macOS Terminal 基礎](./macos-terminal-basics.md)
- Terminalの起動方法
- 基本的なコマンド（ls、cd、pwdなど）
- ファイル・ディレクトリ操作
- Tab補完とワイルドカード
- Finderとの連携
- クリップボード操作（pbcopy、pbpaste）

### 📕 [Linux Bash 基礎](./linux-bash-basics.md)
- Bashシェルの基本
- 基本的なコマンド（ls、cd、pwdなど）
- ファイル・ディレクトリ操作
- パーミッション（権限）の基礎
- パッケージマネージャーの紹介

---

## 共通する基本概念

### ディレクトリ構造

すべてのOSで、ディレクトリは**木構造**（ツリー構造）になっています：

```
ルート（/またはC:\）
├── Users（または/home）
│   ├── YourName
│   │   ├── Desktop
│   │   ├── Documents
│   │   └── Downloads
│   └── OtherUser
└── Program Files（または/usr）
```

### パス（Path）

ファイルやディレクトリの場所を示す住所のようなものです。

#### 絶対パス
ルートからの完全な道順：
- **Windows**: `C:\Users\YourName\Desktop\file.txt`
- **macOS/Linux**: `/Users/YourName/Desktop/file.txt`

#### 相対パス
現在位置からの道順：
- `./file.txt`（現在のディレクトリのfile.txt）
- `../file.txt`（一つ上のディレクトリのfile.txt）

### 特殊な記号

| 記号 | 意味 | 例 |
|------|------|-----|
| `.` | 現在のディレクトリ | `./script.sh` |
| `..` | 一つ上のディレクトリ | `cd ..` |
| `~` | ホームディレクトリ | `cd ~` |
| `/` または `\` | パス区切り文字 | `C:\Users\Name` |
| `*` | ワイルドカード（任意の文字列） | `*.txt` |

---

## 各プラットフォームの違い

### コマンド名の違い

同じ機能でも、コマンド名が異なることがあります：

| 機能 | Windows PowerShell | macOS/Linux |
|------|-------------------|-------------|
| ファイル一覧 | `Get-ChildItem` / `ls` | `ls` |
| 場所移動 | `Set-Location` / `cd` | `cd` |
| 現在位置 | `Get-Location` / `pwd` | `pwd` |
| コピー | `Copy-Item` / `cp` | `cp` |
| 削除 | `Remove-Item` / `rm` | `rm` |
| ファイル内容表示 | `Get-Content` / `cat` | `cat` |

PowerShellは「動詞-名詞」形式のコマンドと、短縮エイリアスの両方をサポートしています。

### パス区切り文字

- **Windows**: バックスラッシュ `\`（例：`C:\Users\Name`）
- **macOS/Linux**: スラッシュ `/`（例：`/Users/Name`）

PowerShellでは両方の区切り文字が使えます。

### 改行コード

- **Windows**: CRLF（`\r\n`）
- **macOS/Linux**: LF（`\n`）

テキストファイルを扱うときに注意が必要です。

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] ターミナル/PowerShellを起動できる
- [ ] 現在位置を確認できる（pwd）
- [ ] ディレクトリを移動できる（cd）
- [ ] ファイル一覧を表示できる（ls）
- [ ] ディレクトリを作成できる（mkdir）
- [ ] ファイルをコピーできる（cp）
- [ ] ファイルを削除できる（rm）
- [ ] Tab補完を使える
- [ ] ワイルドカード（*）を使える

---

**前の章**: [00. 序章](../00_序章/README.md)
**次の章**: [02. ファイル操作編](../02_ファイル操作編/README.md)
