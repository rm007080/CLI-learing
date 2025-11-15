# 10. 演習編 - 実践問題でスキルを磨く

## この章について

これまで学んだCLIスキルを、実際の問題を解きながら定着させましょう。初級から上級まで、段階的に難易度が上がる演習問題を用意しています。

### 演習の進め方

1. **まず自分で考える**: すぐに答えを見ない
2. **複数の解法を試す**: 一つの答えだけでなく、別の方法も考えてみる
3. **実際に実行する**: 練習環境で動作確認する
4. **理解を深める**: なぜそのコマンドが必要か考える

---

## この章の内容

### 📘 [初級演習](./beginner.md)
基本的なコマンド操作（ファイル操作、ナビゲーション、基本的なパイプライン）

### 📗 [中級演習](./intermediate.md)
複雑なパイプライン、スクリプト作成、テキスト処理

### 📕 [上級演習](./advanced.md)
実務的なシナリオ、自動化、総合問題

---

## 初級演習（サンプル）

### 問題1: ファイルとディレクトリの基本操作

**タスク**:
1. `cli-practice`という名前のディレクトリを作成
2. その中に`test1.txt`, `test2.txt`, `test3.txt` を作成
3. `test1.txt`に「Hello World」と書き込む
4. すべての.txtファイルを`backup`というディレクトリにコピー
5. ファイル一覧を確認

<details>
<summary>解答例（macOS/Linux）</summary>

```bash
# 1. ディレクトリ作成
mkdir cli-practice
cd cli-practice

# 2. ファイル作成
touch test1.txt test2.txt test3.txt

# 3. ファイルに書き込み
echo "Hello World" > test1.txt

# 4. バックアップディレクトリ作成とコピー
mkdir backup
cp *.txt backup/

# 5. 確認
ls -la
ls -la backup/
```
</details>

<details>
<summary>解答例（Windows PowerShell）</summary>

```powershell
# 1. ディレクトリ作成
New-Item -ItemType Directory -Name "cli-practice"
Set-Location cli-practice

# 2. ファイル作成
New-Item -ItemType File test1.txt, test2.txt, test3.txt

# 3. ファイルに書き込み
"Hello World" | Out-File test1.txt

# 4. バックアップディレクトリ作成とコピー
New-Item -ItemType Directory -Name "backup"
Copy-Item *.txt backup/

# 5. 確認
Get-ChildItem
Get-ChildItem backup/
```
</details>

---

### 問題2: ファイル検索

**タスク**:
現在のディレクトリ以下で、.txtファイルを全て見つけて、ファイル名とサイズを表示してください。

<details>
<summary>解答例（macOS/Linux）</summary>

```bash
find . -name "*.txt" -type f -exec ls -lh {} + | awk '{print $9, $5}'

# または
find . -name "*.txt" -type f -ls
```
</details>

<details>
<summary>解答例（Windows PowerShell）</summary>

```powershell
Get-ChildItem -Recurse -Filter "*.txt" -File |
  Select-Object Name, @{L='SizeKB';E={[math]::Round($_.Length/1KB,2)}}
```
</details>

---

## 中級演習（サンプル）

### 問題3: ログファイル分析

**シナリオ**:
`application.log`というログファイルがあります。以下の情報を抽出してください。

```
2025-11-14 10:15:32 INFO User logged in: alice
2025-11-14 10:16:45 ERROR Database connection failed
2025-11-14 10:17:12 INFO User logged in: bob
2025-11-14 10:18:33 ERROR Database connection failed
2025-11-14 10:19:55 WARN Low memory: 85% used
2025-11-14 10:20:12 ERROR Database connection failed
2025-11-14 10:21:30 INFO User logged out: alice
```

**タスク**:
1. ERRORの行のみ抽出
2. ERRORの種類ごとに集計（出現回数を数える）
3. 多い順に表示

<details>
<summary>解答例（macOS/Linux）</summary>

```bash
# 1. ERRORの行を抽出
grep "ERROR" application.log

# 2. & 3. 集計して多い順に表示
grep "ERROR" application.log |
  awk '{$1=$2=$3=""; print $0}' |
  sort |
  uniq -c |
  sort -rn
```

**結果**:
```
      3 Database connection failed
```
</details>

<details>
<summary>解答例（Windows PowerShell）</summary>

```powershell
# 1. ERRORの行を抽出
Select-String "ERROR" application.log

# 2. & 3. 集計して多い順に表示
Select-String "ERROR" application.log |
  ForEach-Object { $_.Line -replace '^\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2} ERROR ', '' } |
  Group-Object -NoElement |
  Sort-Object Count -Descending
```

**結果**:
```
Count Name
----- ----
    3 Database connection failed
```
</details>

---

### 問題4: バックアップスクリプト

**タスク**:
以下の要件を満たすバックアップスクリプトを作成してください：

1. `~/Documents`をバックアップ
2. バックアップ先は`~/Backups`
3. ファイル名は`backup_YYYYMMDD_HHMMSS.tar.gz`（またはzip）
4. 7日以上前のバックアップは自動削除

