# 07. AI時代のCLI編 - AIツールとの効果的な連携

## この章について

AI時代において、CLIスキルはAIツールを効果的に活用するための基盤となります。この章では、実務で使われているAIコーディングエージェントの活用法と、AI時代のCLI自動化技術を学びます。

### 学習目標

- AIコーディングエージェントの効果的な活用
- AIに正確な指示を出すスキル
- AIの提案を安全に検証する方法
- 自動化ワークフローの構築

---

## この章で学ぶこと

### 📘 [AIコーディングエージェント活用](./ai-coding-agents.md)
- 主要なAIツール（Codex, Cursor, Claude Code, Gemini）
- 各ツールの特性と使い分け
- AIへの効果的な指示の出し方
- 実務での活用事例

### 📗 [AIと連携した自動化](./ai-automation.md)
- CLI上でのAI活用
- パイプラインとAIの組み合わせ
- テスト駆動開発とAI
- ドキュメント自動生成

### 📕 [AIの提案を安全に検証する](./verifying-ai-suggestions.md)
- コマンドの危険性チェック
- スクリプトのレビューポイント
- テスト環境での検証
- エラーハンドリング

---

## 主要なAIコーディングエージェント

### 2025年現在の主要ツール

| ツール | 特徴 | 適した用途 | 月額料金 |
|-------|------|-----------|---------|
| **Codex (GPT-5)** | 実装力が強い、テストまで完遂 | 実装・デバッグ・テスト | $200 |
| **Cursor** | コード補完、チャット、複数ファイル編集 | 日常的なコーディング | $20 |
| **Claude Code** | マルチステップタスク、フロントエンド | 複雑なタスク、UI実装 | $20-40 |
| **Gemini** | 素直で従順、簡潔なドキュメント | リファクタリング、ドキュメント作成 | $20 |

### 使い分けの実例（実務経験から）

**シナリオ**: HubSpot CRMからTwenty CRMへの移行プロジェクト

1. **仕様策定**（人間）
   - 実装方針を決定
   - API仕様を整理
   - テスト戦略を立案

2. **実装**（Codex）
   - 仕様書に基づいてコード生成
   - テストが通るまで実装
   - デバッグとエラー修正

3. **リファクタリング**（Gemini）
   - コードの簡潔化
   - パフォーマンス改善
   - メソッド定義コメント追加

4. **ドキュメント**（Codex）
   - 最終的な仕様書生成
   - API ドキュメント作成

---

## AIへの効果的な指示の出し方

### ❌ 曖昧な指示

```
「ファイルを整理して」
→ AIは何をすべきか分からない
```

### ✅ 明確で具体的な指示

```bash
「Downloadsフォルダ内のファイルを以下のルールで整理してください：
1. 画像ファイル（.jpg, .png, .gif）→ Images フォルダへ
2. ドキュメント（.pdf, .docx, .xlsx）→ Documents フォルダへ
3. アーカイブ（.zip, .tar.gz）→ Archives フォルダへ
4. 作成日が30日以上前のファイルは Old_Files フォルダへ
5. 各フォルダが存在しない場合は作成する
6. 移動前に確認メッセージを表示する」
```

**AIが生成するスクリプト例**:
```bash
#!/bin/bash

TARGET_DIR="$HOME/Downloads"
CUTOFF_DATE=$(date -d '30 days ago' +%s)

# フォルダ作成
mkdir -p "$TARGET_DIR"/{Images,Documents,Archives,Old_Files}

# ファイルを分類
for file in "$TARGET_DIR"/*; do
    [ -f "$file" ] || continue

    filename=$(basename "$file")
    extension="${filename##*.}"
    file_date=$(stat -c %Y "$file")

    # 30日以上前のファイル
    if [ $file_date -lt $CUTOFF_DATE ]; then
        echo "移動: $filename → Old_Files/"
        mv -i "$file" "$TARGET_DIR/Old_Files/"
        continue
    fi

    # 拡張子で分類
    case "$extension" in
        jpg|png|gif)
            echo "移動: $filename → Images/"
            mv -i "$file" "$TARGET_DIR/Images/"
            ;;
        pdf|docx|xlsx)
            echo "移動: $filename → Documents/"
            mv -i "$file" "$TARGET_DIR/Documents/"
            ;;
        zip|tar.gz|tgz)
            echo "移動: $filename → Archives/"
            mv -i "$file" "$TARGET_DIR/Archives/"
            ;;
    esac
done
```

---

## パイプラインとAIの組み合わせ

### 実例1: ログ分析 → AI要約

```bash
# エラーログを抽出してAIに要約させる
grep "ERROR" application.log |
  tail -100 |
  gemini -p "以下のエラーログを分析して、主な問題点を3つにまとめてください"
```

### 実例2: コミットメッセージ自動生成

```bash
# 変更内容をAIに渡してコミットメッセージを生成
git diff --cached |
  claude -p "この変更の適切なコミットメッセージを生成してください。形式: <type>: <description>" |
  git commit -F -
```

### 実例3: ドキュメント自動生成

```powershell
# 関数定義を抽出してドキュメント生成
Get-ChildItem -Recurse -Filter "*.py" |
  ForEach-Object {
    Get-Content $_.FullName |
    Select-String "^def " |
    Out-String
  } |
  codex -p "Pythonの関数定義からAPI ドキュメントを生成してください" |
  Out-File "API_DOCS.md"
```

---

## 実務での活用事例（3週間プロジェクト）

