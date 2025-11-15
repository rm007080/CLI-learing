# Windows ではじめる CLI 入門 — いっしょに PowerShell デビューしよっ！（OneDrive対応版）

こんにちは！明るく元気に、あなたのターミナル体験をエスコートするよ。CLI（コマンドライン・インターフェース）ってちょっとドキドキするけど、手順どおりに練習すれば大丈夫。わたしと一緒に、ゆっくり一歩ずつ進めていこうね！

この入門は、あなたが実際に手を動かしながら学べるように作ってあるよ。コマンドはぜんぶ Windows を想定（標準シェルは PowerShell）。出力例はイメージだから、あなたの環境では日付やユーザー名が少し違っても OK だよ。

**この資料は OneDrive 環境でも確実に動作するように改良されています！**

最初に「練習用フォルダ」を作って安全に遊ぼうね。消したくない大切なファイルがある場所では試さないように気をつけよ〜！

---

## 1. はじめに

### CLI ってなんだろう？GUI との違い

- GUI（グラフィカル・ユーザー・インターフェース）は、アイコンやボタンをクリックして操作するいつもの画面のことだよ（エクスプローラーなど）。
- CLI（コマンドライン・インターフェース）は、文字でコマンドを入力して操作する方法。見た目は地味だけど、正確・高速・自動化に強いのが魅力なの！

例え話：  
GUI は「レストランでメニューの中から注文する」感じ。CLI は「オーダー票に正確に書いて注文する」感じ。

- GUI: タッチパネルに表示された「ステーキ」のアイコンを押し、「焼き加減」のボタンを押し、「ソース」のボタンを押し、最後に「決定」ボタンを押して注文
- CLI: 「ステーキをミディアムレアで、付け合わせはアスパラガス、ソースはバルサミコで」のように、言葉で細かく、かつ正確に指示

> [!TIP]
> CLI と CUI の関係
>
> - CUI（キャラクタ・ユーザー・インターフェース）は「文字でやりとりする UI」の総称。
> - CLI は CUI の中でも「コマンドを打って処理する」タイプ。つまり CLI ⊂ CUI って関係だよ。

---

### AI 時代の今、なぜコマンド操作を学ぶの？ AI を「賢く安全に使いこなす」ために！

CLI の基本を学ぶ目的は、AI を**賢く、安全に使いこなすための主導権を握る**ことなんです。

#### 1. AI の提案を理解し、安全に使うために

AI は非常に優秀ですが、時々、私たちの意図とは違う、少し危険なコマンドを提案してしまう可能性もゼロではありません。

例えば、「PC の中を整理して」とお願いしたら、もし AI が間違えて「全部消し去る」という超危険な呪文（`Remove-Item -Recurse -Force C:\` など）を唱えようとしたら…？

CLI の知識があれば、あなたはその呪文を見て、「おっと、これは全部消すコマンドだ。危ない！」と**AI の提案を理解し、危険を未然に防ぐ**ことができます。AI の言いなりになるのではなく、AI を安全に使うための「賢い主（あるじ）」になれるのです。

#### 2. AI に、より的確な指示を出すために

CLI の強みは、小さなコマンドを「パイプライン（`|`）」という記号で繋ぎ合わせて、複雑な処理を自動化できる点にあります。

- 「ファイルの一覧（`Get-ChildItem`）から」
- 「テキストファイル（`.txt`）だけを絞り込み（`Where-Object`）」
- 「その数を数える（`Measure-Object`）」

といった一連の流れを、`Get-ChildItem | Where-Object {$_.Extension -eq ".txt"} | Measure-Object` のように一行で表現できます。

このような「コマンドの文法」を知っていると、**「こういう手順で、この処理をしてほしい」と、より正確で複雑な指示**を出せるようになります。

そしてこの「パイプライン（`|`）」で処理を繋げる考え方は、**CLI 上で動作する AI アシスタントと組み合わせることで、さらに強力になります**。コマンドの実行結果をそのまま AI に渡して要約させたり、AI が生成したコードをファイルに保存したりと、複雑な処理の自動化に AI を組み込むことができるのです。

コマンドの実行結果を AI に渡す例：

```powershell
# 議事録を要約して別のファイルに保存する
Get-Content minutes.txt | gemini -p "要約して" | Out-File summary.md
```

---

### PowerShell を使ってみよう

1. スタートメニューを開く（`Windows キー`）
2. 「PowerShell」と入力して Enter
3. こんな感じのプロンプトが出れば準備 OK！

```powershell
PS C:\Users\YourName>
```

- 最初の `PS` は PowerShell の合図。入力して Enter を押すとコマンドが実行されるよ。

まずは安全に練習するためのフォルダを作ろう！

```powershell
# デスクトップに練習用フォルダを作る