<details>
<summary>解答例（bash）</summary>

```bash
#!/bin/bash

# 設定
SOURCE="$HOME/Documents"
BACKUP_DIR="$HOME/Backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$TIMESTAMP.tar.gz"
DAYS_TO_KEEP=7

# バックアップディレクトリ作成
mkdir -p "$BACKUP_DIR"

# バックアップ実行
echo "バックアップ開始: $(date)"
tar czf "$BACKUP_DIR/$BACKUP_FILE" "$SOURCE"
echo "バックアップ完了: $BACKUP_FILE"

# 古いバックアップを削除
echo "古いバックアップを削除中..."
find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +$DAYS_TO_KEEP -delete
echo "完了"

# 実行方法:
# chmod +x backup.sh
# ./backup.sh
```
</details>

<details>
<summary>解答例（PowerShell）</summary>

```powershell
# backup.ps1

# 設定
$Source = "$env:USERPROFILE\Documents"
$BackupDir = "$env:USERPROFILE\Backups"
$Timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$BackupFile = "backup_$Timestamp.zip"
$DaysToKeep = 7

# バックアップディレクトリ作成
if (-not (Test-Path $BackupDir)) {
    New-Item -ItemType Directory -Path $BackupDir | Out-Null
}

# バックアップ実行
Write-Host "バックアップ開始: $(Get-Date)"
Compress-Archive -Path $Source -DestinationPath "$BackupDir\$BackupFile"
Write-Host "バックアップ完了: $BackupFile"

# 古いバックアップを削除
Write-Host "古いバックアップを削除中..."
$CutoffDate = (Get-Date).AddDays(-$DaysToKeep)
Get-ChildItem -Path $BackupDir -Filter "backup_*.zip" |
    Where-Object { $_.LastWriteTime -lt $CutoffDate } |
    Remove-Item -Force
Write-Host "完了"

# 実行方法:
# .\backup.ps1
```
</details>

---

## 上級演習（サンプル）

### 問題5: ファイル整理自動化

**シナリオ**:
Downloadsフォルダが乱雑になっています。ファイルを自動的に整理するスクリプトを作成してください。

**要件**:
1. 拡張子ごとにフォルダ分け
   - 画像: Images/ (.jpg, .png, .gif)
   - ドキュメント: Documents/ (.pdf, .docx, .xlsx)
   - アーカイブ: Archives/ (.zip, .tar.gz)
   - その他: Others/
2. 30日以上前のファイルは Old/ フォルダへ
3. 移動前に確認メッセージ表示（オプション）
4. ドライラン機能（実際には移動せず、何が起こるか表示）

<details>
<summary>解答例（bash）</summary>

```bash
#!/bin/bash

set -euo pipefail

# 設定
TARGET_DIR="${1:-$HOME/Downloads}"
DRY_RUN=${2:-false}
DAYS_OLD=30

# 拡張子マッピング
declare -A FOLDER_MAP=(
    ["jpg"]="Images"
    ["png"]="Images"
    ["gif"]="Images"
    ["pdf"]="Documents"
    ["docx"]="Documents"
    ["xlsx"]="Documents"
    ["zip"]="Archives"
    ["gz"]="Archives"
)

echo "ファイル整理スクリプト"
echo "対象ディレクトリ: $TARGET_DIR"
echo "ドライラン: $DRY_RUN"
echo "---"

# 古いファイル用のフォルダ作成
if [ "$DRY_RUN" != "true" ]; then
    mkdir -p "$TARGET_DIR/Old"
fi

# ファイルを処理
find "$TARGET_DIR" -maxdepth 1 -type f | while read -r file; do
    filename=$(basename "$file")
    extension="${filename##*.}"
    extension_lower=$(echo "$extension" | tr '[:upper:]' '[:lower:]')

    # ファイルの日付チェック
    file_date=$(stat -c %Y "$file" 2>/dev/null || stat -f %m "$file")
    current_date=$(date +%s)
    days_diff=$(( (current_date - file_date) / 86400 ))

    # 30日以上前のファイル
    if [ $days_diff -gt $DAYS_OLD ]; then
        echo "[$DRY_RUN] 移動: $filename → Old/ (${days_diff}日前)"
        if [ "$DRY_RUN" != "true" ]; then
            mv "$file" "$TARGET_DIR/Old/"
        fi
        continue
    fi

    # 拡張子で分類
    if [[ -v FOLDER_MAP[$extension_lower] ]]; then
        dest_folder="${FOLDER_MAP[$extension_lower]}"
        echo "[$DRY_RUN] 移動: $filename → $dest_folder/"

        if [ "$DRY_RUN" != "true" ]; then
            mkdir -p "$TARGET_DIR/$dest_folder"
            mv "$file" "$TARGET_DIR/$dest_folder/"
        fi
    else
        echo "[$DRY_RUN] 移動: $filename → Others/"
        if [ "$DRY_RUN" != "true" ]; then
            mkdir -p "$TARGET_DIR/Others"
            mv "$file" "$TARGET_DIR/Others/"
        fi
    fi
done

echo "---"
echo "整理完了"

# 実行方法:
# ./organize.sh                  # 通常実行
# ./organize.sh ~/Downloads true  # ドライラン
```
</details>

