# 03. 応用技術編 - パイプライン、変数、スクリプト基礎

## この章について

CLIの真の力は、コマンドを組み合わせて複雑な処理を実現できることです。この章では、効率的なコマンド操作の核心となる技術を学びます。

### 学習目標

- パイプラインとリダイレクションの完全理解
- 変数と環境変数の活用
- スクリプトの基礎
- エラーハンドリング

---

## この章で学ぶこと

### 📘 [パイプラインとリダイレクション](./pipes-and-redirection.md)
- パイプ（|）: コマンドの連結
- リダイレクト（>、>>、<）: 入出力の制御
- 標準出力、標準エラー出力
- プロセス置換

### 📗 [変数と環境変数](./variables-and-environment.md)
- 変数の定義と使用
- 環境変数の役割
- PATH の設定
- プラットフォーム別の違い

### 📕 [スクリプト入門](./scripting-basics.md)
- シェルスクリプトの基本構造
- 条件分岐（if文）
- ループ（for, while）
- 関数の定義

---

## パイプラインの威力

### 実例: ログファイルからエラーを分析

**macOS/Linux**:
```bash
# ログからERRORを抽出 → 重複削除して集計 → 多い順に表示
cat application.log |
  grep "ERROR" |
  sort |
  uniq -c |
  sort -rn |
  head -10
```

**Windows PowerShell**:
```powershell
# ログからERRORを抽出 → グループ化して集計
Get-Content application.log |
  Select-String "ERROR" |
  Group-Object -NoElement |
  Sort-Object Count -Descending |
  Select-Object -First 10
```

**結果**:
```
  45 ERROR: Database connection failed
  23 ERROR: Invalid user credentials
  12 ERROR: File not found
   8 ERROR: Timeout exceeded
   5 ERROR: Permission denied
```

### 実例: システム情報の収集と整形

**macOS/Linux**:
```bash
# ディスク使用状況を見やすく表示
df -h | grep -v tmpfs | sort -k5 -rn
```

**Windows PowerShell**:
```powershell
# ディスク使用状況を見やすく表示
Get-PSDrive -PSProvider FileSystem |
  Where-Object {$_.Used -ne $null} |
  Select-Object Name,
    @{L='UsedGB';E={[math]::Round($_.Used/1GB,2)}},
    @{L='FreeGB';E={[math]::Round($_.Free/1GB,2)}} |
  Sort-Object UsedGB -Descending
```

---

## 変数の活用例

### 設定値の一元管理

**bash**:
```bash
# 設定
BACKUP_DIR="$HOME/backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
SOURCE_DIR="$HOME/Documents"

# バックアップ実行
tar czf "$BACKUP_DIR/backup_$TIMESTAMP.tar.gz" "$SOURCE_DIR"
```

**PowerShell**:
```powershell
# 設定
$BackupDir = "$env:USERPROFILE\backups"
$Timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$SourceDir = "$env:USERPROFILE\Documents"

# バックアップ実行
Compress-Archive -Path $SourceDir -DestinationPath "$BackupDir\backup_$Timestamp.zip"
```

---

## スクリプトの基礎例

### 条件分岐: ファイルの存在確認

**bash**:
```bash
#!/bin/bash

FILE="data.txt"

if [ -f "$FILE" ]; then
    echo "ファイル $FILE が存在します"
    cat "$FILE"
else
    echo "ファイル $FILE が見つかりません"
fi
```

**PowerShell**:
```powershell
$File = "data.txt"

if (Test-Path $File) {
    Write-Host "ファイル $File が存在します"
    Get-Content $File
} else {
    Write-Host "ファイル $File が見つかりません"
}
```

### ループ: 複数ファイルの処理

**bash**:
```bash
#!/bin/bash

# すべての.txtファイルをバックアップ
for file in *.txt; do
    cp "$file" "${file}.bak"
    echo "バックアップ完了: $file"
done
```

**PowerShell**:
```powershell
# すべての.txtファイルをバックアップ
Get-ChildItem -Filter "*.txt" | ForEach-Object {
    Copy-Item $_.FullName "$($_.FullName).bak"
    Write-Host "バックアップ完了: $($_.Name)"
}
```

---

## 学習チェックリスト

この章を終えたら、以下ができるようになっているはずです：

- [ ] パイプラインで3つ以上のコマンドを連結できる
- [ ] リダイレクトで出力をファイルに保存できる
- [ ] 変数を定義して再利用できる
- [ ] 環境変数PATHを理解している
- [ ] 簡単なスクリプトを書ける
- [ ] if文で条件分岐ができる
- [ ] forループで繰り返し処理ができる

---

**前の章**: [02. ファイル操作編](../02_ファイル操作編/README.md)
**次の章**: [04. シェルスクリプティング編](../04_シェルスクリプティング編/README.md)