# まず、デスクトップの場所を確認
[Environment]::GetFolderPath("Desktop")

# デスクトップに移動
cd ([Environment]::GetFolderPath("Desktop"))

# 練習用フォルダを作成
mkdir cli-practice
cd cli-practice
pwd
```

実行結果（例）:

```powershell
Path
----
C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice
```

> [!NOTE]
> **デスクトップの場所について**
>
> Windows の環境によって、デスクトップフォルダの場所は異なります：
>
> - 標準環境：`C:\Users\ユーザー名\Desktop`
> - OneDrive同期（パターン1）：`C:\Users\ユーザー名\OneDrive\Desktop`
> - OneDrive同期（パターン2）：`C:\Users\ユーザー名\OneDrive\デスクトップ`
> - OneDrive + Documents統合：`C:\Users\ユーザー名\OneDrive\Documents\Desktop`
>
> 上記のコマンド `[Environment]::GetFolderPath("Desktop")` を使えば、どの環境でも自動的に正しいパスが分かるよ！
>
> もし「パスが存在しません」というエラーが出た場合は、ホームディレクトリに作成しても大丈夫です：
>
> ```powershell
> cd ~
> mkdir cli-practice
> cd cli-practice
> pwd
> ```

---

## 2. ファイルとディレクトリの基本

### フォルダとディレクトリは同じもの？

みなさんが普段「フォルダ」と呼んでいるものは、CLI の世界では「ディレクトリ」と呼ばれます。呼び方が違うだけで、役割はまったく同じ！ファイルを整理するための「入れ物」のことです。

GUI（グラフィカルな画面）ではフォルダ、CLI（文字の画面）ではディレクトリ、と覚えておくとスムーズですよ。この資料では、基本的に「ディレクトリ」という言葉を使っていきますね。

---

### PC の中はどうなってる？（階層構造）

- Windows のファイルは「入れ子（ディレクトリの中にディレクトリ）」の階層構造だよ。
- キーワード
  - 絶対パス：ドライブルート（`C:\`）から全部書く（例：`C:\Users\you\Desktop`）
  - 相対パス：いまいる場所からの道順（例：`..\Documents`）
  - `~`：あなたのホーム（`C:\Users\あなたのユーザー名`）
  - `.`：現在地、`..`：1 つ上の階層（親）

---

### 現在地を確認する（`Get-Location` / `pwd`）

```powershell
Get-Location
```

または短く：

```powershell
pwd
```

出力（例）:

```powershell
Path
----
C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice
```

- `pwd` = print working directory（いまいる場所を表示）
- PowerShell では `Get-Location` が正式コマンドで、`pwd` はそのエイリアス（別名）だよ。

---

### 中身を一覧表示する（`Get-ChildItem` / `ls`）

まずはサンプルの入れ物を少し作るね：

```powershell
mkdir photos, scripts
New-Item notes.txt, README.md
```

> [!NOTE]
> `New-Item` でファイルを作成する際、`-ItemType File` は拡張子があれば省略できます。ただし、拡張子がないファイル（例：`README`）を作る場合や、より明示的にしたい場合は `-ItemType File` を指定することをおすすめします。
>
> ```powershell
> # 拡張子がない場合は明示的に指定
> New-Item -ItemType File README
> ```
>
> 拡張子がないファイルは、Windowsでは「FILE ファイル」や「ファイル」という種類で表示され、特定のアプリケーションと関連付けられていません。開く場合は、メモ帳やVSCodeなどのテキストエディタから直接開くか、`notepad README` のようにコマンドで指定して開きます。
>
> Unix/Linux系のシステムでは、`README`、`Makefile`、`Dockerfile` など、拡張子なしのテキストファイルがよく使われます。

一覧表示してみよう！

```powershell
Get-ChildItem
```

または短く：

```powershell
ls
```

出力（例）:

```powershell
    ディレクトリ: C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:20                photos
