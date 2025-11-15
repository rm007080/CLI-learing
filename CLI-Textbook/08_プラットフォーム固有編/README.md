# 08. プラットフォーム固有編 - OS別の特殊機能とTips

## この章について

各OSには独自の強力な機能があります。この章では、プラットフォーム固有の便利な機能と、実務でよく遭遇する課題の解決方法を学びます。

### 学習目標

- Windows PowerShellの高度な機能
- macOS特有のツールとコマンド
- OneDrive/iCloud統合環境での操作
- クロスプラットフォーム開発のコツ

---

## この章で学ぶこと

### 📘 [Windows PowerShell 高度な機能](./windows-advanced.md)
- PowerShellオブジェクトパイプライン
- WMI/CIMによるシステム管理
- レジストリ操作
- Windows Terminal のカスタマイズ
- WSL（Windows Subsystem for Linux）連携

### 📗 [macOS 特有の機能](./macos-advanced.md)
- Spotlight検索とmdfind
- Automator連携
- AppleScript自動化
- ユニバーサルクリップボード
- Time Machine バックアップ制御

### 📕 [OneDrive/クラウド統合環境](./cloud-integration.md)
- OneDrive環境でのパス取得
- クラウド同期の考慮事項
- iCloud Drive操作
- Dropbox CLI

---

## Windows PowerShell 高度な機能

### PowerShellオブジェクトパイプライン

**bashとの違い**: PowerShellはテキストではなく**オブジェクト**を扱います

```powershell
# ファイルオブジェクトの詳細なフィルタリング
Get-ChildItem -Recurse |
  Where-Object {
    $_.Length -gt 1MB -and
    $_.LastWriteTime -gt (Get-Date).AddDays(-7)
  } |
  Sort-Object Length -Descending |
  Select-Object Name, Length, LastWriteTime |
  Format-Table -AutoSize
```

### WMI/CIMでシステム情報取得

```powershell
# システム情報
Get-CimInstance -ClassName Win32_OperatingSystem |
  Select-Object Caption, Version, OSArchitecture, LastBootUpTime

# ディスク情報
Get-CimInstance -ClassName Win32_LogicalDisk |
  Where-Object {$_.DriveType -eq 3} |
  Select-Object DeviceID,
    @{L='SizeGB';E={[math]::Round($_.Size/1GB,2)}},
    @{L='FreeGB';E={[math]::Round($_.FreeSpace/1GB,2)}}

# プロセス情報
Get-Process |
  Sort-Object CPU -Descending |
  Select-Object -First 10 Name, CPU, WorkingSet
```

### OneDrive環境でのパス取得

```powershell
# 環境に依存しないデスクトップパス取得
$Desktop = [Environment]::GetFolderPath("Desktop")
$Documents = [Environment]::GetFolderPath("MyDocuments")

# OneDrive パスの判定
if ($Desktop -match "OneDrive") {
    Write-Host "OneDrive 同期環境です"
}

# パスを変数に保存して使用
$BackupPath = Join-Path $Documents "Backups"
New-Item -ItemType Directory -Path $BackupPath -Force
```

**OneDrive環境のパスパターン**:
```powershell
# 標準環境
C:\Users\YourName\Desktop

# OneDrive Personal
C:\Users\YourName\OneDrive\Desktop

# OneDrive Business
C:\Users\YourName\OneDrive - CompanyName\Desktop

# 自動取得で対応
$Desktop = [Environment]::GetFolderPath("Desktop")
# → 環境に応じて適切なパスを返す
```

### WSL（Windows Subsystem for Linux）連携

```powershell
# PowerShell から Linux コマンド実行
wsl ls -la

# ファイルパス変換
# Windows → WSL
wsl wslpath 'C:\Users\Name\file.txt'
# → /mnt/c/Users/Name/file.txt

# WSL → Windows
wsl wslpath -w '/home/user/file.txt'
# → \\wsl$\Ubuntu\home\user\file.txt

# WSL とPowerShell のパイプライン連携
Get-ChildItem *.log | ForEach-Object {
    Get-Content $_.FullName | wsl grep "ERROR"
}
```

---

## macOS 特有の機能

### Spotlight検索 - mdfind

```bash
# Spotlightのインデックスを使った高速検索
mdfind "kind:pdf"
mdfind "kind:image date:today"
mdfind -name "report.pdf"

# 特定ディレクトリ内で検索
mdfind -onlyin ~/Documents "kind:pdf"

# メタデータで検索
mdfind "kMDItemAuthors == '*John*'"
```