### プロジェクト概要
- **目的**: HubSpot CRM → Twenty CRMへの対応
- **予定期間**: 1週間
- **実際の期間**: 3週間
- **主な課題**: 実装方針の決定に時間がかかった

### 学んだ10のコツ

#### 1. コーディングエージェントを使い分ける
```bash
# Codex: 実装とテスト
codex -f spec.md "この仕様を実装してください"

# Gemini: リファクタリング
gemini -f code.py "このコードをリファクタリングしてください"

# Codex: 最終ドキュメント
codex -f code.py "このコードのドキュメントを生成してください"
```

#### 2. おおまかな戦略は自分で考える
```
✅ 人間が決めること:
- 実装パターンの選択（パターン1, 2, 3のどれ？）
- アーキテクチャ設計
- セキュリティポリシー

❌ AIに丸投げしないこと:
- 「よろしく実装して」
- 「ベストな方法で」
```

#### 3. タスクを細かく分割する
```bash
# 悪い例：大きすぎるタスク
"CRM連携機能を実装して"

# 良い例：分割したタスク
"1. HubSpot API クライアントを実装"
"2. Twenty API クライアントを実装"
"3. データマッピングロジックを実装"
"4. 同期スクリプトを実装"
"5. エラーハンドリングを追加"
"6. テストを作成"
```

#### 4. ロジックをデータ化してマスターファイル化

**config.yaml**:
```yaml
crm_mapping:
  hubspot:
    contact:
      firstName: first_name
      lastName: last_name
      email: email_address
  twenty:
    contact:
      firstName: given_name
      lastName: family_name
      email: email
```

```bash
# このYAMLをAIに渡して実装させる
codex -f config.yaml "このマッピング設定を使って変換関数を実装してください"
```

#### 5. ドキュメントを頻繁に更新させる
```bash
# 実装後、すぐにドキュメント生成
git diff HEAD~1 |
  codex -p "この変更内容をREADMEに反映してください" |
  tee -a README.md
```

#### 6. トークン残量2-30%で引き継ぎプロンプトを書いてもらう
```
現在のタスクの進捗状況と、次のセッションで継続するための引き継ぎ情報を生成してください。
含める情報:
- 完了したタスク
- 未完了のタスク
- 注意点
- 次にやるべきこと
```

#### 7. テスト環境を整備する
```bash
# Docker で隔離されたテスト環境
docker run -it --rm \
  -v $(pwd):/workspace \
  -w /workspace \
  python:3.11 \
  bash -c "pip install -r requirements.txt && pytest"
```

#### 8. 依頼ごとにcommitする
```bash
# タスク完了後、即座にコミット
git add .
git commit -m "feat: Implement HubSpot API client

- Add authentication
- Add contact fetch method
- Add error handling"
```

#### 9. E2Eに近いテストを書く
```python
def test_crm_sync_end_to_end():
    """HubSpot → Twenty への完全な同期をテスト"""
    # 1. HubSpotからデータ取得
    hubspot_contacts = fetch_hubspot_contacts()

    # 2. Twenty形式に変換
    twenty_contacts = convert_to_twenty_format(hubspot_contacts)

    # 3. Twentyに同期
    result = sync_to_twenty(twenty_contacts)

    # 4. 検証
    assert result.success
    assert len(result.synced) == len(hubspot_contacts)
```

#### 10. 仕様書をちゃんと書く（最重要）
```markdown
# HubSpot CRM 連携仕様書

## 目的
HubSpot CRMの連絡先データをTwenty CRMと同期する

## データマッピング
| HubSpot フィールド | Twenty フィールド | 変換ルール |
|-------------------|------------------|----------|
| firstName | given_name | そのまま |
| lastName | family_name | そのまま |
| email | email | 小文字に正規化 |

## エラーハンドリング
- API エラー → 3回リトライ、指数バックオフ
- データ検証エラー → ログに記録、スキップ
- 致命的エラー → プロセス停止、通知

## テスト戦略
- ユニットテスト: 各変換関数
- 統合テスト: API連携
- E2Eテスト: 完全な同期フロー
```

---

## AIの提案を検証するチェックリスト

### コマンド実行前

- [ ] 削除系コマンド（rm, Remove-Item）が含まれていないか
- [ ] ワイルドカード（*）の範囲は適切か
- [ ] 権限昇格（sudo, 管理者実行）は必要か
- [ ] パスは正しいか（誤って重要なディレクトリを指していないか）

### スクリプト実行前

- [ ] 入力検証が実装されているか
- [ ] エラーハンドリングがあるか
- [ ] ハードコードされた認証情報がないか
- [ ] テスト環境で動作確認したか

### 本番環境適用前

- [ ] バックアップを取得したか
- [ ] ロールバック手順を準備したか
- [ ] ログ出力が適切か
- [ ] 監視設定をしたか

---

## まとめ

### AI時代のCLIスキルの価値

1. **AIを安全に使える**: 提案の危険性を判断できる
2. **AIに正確な指示を出せる**: より良い結果を得られる
3. **AIと協働できる**: パイプラインで連携
4. **生産性が劇的に向上**: 自動化 × AI

### 重要な原則

> **「AIに任せられるのは、理解している内容の実装のみ」**
>
> **「実装方針の決定は人間の責任」**
>
> **「テストがしっかりしていれば、AIは最強のツール」**

---

**前の章**: [06. セキュリティ編](../06_セキュリティ編/README.md)
**次の章**: [08. プラットフォーム固有編](../08_プラットフォーム固有編/README.md)