d-----        2025/09/26     14:20                scripts
-a----        2025/09/26     14:19              0 notes.txt
-a----        2025/09/26     14:19              0 README.md
```

> [!NOTE]
> 各行先頭の`Mode`列について：`d`はディレクトリ、`-`はファイルを示します。その後の文字は「属性（読み取り専用、アーカイブ、隠しファイルなど）」を表しています。今は「そんな設定があるんだな」くらいで大丈夫！

他にも便利なオプションがあるよ。

- `-Force`：隠しファイルも表示する
- `-Name`：名前だけ表示する
- `-Recurse`：サブディレクトリの中身も再帰的に表示する

```powershell
Get-ChildItem -Force
```

---

### 場所を移動する（`Set-Location` / `cd`）

```powershell
Set-Location photos
```

または短く：

```powershell
cd photos
pwd
```

出力（例）:

```powershell
Path
----
C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice\photos
```

- 1 つ上へ戻る：

```powershell
cd ..
pwd
```

- ホームへ移動：

```powershell
cd ~
pwd
```

- 直前の場所へ戻る（トグル）：

```powershell
cd -
```

---

## 3. ファイル・ディレクトリの操作

練習用ディレクトリに戻って進めようね：

```powershell
# デスクトップの練習用フォルダに戻る
cd (Join-Path ([Environment]::GetFolderPath("Desktop")) "cli-practice")
```

または、変数を使うともっと分かりやすいよ：

```powershell
$desktop = [Environment]::GetFolderPath("Desktop")
cd "$desktop\cli-practice"
```

---

### ディレクトリを作成する（`New-Item` / `mkdir`）

```powershell
mkdir project
ls
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:25                photos
d-----        2025/09/26     14:25                project
d-----        2025/09/26     14:25                scripts
-a----        2025/09/26     14:19              0 notes.txt
-a----        2025/09/26     14:19              0 README.md
```

- まとめて作る＆途中の階層も同時に：

```powershell
mkdir logs\2025\09 -Force
Get-ChildItem logs -Recurse
```

出力（例）:

```powershell
    ディレクトリ: C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice\logs

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:26                2025

    ディレクトリ: C:\Users\あなたのユーザー名\OneDrive\Documents\Desktop\cli-practice\logs\2025

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:26                09
```

---

### 空のファイルを作成する（`New-Item`）

```powershell
New-Item hello.txt
ls hello.txt
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/09/26     14:30              0 hello.txt
```

- `New-Item` は「新しいファイルやディレクトリを作る」コマンドだよ。

---

### ファイルやディレクトリをコピーする（`Copy-Item` / `cp`）

ファイルをコピー：

```powershell
Copy-Item hello.txt hello-copy.txt
```

または短く：

```powershell
cp hello.txt hello-copy.txt
ls
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:26                logs
d-----        2025/09/26     14:20                photos
d-----        2025/09/26     14:25                project
d-----        2025/09/26     14:20                scripts
-a----        2025/09/26     14:30              0 hello-copy.txt
-a----        2025/09/26     14:30              0 hello.txt
-a----        2025/09/26     14:19              0 notes.txt
-a----        2025/09/26     14:19              0 README.md
```

ディレクトリをコピー（`-Recurse` は中身ごと再帰的に）：

```powershell
Copy-Item -Recurse photos photos-backup
ls
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:26                logs
d-----        2025/09/26     14:20                photos
d-----        2025/09/26     14:32                photos-backup
d-----        2025/09/26     14:25                project
d-----        2025/09/26     14:20                scripts
-a----        2025/09/26     14:30              0 hello-copy.txt
-a----        2025/09/26     14:30              0 hello.txt
-a----        2025/09/26     14:19              0 notes.txt
-a----        2025/09/26     14:19              0 README.md
```

---

### ファイルやディレクトリを移動する/名前を変更する（`Move-Item` / `mv`）

名前を変える（リネーム）：

```powershell
Move-Item hello-copy.txt greetings.txt
```

または短く：

```powershell
mv hello-copy.txt greetings.txt
ls
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        2025/09/26     14:26                logs
d-----        2025/09/26     14:20                photos
d-----        2025/09/26     14:32                photos-backup
d-----        2025/09/26     14:25                project
d-----        2025/09/26     14:20                scripts
-a----        2025/09/26     14:30              0 greetings.txt
-a----        2025/09/26     14:30              0 hello.txt
-a----        2025/09/26     14:19              0 notes.txt
-a----        2025/09/26     14:19              0 README.md
```

別の場所へ動かす：

```powershell
mv greetings.txt photos\
ls photos
```

出力（例）:

```powershell
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2025/09/26     14:30              0 greetings.txt
```

> [!TIP]
> ファイル名にスペースが含まれる場合は、ファイル名をダブルクォーテーション(`"`)またはシングルクォーテーション(`'`)で囲みましょう。
>
> ```powershell
> # ダブルクォーテーションで囲む
> mv "My File.txt" photos\
>
> # シングルクォーテーションで囲む
> mv 'My File.txt' photos\
> ```