---

### 問題6: システムレポート生成

**タスク**:
システムの状態をまとめたレポートを生成するスクリプトを作成してください。

**含める情報**:
- システム情報（OS、バージョン、アーキテクチャ）
- ディスク使用状況
- メモリ使用状況
- CPU使用率の高いプロセス TOP 5
- ネットワーク接続状態
- 現在の日時

<details>
<summary>解答例（bash）</summary>

```bash
#!/bin/bash

OUTPUT_FILE="system_report_$(date +%Y%m%d_%H%M%S).txt"

{
    echo "================================"
    echo "システムレポート"
    echo "生成日時: $(date)"
    echo "================================"
    echo ""

    echo "--- システム情報 ---"
    uname -a
    echo ""

    echo "--- ディスク使用状況 ---"
    df -h | grep -v tmpfs
    echo ""

    echo "--- メモリ使用状況 ---"
    free -h
    echo ""

    echo "--- CPU使用率 TOP 5 ---"
    ps aux | sort -k3 -rn | head -6
    echo ""

    echo "--- ネットワーク接続 ---"
    netstat -tuln | head -20
    echo ""

} > "$OUTPUT_FILE"

echo "レポート生成完了: $OUTPUT_FILE"
cat "$OUTPUT_FILE"
```
</details>

<details>
<summary>解答例（PowerShell）</summary>

```powershell
$OutputFile = "system_report_$(Get-Date -Format 'yyyyMMdd_HHmmss').txt"

$Report = @"
================================
システムレポート
生成日時: $(Get-Date)
================================

--- システム情報 ---
$(Get-CimInstance -ClassName Win32_OperatingSystem | Select-Object Caption, Version, OSArchitecture | Format-List | Out-String)

--- ディスク使用状況 ---
$(Get-PSDrive -PSProvider FileSystem | Where-Object {$_.Used -ne $null} | Format-Table Name, @{L='UsedGB';E={[math]::Round($_.Used/1GB,2)}}, @{L='FreeGB';E={[math]::Round($_.Free/1GB,2)}} | Out-String)

--- メモリ使用状況 ---
$(Get-CimInstance -ClassName Win32_OperatingSystem | Select-Object @{L='TotalGB';E={[math]::Round($_.TotalVisibleMemorySize/1MB,2)}}, @{L='FreeGB';E={[math]::Round($_.FreePhysicalMemory/1MB,2)}} | Format-List | Out-String)

--- CPU使用率 TOP 5 ---
$(Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU, @{L='MemMB';E={[math]::Round($_.WorkingSet/1MB,2)}} | Format-Table | Out-String)

--- ネットワーク接続 ---
$(Get-NetTCPConnection | Where-Object {$_.State -eq 'Established'} | Select-Object -First 10 LocalAddress, LocalPort, RemoteAddress, RemotePort | Format-Table | Out-String)

"@

$Report | Out-File $OutputFile

Write-Host "レポート生成完了: $OutputFile"
Get-Content $OutputFile
```
</details>

---

## チャレンジ問題

### 問題7: 総合演習 - プロジェクト構造分析

**シナリオ**:
あるプロジェクトのディレクトリ構造を分析し、統計情報を出力してください。

**出力する情報**:
1. ファイル数（拡張子別）
2. 総行数（コードファイルのみ）
3. ディレクトリツリー図
4. 最大ファイルサイズとそのファイル名
5. 最近更新されたファイル TOP 5

**ヒント**:
- `find`, `wc`, `sort`, `awk`などを組み合わせる
- PowerShellなら`Get-ChildItem`, `Measure-Object`, `Group-Object`など

---

## 学習の確認

すべての演習を終えたら、以下を確認してください：

- [ ] 基本的なファイル操作が自在にできる
- [ ] パイプラインで複数のコマンドを連結できる
- [ ] テキスト処理（grep, sed, awk）ができる
- [ ] シェルスクリプトを書ける
- [ ] エラーハンドリングができる
- [ ] 実務的な自動化スクリプトを作成できる

---

## 次のステップ

演習を通じてCLIスキルが身についたら、以下にチャレンジしてみましょう：

1. **自分の作業を自動化**: 日常の繰り返し作業をスクリプト化
2. **オープンソースに貢献**: GitHubのプロジェクトでCLIスキルを活用
3. **さらに学ぶ**: sed/awk、正規表現、高度なシェルスクリプティング
4. **AIツールと連携**: 学んだCLIスキルでAIを効果的に活用

---

**おめでとうございます！CLI教科書を完走しました！** 🎉

これからも継続的に練習して、スキルを磨き続けてください。

**前の章**: [09. リファレンス編](../09_リファレンス編/README.md)
**教科書のトップ**: [CLI教科書 README](../README.md)