### pbcopy/pbpaste - クリップボード操作

```bash
# コマンド出力をクリップボードにコピー
ls -la | pbcopy

# クリップボードの内容をファイルに保存
pbpaste > clipboard.txt

# ファイル内容をクリップボードにコピー
pbcopy < file.txt

# パイプライン内で使用
cat error.log | grep "ERROR" | pbcopy
```

### open - Finderとの連携

```bash
# Finderで開く
open .
open ~/Documents

# アプリケーションで開く
open -a Safari index.html
open -a "Visual Studio Code" project.py

# URLを開く
open https://example.com

# 複数ファイルを開く
open *.pdf
```

### macOS のシステム管理

```bash
# システム情報
system_profiler SPSoftwareDataType

# ディスク使用状況
diskutil list
diskutil info disk0

# Time Machine バックアップ
tmutil startbackup
tmutil stopbackup
tmutil listbackups
tmutil restore /path/to/file

# Wi-Fi情報
networksetup -listallhardwareports
networksetup -getairportnetwork en0
```

### AppleScript自動化

```bash
# AppleScript を実行
osascript -e 'display notification "Hello" with title "Greeting"'

# ファイルからスクリプト実行
osascript script.scpt

# Finderを制御
osascript -e 'tell application "Finder" to empty trash'
```

---

## Linux 固有の便利機能

### systemd サービス管理

```bash
# サービス状態確認
systemctl status nginx

# サービス起動/停止
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx

# 自動起動設定
sudo systemctl enable nginx
sudo systemctl disable nginx

# ログ確認
journalctl -u nginx -f
```

### ディストリビューション情報

```bash
# OSバージョン確認
cat /etc/os-release
lsb_release -a

# カーネルバージョン
uname -r

# ハードウェア情報
lshw
lscpu
lsblk
```

---

## クロスプラットフォーム対応

### パス区切り文字の統一

**Python**:
```python
import os
# 自動的にOSに合わせた区切り文字を使用
path = os.path.join("folder", "subfolder", "file.txt")
```

**Node.js**:
```javascript
const path = require('path');
// OSに依存しないパス結合
const filePath = path.join('folder', 'subfolder', 'file.txt');
```

**PowerShell**:
```powershell
# Join-Path でOSに合わせた区切り文字
$Path = Join-Path "folder" "subfolder" "file.txt"
```

### 改行コードの扱い

```bash
# Windows (CRLF) → Unix (LF)
dos2unix file.txt

# Unix (LF) → Windows (CRLF)
unix2dos file.txt

# Gitで自動変換
git config --global core.autocrlf true  # Windows
git config --global core.autocrlf input # macOS/Linux
```

---

## 便利な環境設定

### PowerShell Profile

```powershell
# プロファイルの場所
$PROFILE

# プロファイル作成/編集
New-Item -ItemType File -Path $PROFILE -Force
notepad $PROFILE

# プロファイル例
# エイリアス設定
Set-Alias ll Get-ChildItem
Set-Alias g git

# 関数定義
function cdp {
    Set-Location -Path ([Environment]::GetFolderPath("MyDocuments"))
}

# プロンプトカスタマイズ
function prompt {
    "PS $(Get-Location)> "
}
```

### Bash .bashrc/.zshrc

```bash
# .bashrc / .zshrc の場所
~/.bashrc  # bash
~/.zshrc   # zsh

# 編集
vim ~/.bashrc

# 設定例
# エイリアス
alias ll='ls -lah'
alias ..='cd ..'
alias gs='git status'

# 環境変数
export EDITOR=vim
export PATH="$HOME/bin:$PATH"

# 関数
cdp() {
    cd ~/Documents
}

# プロンプトカスタマイズ（bash）
PS1='\u@\h:\w\$ '

# 設定を再読み込み
source ~/.bashrc
```

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] PowerShellオブジェクトパイプラインを使える
- [ ] OneDrive環境で適切なパスを取得できる
- [ ] macOS固有のコマンド（open, pbcopy, mdfind）を使える
- [ ] クロスプラットフォーム対応のスクリプトを書ける
- [ ] プロファイル/設定ファイルをカスタマイズできる

---

**前の章**: [07. AI時代のCLI編](../07_AI時代のCLI編/README.md)
**次の章**: [09. リファレンス編](../09_リファレンス編/README.md)