---

### ファイルを削除する（`Remove-Item` / `rm`）

> [!WARNING]
> エクスプローラーのごみ箱と違って、`Remove-Item`コマンドは実行するとファイルが**完全に**削除されます。元に戻すのはとても難しいので、特に慣れないうちは慎重に使いましょう。

ファイルを削除：

```powershell
Remove-Item hello.txt

# ファイルが削除されたか確認
Test-Path hello.txt
```

出力（例）:

```powershell
False
```

`Test-Path` は、ファイルやディレクトリが存在するかを確認するコマンドだよ。`True`（存在する）か `False`（存在しない）を返すよ。

もっと分かりやすく表示したい場合は：

```powershell
if (Test-Path hello.txt) { 
    "hello.txt があります" 
} else { 
    "hello.txt はありません" 
}
```

出力（例）:

```powershell
hello.txt はありません
```

確認しながら削除（`-Confirm` は対話的。うっかり防止に超おすすめ！）：

```powershell
Remove-Item notes.txt -Confirm
# 出る質問例: 
# 確認
# ターゲット "C:\Users\...\notes.txt" で操作 "項目の削除" を実行しています。
# [Y] はい(Y)  [A] すべて続行(A)  [N] いいえ(N)  [L] すべて無視(L)  [S] 中断(S)  [?] ヘルプ (既定値は "Y"):
# Y と入力して Enter で削除
# N と入力して Enter でキャンセル
```

> [!TIP]
> どうしても`Remove-Item`コマンドが不安な時は、以下の方法があります：
>
> **方法1: エクスプローラーのごみ箱を使う**
>
> ```powershell
> # ごみ箱を開く
> explorer shell:RecycleBinFolder
> ```
>
> ごみ箱が開いたら、削除したいファイルをエクスプローラーからドラッグ＆ドロップします。
>
> **方法2: `-Confirm` オプションで確認しながら削除**
>
> ```powershell
> Remove-Item notes.txt -Confirm
> ```
>
> これなら削除前に確認メッセージが出るので、うっかり削除を防げます。
>
> **注意**: PowerShell の標準コマンドには「ごみ箱に移動する」機能はありません。`Remove-Item` は完全削除なので、大切なファイルは事前にバックアップを取っておくことをおすすめします。

