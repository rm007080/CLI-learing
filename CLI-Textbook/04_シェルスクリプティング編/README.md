# 04. シェルスクリプティング編 - 自動化の技術

## この章について

これまで学んだコマンドを組み合わせて、**再利用可能なスクリプト**を作成します。スクリプトは一度書けば何度でも実行でき、作業を大幅に効率化できます。

### 学習目標

- 実用的なシェルスクリプトの作成
- PowerShellスクリプトの作成
- エラーハンドリングとデバッグ
- スクリプトのベストプラクティス

---

## この章で学ぶこと

### 📘 [Bash スクリプティング](./bash-scripting.md)
- シェバン（#!）とスクリプトの実行
- コマンドライン引数の処理
- 関数の定義と呼び出し
- エラーハンドリング（trap、set -e）
- 実践例：バックアップスクリプト、ログ分析

### 📗 [PowerShell スクリプティング](./powershell-scripting.md)
- .ps1ファイルの作成と実行
- パラメータの定義
- 関数とモジュール
- エラーハンドリング（try-catch）
- 実践例：ファイル整理、レポート生成

### 📕 [スクリプティングのベストプラクティス](./scripting-best-practices.md)
- 読みやすいコードの書き方
- コメントとドキュメント
- エラーメッセージの設計
- セキュリティ considerations
- テストとデバッグ

---

## 実用的なスクリプト例

### 例1: 自動バックアップスクリプト

**bash（backup.sh）**:
```bash
#!/bin/bash
# 日次バックアップスクリプト

set -euo pipefail  # エラー時に停止

# 設定
SOURCE_DIR="$HOME/Documents"
BACKUP_DIR="$HOME/Backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_$TIMESTAMP.tar.gz"
DAYS_TO_KEEP=7

# バックアップディレクトリ作成
mkdir -p "$BACKUP_DIR"

# バックアップ実行
echo "バックアップ開始: $(date)"
tar czf "$BACKUP_DIR/$BACKUP_FILE" "$SOURCE_DIR"
echo "バックアップ完了: $BACKUP_FILE"

# 古いバックアップを削除
echo "古いバックアップを削除中..."
find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +$DAYS_TO_KEEP -delete
echo "完了"
```

**PowerShell（Backup.ps1）**:
```powershell
#!/usr/bin/env pwsh
# 日次バックアップスクリプト

[CmdletBinding()]
param(
    [Parameter()]
    [string]$SourceDir = "$env:USERPROFILE\Documents",

    [Parameter()]
    [string]$BackupDir = "$env:USERPROFILE\Backups",

    [Parameter()]
    [int]$DaysToKeep = 7
)

$ErrorActionPreference = "Stop"

# バックアップディレクトリ作成
if (-not (Test-Path $BackupDir)) {
    New-Item -ItemType Directory -Path $BackupDir | Out-Null
}

# バックアップ実行
$Timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$BackupFile = "backup_$Timestamp.zip"

Write-Host "バックアップ開始: $(Get-Date)"
Compress-Archive -Path $SourceDir -DestinationPath "$BackupDir\$BackupFile"
Write-Host "バックアップ完了: $BackupFile"

# 古いバックアップを削除
Write-Host "古いバックアップを削除中..."
$CutoffDate = (Get-Date).AddDays(-$DaysToKeep)
Get-ChildItem -Path $BackupDir -Filter "backup_*.zip" |
    Where-Object { $_.LastWriteTime -lt $CutoffDate } |
    Remove-Item -Force

Write-Host "完了"
```

### 例2: ログ分析スクリプト