---

#### 空のディレクトリを削除する

`Remove-Item`コマンドは、**中身が空の**ディレクトリも削除できます。

```powershell
mkdir emptydir
Remove-Item emptydir

# 削除されたか確認
Test-Path emptydir
```

出力（例）:

```powershell
False
```

---

#### 中身があるディレクトリを削除する (`Remove-Item -Recurse`)

中身が入っているディレクトリを丸ごと削除したい場合は、`Remove-Item`コマンドに`-Recurse`オプションをつけます。

```powershell
Remove-Item -Recurse photos

# 削除されたか確認
Test-Path photos
```

出力（例）:

```powershell
False
```

確認しながら削除（`-Confirm`オプションをつける）を推奨：

```powershell
Remove-Item -Recurse -Confirm photos
```

---

## 4. ファイルの中身を見る

### ファイルの内容をすべて表示する（`Get-Content` / `cat`）

まず内容のあるファイルを作るね：

```powershell
"一行目だよ" | Out-File poem.txt
"二行目もいるよ" | Add-Content poem.txt
Get-Content poem.txt
```

**コマンドの解説：**

1. `"一行目だよ" | Out-File poem.txt`
   - 文字列を `poem.txt` に**上書き保存**
   - ファイルが既に存在する場合は、内容が消えて新しい内容に置き換わる

2. `"二行目もいるよ" | Add-Content poem.txt`
   - 文字列を `poem.txt` に**追記**
   - 既存の内容は残したまま、末尾に新しい行を追加

3. `Get-Content poem.txt`
   - ファイルの内容を読み込んで画面に表示

または短く：

```powershell
cat poem.txt
```

出力（例）:

```powershell
一行目だよ
二行目もいるよ
```

> [!TIP]
> **`Out-File`（上書き）と `Add-Content`（追記）の違い**
>
> | コマンド | 動作 | 既存ファイルがある場合 |
> |---------|------|---------------------|
> | `Out-File` または `>` | **上書き**保存 | 既存の内容は消える |
> | `Add-Content` または `>>` | **追記** | 既存の内容は残る |
>
> **リダイレクト演算子を使った短い書き方：**
>
> ```powershell
> # Out-File の代わりに > を使う（上書き）
> "一行目だよ" > poem.txt
>
> # Add-Content の代わりに >> を使う（追記）
> "二行目もいるよ" >> poem.txt
>
> # 確認
> cat poem.txt
> ```
>
> **パイプライン（`|`）について：**
>
> `|` 記号は、左側のコマンドの出力を右側のコマンドの入力として渡します。これにより、コマンドを連鎖させて複雑な処理を簡潔に書けるようになります。
>
> ```powershell
> # 左側の文字列が、右側のコマンドに渡される
> "Hello" | Out-File hello.txt
> ```

---

### 長いファイルも安心！スクロールしながら見る（`more`）

```powershell
Get-Content poem.txt | more
```

または短く：

```powershell
more poem.txt
```

画面には内容がページ単位で表示されるよ。主なキー操作：

- スペース：1 ページ進む
- Enter：1 行ずつ進む
- `q` 終了（これで PowerShell に戻るよ）

> [!IMPORTANT]
> `more` の画面から戻れなくなったら、慌てず`q`キーを押してみてね！

出力イメージ（画面表示）:

```powershell
一行目だよ
二行目もいるよ
-- 続く --
```

---

## 5. 知っているとちょっと便利

### コマンドの履歴を確認する（PowerShell）

- 直近の履歴を見る：

```powershell
Get-History | Select-Object -Last 5
```

または短く：

```powershell
history | select -Last 5
```

出力（例）:

```powershell
  Id CommandLine
  -- -----------
 121 ls
 122 Copy-Item -Recurse photos photos-backup
 123 Move-Item greetings.txt photos\
 124 Remove-Item notes.txt -Confirm
 125 Get-History | Select-Object -Last 5
```

- 直前のコマンドをもう一度：

```powershell
# ↑キーを押して Enter
```

- 履歴を検索（超便利！）：`Ctrl + r` を押して、探したい文字を打つ → Enter で実行、`Esc` で編集してから Enter でも OK
- 履歴を遡って実行（超超便利！）：`↑`, `↓` を押して、コマンド履歴を遡る → Enter で実行

- 履歴をすべて表示：

```powershell
Get-History
```

---

### 入力を補完する（Tab キー）

- 途中まで入力して `Tab` を押すと、候補を補完してくれるよ。

```powershell
cd scr<Tab>
```

出力（例）:

```powershell
cd .\scripts\
```

- 候補が複数あるときは、もう一度 `Tab` で次の候補が出るよ。タイプ数が減って、入力ミスも減るからどんどん使おう！

> [!NOTE]
> **ワイルドカード（グロブ）で複数ファイルを一度に操作！**
>
> Tab キーによる補完と似ているけれど、もっと強力なのが**ワイルドカード（グロブ）**だよ。`*` や `?` といった特殊な記号を使って、複数のファイルを一度に指定できます。
>
> **よく使うパターン：**
>
> ```powershell
> # .txt で終わるファイルをすべて表示
> ls *.txt
>
> # test で始まるファイルをすべて表示
> ls test*
>
> # ファイル名に "2024" を含むファイルを表示
> ls *2024*
>
> # 1文字 + .txt のファイル（a.txt, b.txtなど）
> ls ?.txt
> ```
>
> **注意**: `*.txt` は**それ単体では実行できません**。必ず `ls` や `Remove-Item` などのコマンドと組み合わせて使います。
>
> これは PowerShell の機能で、コマンドが実行される前に `*` の部分が実際のファイル名に展開されるんだ。今後の学習で詳しく触れるけど、こういう便利な記号があることも知っておくと、世界が広がるよ！

---

### エクスプローラーと連携する（`Start-Process` / `explorer` / `ii`）

CLI で作業していて、「やっぱり GUI の画面でファイルを確認したいな」と思うこと、ありますよね。そんな時に超便利なのが、`Start-Process`コマンドや`explorer`、`ii`（Invoke-Item）コマンドです！

- **今いる場所をエクスプローラーで開く**

```powershell
Start-Process .
```

または短く：

```powershell
explorer .
```

または：

```powershell
ii .
```

このコマンドを実行すると、今 PowerShell で作業しているディレクトリがエクスプローラーウィンドウでパッと開きます。これは本当に便利！

- **特定のファイルやアプリケーションを開く**

```powershell
# `poem.txt`をデフォルトのテキストエディタで開く
ii poem.txt

# `photos`ディレクトリをエクスプローラーで開く
explorer photos

# `poem.txt`をエクスプローラーで選択して表示する
explorer /select,poem.txt

# メモ帳を開く
Start-Process notepad
```

これらのコマンドを使いこなせば、CLI と GUI の良いとこ取りができて、作業がもっとスムーズになりますよ！

> [!TIP]
> エクスプローラー上にあるファイルやディレクトリを PowerShell にドラッグ＆ドロップすると、そのファイルやディレクトリのフルパスが自動で入力されるよ。長いパスを打つのが面倒な時に試してみてね！

---

### クリップボードとの連携（`Set-Clipboard`, `Get-Clipboard`）

Windows PowerShell には、クリップボードと連携するための超便利なコマンド`Set-Clipboard`と`Get-Clipboard`があります。これらを使えば、コマンドの結果をコピーしたり、クリップボードの内容をファイルに保存したりできるよ！

- **コマンドの実行結果をクリップボードにコピーする**

```powershell
# `Get-Location`の実行結果をクリップボードにコピーする
Get-Location | Set-Clipboard
```

または短く：

```powershell
pwd | Set-Clipboard
```

- **クリップボードの内容をファイルに書き出す**

```powershell
# クリップボードの内容を`clip.txt`というファイルに書き出す
Get-Clipboard | Out-File clip.txt
```