**bash（analyze_log.sh）**:
```bash
#!/bin/bash
# ログファイルからエラーを分析

LOG_FILE="${1:-application.log}"
OUTPUT_FILE="error_report_$(date +%Y%m%d).txt"

if [ ! -f "$LOG_FILE" ]; then
    echo "エラー: ログファイル '$LOG_FILE' が見つかりません" >&2
    exit 1
fi

echo "ログ分析レポート" > "$OUTPUT_FILE"
echo "生成日時: $(date)" >> "$OUTPUT_FILE"
echo "対象ファイル: $LOG_FILE" >> "$OUTPUT_FILE"
echo "================================" >> "$OUTPUT_FILE"
echo "" >> "$OUTPUT_FILE"

echo "エラー発生回数（上位10件）:" >> "$OUTPUT_FILE"
grep "ERROR" "$LOG_FILE" | sort | uniq -c | sort -rn | head -10 >> "$OUTPUT_FILE"

echo "" >> "$OUTPUT_FILE"
echo "警告発生回数（上位10件）:" >> "$OUTPUT_FILE"
grep "WARN" "$LOG_FILE" | sort | uniq -c | sort -rn | head -10 >> "$OUTPUT_FILE"

echo "レポート生成完了: $OUTPUT_FILE"
```

### 例3: ファイル整理スクリプト

**PowerShell（Organize-Files.ps1）**:
```powershell
#!/usr/bin/env pwsh
# ファイルを拡張子ごとにフォルダ分け

[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string]$TargetDir
)

if (-not (Test-Path $TargetDir)) {
    Write-Error "ディレクトリが見つかりません: $TargetDir"
    exit 1
}

# 拡張子ごとのフォルダマッピング
$FolderMap = @{
    '.jpg'  = 'Images'
    '.png'  = 'Images'
    '.gif'  = 'Images'
    '.pdf'  = 'Documents'
    '.docx' = 'Documents'
    '.xlsx' = 'Documents'
    '.zip'  = 'Archives'
    '.tar'  = 'Archives'
    '.mp3'  = 'Music'
    '.mp4'  = 'Videos'
}

Get-ChildItem -Path $TargetDir -File | ForEach-Object {
    $Extension = $_.Extension.ToLower()

    if ($FolderMap.ContainsKey($Extension)) {
        $DestFolder = Join-Path $TargetDir $FolderMap[$Extension]

        # フォルダ作成
        if (-not (Test-Path $DestFolder)) {
            New-Item -ItemType Directory -Path $DestFolder | Out-Null
        }

        # ファイル移動
        Move-Item -Path $_.FullName -Destination $DestFolder
        Write-Host "移動: $($_.Name) → $($FolderMap[$Extension])/"
    }
}

Write-Host "整理完了"
```

---

## スクリプト実行の準備

### Bash スクリプトの実行権限

```bash
# 実行権限を付与
chmod +x script.sh

# 実行
./script.sh
```

### PowerShell 実行ポリシーの設定

```powershell
# 実行ポリシーを確認
Get-ExecutionPolicy

# 現在のユーザーのみ許可（推奨）
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned

# スクリプト実行
.\script.ps1
```

---

## デバッグのコツ

### Bash でのデバッグ

```bash
# デバッグモードで実行（各コマンドを表示）
bash -x script.sh

# スクリプト内でデバッグモードを有効化
set -x

# 特定の箇所だけデバッグ
set -x
# デバッグしたいコマンド
set +x
```

### PowerShell でのデバッグ

```powershell
# 詳細出力を有効化
$VerbosePreference = "Continue"

# デバッグ出力を有効化
$DebugPreference = "Continue"

# スクリプト内でのデバッグ
Write-Verbose "変数の値: $MyVar"
Write-Debug "ここを通過"
```

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] Bash/PowerShellスクリプトを作成できる
- [ ] コマンドライン引数を受け取れる
- [ ] 関数を定義して再利用できる
- [ ] エラーハンドリングができる
- [ ] スクリプトをデバッグできる
- [ ] 実用的な自動化スクリプトを書ける

---

**前の章**: [03. 応用技術編](../03_応用技術編/README.md)
**次の章**: [05. ツール&ユーティリティ編](../05_ツール_ユーティリティ編/README.md)