または、リダイレクト演算子 `>` を使ってもOK：

```powershell
Get-Clipboard > clip.txt
```

> [!CAUTION]
> **`|` と `>` を同時に使うことはできません！**
>
> ```powershell
> # ❌ エラーになる
> Get-Clipboard | > clip.txt
>
> # ✅ どちらか一方を使う
> Get-Clipboard | Out-File clip.txt  # パイプライン + Out-File
> Get-Clipboard > clip.txt            # リダイレクトのみ
> ```
>
> - `|` は「コマンドとコマンドを繋ぐ」
> - `>` は「コマンドとファイルを繋ぐ」
> 
> どちらか一方を選んで使いましょう！

---

### VSCode と連携する（`code`コマンド）

もしあなたがコードエディタとして Visual Studio Code（VSCode）を使っているなら、`code`コマンドがとっても便利だよ！

※このコマンドを使うには、VSCode をインストールした後に、「シェルコマンドのインストール」という設定が必要です。VSCode を開いて、`Ctrl + Shift + P`でコマンドパレットを出し、「`Shell Command: Install 'code' command in PATH`」を実行すると使えるようになります。

- **今いる場所を VSCode で開く**

プロジェクトのルートディレクトリに`cd`で移動したあと、次のコマンドを打ってみて！

```powershell
code .
```

これだけで、今いるディレクトリをプロジェクトとして VSCode の新しいウィンドウで開いてくれます。いちいち VSCode の「ファイル」メニューから「開く...」を選ばなくていいから、すっごく楽ちん！

- **特定のファイルを開く**

```powershell
# README.mdをVSCodeで開いて編集する
code README.md
```

CLI でファイルを探して、そのまま`code`コマンドで編集を始める、なんていうスムーズな連携ができるようになりますよ。

---

### Cursor と連携する（`cursor`コマンド）

AI 搭載エディタとして人気の Cursor を使っているなら、`cursor`コマンドも同じように使えます！

※このコマンドを使うには、Cursor をインストールした後に、「シェルコマンドのインストール」という設定が必要です。Cursor を開いて、`Ctrl + Shift + P`でコマンドパレットを出し、「`Shell Command: Install 'code' command in PATH`」を実行すると使えるようになります。

- **今いる場所を Cursor で開く**

```powershell
cursor .
```

- **特定のファイルを開く**

```powershell
# README.mdをCursorで開いて編集する
cursor README.md
```

---

### おまけショートカット

- `Ctrl + c`：実行中のコマンドを中断
- `Esc`：入力中の行をすべてクリア
- `Home` / `End`：行の先頭 / 末尾へ
- `Ctrl + l` または `Clear-Host`：画面をきれいに
- `cls`：画面をきれいに（`Clear-Host`のエイリアス）

---

## 6. おわりに

### これからの学習に向けて

次のステップでおすすめのテーマだよ。ワクワクするね！

- リダイレクト：`>`（上書き）、`>>`（追記）、`Out-File`
- パイプライン：`|`（コマンドとコマンドをつなぐ）
- 検索：`Select-String`（文字列検索）、`Where-Object`（フィルタリング）
- 権限：`Get-Acl`、`Set-Acl`
- 圧縮：`Compress-Archive` / `Expand-Archive`
- ネットワーク：`Invoke-WebRequest`、`Test-Connection`

---

### ミニ課題（自習用）

学んだことを使って、腕試ししてみよう！

1. `project`ディレクトリの中に`src`と`docs`というディレクトリを作ってみよう。
2. `README.md`を`docs`ディレクトリにコピーして、`explorer /select,docs\README.md`でエクスプローラーで確認してみよう。
3. `poem.txt`を`docs`ディレクトリに移動して、`more`コマンドで中身を読んでみよう。
4. `Get-History`コマンドで、今日使ったコマンドの履歴を眺めてみよう。
5. `Get-ChildItem`の結果を`Set-Clipboard`を使ってクリップボードにコピーして、テキストエディタに貼り付けてみよう。

---

### 困ったときの調べ方

- 公式ヘルプ：

```powershell
Get-Help Get-ChildItem      # ヘルプ表示
Get-Help Get-ChildItem -Examples  # 例を見る
Get-Help Get-ChildItem -Detailed  # 詳細を見る
Get-Help Get-ChildItem -Full      # 完全版を見る
```

- コマンドを探す：

```powershell
Get-Command *file*   # 「file」を含むコマンドを一覧
Get-Command -Noun Item   # 名詞が「Item」のコマンドを一覧
```

- エイリアスを確認：

```powershell
Get-Alias ls         # 「ls」が何の省略形かを確認
Get-Alias -Definition Get-ChildItem  # 「Get-ChildItem」のエイリアスを一覧
```

- Web 検索のコツ：
  - キーワード + "PowerShell" を付けると近い情報に出会いやすいよ。
  - コマンドプロンプト（cmd.exe）と PowerShell では使えるコマンドが違うことがあるから、記事の前提に注意してね。

> [!NOTE]
> Web 上の記事を探すとき、Windows には「コマンドプロンプト（cmd.exe）」と「PowerShell」という 2 つのシェルがあることを知っておくと、より正確な情報にたどり着きやすくなります。この資料では PowerShell を使っていますが、古い記事ではコマンドプロンプト向けの情報もあるので注意しましょう。

---

## 付録：今日使ったコマンド早見表（超ざっくり）

| コマンド（正式名）      | エイリアス  | 役割                      | よく使う例                                  |
| ----------------------- | ----------- | ------------------------- | ------------------------------------------- |
| `Get-Location`          | `pwd`       | いまの場所を表示          | `pwd`                                       |
| `Get-ChildItem`         | `ls`, `dir` | 中身一覧                  | `ls`                                        |
| `Set-Location`          | `cd`        | 移動                      | `cd ..` / `cd ~/Desktop`                    |
| `New-Item -ItemType Directory` | `mkdir`     | ディレクトリ作成          | `mkdir logs\2025\09 -Force`                 |
| `New-Item`              | -           | ファイル作成              | `New-Item memo.txt`                         |
| `Copy-Item`             | `cp`        | コピー                    | `Copy-Item -Recurse photos photos-backup`   |
| `Move-Item`             | `mv`        | 移動/リネーム             | `mv memo.txt docs\`                         |
| `Remove-Item`           | `rm`, `del` | 削除（注意！）            | `Remove-Item file.txt -Confirm`             |
| `Get-Content`           | `cat`       | 全文表示                  | `cat poem.txt`                              |
| `more`                  | -           | ページ表示                | `more poem.txt`                             |
| `Get-History`           | `history`   | 履歴                      | `Get-History \| Select-Object -Last 10`     |
| `Start-Process`         | `start`     | アプリやファイルで開く    | `Start-Process .`                           |
| `Invoke-Item`           | `ii`        | 既定アプリで開く          | `ii .` / `ii file.txt`                      |
| `explorer`              | -           | エクスプローラーで開く    | `explorer .`                                |
| `Set-Clipboard`         | -           | クリップボードにコピー    | `Get-ChildItem \| Set-Clipboard`            |
| `Get-Clipboard`         | -           | クリップボードから貼付    | `Get-Clipboard \| Out-File new-file.txt`    |
| `code`                  | -           | VSCode で開く             | `code .` / `code file.txt`                  |
| `cursor`                | -           | Cursor で開く             | `cursor .` / `cursor file.txt`              |
| `[Environment]::GetFolderPath()` | -  | 特殊フォルダのパス取得  | `[Environment]::GetFolderPath("Desktop")`   |

---

ここまでできたあなた、すごい！もう CLI の第一歩をしっかり踏み出せたよ。最初はゆっくりで大丈夫。少しずつ "手が覚える" 感覚が出てくるから、毎日ちょっとだけでも触ってみてね。わからないことがあったらまた呼んで！いっしょにレベルアップしていこ〜！！

