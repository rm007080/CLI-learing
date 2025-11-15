# Linux ではじめる CLI 入門 — いっしょに Bash デビューしよっ！

こんにちは！明るく元気に、あなたのターミナル体験をエスコートするよ。CLI（コマンドライン・インターフェース）ってちょっとドキドキするけど、手順どおりに練習すれば大丈夫。わたしと一緒に、ゆっくり一歩ずつ進めていこうね！

この入門は、あなたが実際に手を動かしながら学べるように作ってあるよ。コマンドはぜんぶ Linux を想定（標準シェルは bash）。出力例はイメージだから、あなたの環境では日付やユーザー名が少し違っても OK だよ。

最初に「練習用フォルダ」を作って安全に遊ぼうね。消したくない大切なファイルがある場所では試さないように気をつけよ〜！

---

## 1. はじめに

### Linux のターミナルとは

Linux では、ターミナル（Terminal）を使ってシェルと対話します。シェルはコマンドを解釈して実行するプログラムで、最も一般的なのが **bash**（Bourne Again SHell）です。

### ディストリビューションによる違い

Linux にはさまざまなディストリビューション（配布版）があります：

- **Ubuntu / Debian**: 初心者に優しい、人気のディストリビューション
- **Fedora / RHEL / CentOS**: エンタープライズ向け、RedHat系
- **Arch Linux**: カスタマイズ性が高い、上級者向け

基本的なコマンドはどのディストリビューションでも共通ですが、パッケージマネージャーなど一部が異なります。

---

### ターミナルを開こう

ディストリビューションによって開き方が異なります：

**Ubuntu（GNOME）**:
1. `Ctrl + Alt + T` を押す
2. または、アプリケーションメニューから「Terminal」を検索

**他のディストリビューション**:
- アプリケーションメニューから「Terminal」や「Konsole」を探す

こんな感じのプロンプトが出れば準備 OK！

```bash
user@hostname:~$
```

- `user`: あなたのユーザー名
- `hostname`: コンピュータの名前
- `~`: ホームディレクトリ（あなたの個人フォルダ）
- `$`: 一般ユーザーの合図（`#`はrootユーザー）

### 練習用フォルダを作ろう

安全に練習するための場所を用意します：

```bash
# ホームディレクトリに移動
cd ~

# 練習用フォルダを作る
mkdir cli-practice

# 作ったフォルダに移動
cd cli-practice

# 現在位置を確認
pwd
```

実行結果（例）:

```bash
/home/yourname/cli-practice
```

---

## 2. ファイルとディレクトリの基本

### 現在位置を確認する - `pwd`

**pwd** = **P**rint **W**orking **D**irectory（現在の作業ディレクトリを表示）

```bash
pwd
```

実行結果（例）:

```bash
/home/yourname/cli-practice
```

---

### ファイル一覧を表示する - `ls`

**ls** = **L**i**s**t（一覧表示）

```bash
# 基本の使い方
ls

# 詳細表示（-l = long format）
ls -l

# 隠しファイルも表示（-a = all）
ls -la

# ファイルタイプ表示（-F = classify）
ls -F
```

実行結果（例）:

```bash
$ ls -la
total 12
drwxr-xr-x  2 user user 4096 Nov 14 10:30 .
drwxr-xr-x 25 user user 4096 Nov 14 10:29 ..
-rw-r--r--  1 user user  220 Nov 14 10:29 .bashrc
drwxr-xr-x  2 user user 4096 Nov 14 10:30 Documents
```

**オプションの組み合わせ**:
```bash
# -l、-a、-h（人間が読みやすい形式）を組み合わせ
ls -lah
```

---

### ディレクトリを移動する - `cd`

**cd** = **C**hange **D**irectory（ディレクトリを変更）

```bash
# ホームディレクトリに移動
cd ~
# または
cd

# 一つ上のディレクトリに移動
cd ..

# 絶対パスで移動
cd /home/yourname/Documents

# 相対パスで移動
cd Documents

# 前にいたディレクトリに戻る
cd -
```

> [!TIP]
> **Tab補完を使おう！**
>
> `cd Doc` と入力して `Tab` キーを押すと、`cd Documents/` と自動補完されます。
> これで入力ミスを減らせます！

---

## 3. ファイル・ディレクトリ操作

### ディレクトリを作る - `mkdir`

**mkdir** = **M**a**k**e **DIR**ectory（ディレクトリを作成）

```bash
# 基本の使い方
mkdir myfolder

# 複数階層を一度に作る（-p = parents）
mkdir -p parent/child/grandchild

# 複数のディレクトリを同時に作る
mkdir folder1 folder2 folder3
```

実行結果:

```bash
$ ls
folder1  folder2  folder3  myfolder  parent
```

---

### 空のファイルを作る - `touch`

```bash
# ファイルを作成（存在しない場合）
touch file1.txt

# 既存ファイルのタイムスタンプを更新（存在する場合）
touch file1.txt

# 複数ファイルを同時に作成
touch file1.txt file2.txt file3.txt
```

---

### ファイル・ディレクトリをコピーする - `cp`

**cp** = **C**o**p**y（コピー）

```bash
# ファイルをコピー
cp file1.txt file2.txt

# ファイルを別のディレクトリにコピー
cp file1.txt Documents/

# ディレクトリごとコピー（-r = recursive）
cp -r myfolder myfolder_backup

# 上書き前に確認（-i = interactive）
cp -i file1.txt file2.txt
```

---

### ファイル・ディレクトリを移動/リネームする - `mv`

**mv** = **M**o**v**e（移動）

```bash
# ファイルをリネーム
mv oldname.txt newname.txt

# ファイルを別のディレクトリに移動
mv file1.txt Documents/

# ディレクトリをリネーム
mv old_folder new_folder

# 複数ファイルを移動
mv file1.txt file2.txt Documents/
```

> [!NOTE]
> macOSと異なり、Linuxでは`mv`は上書き前に確認を求めません。
> 安全のため`-i`オプションを使いましょう：`mv -i oldname.txt newname.txt`

---

### ファイルを削除する - `rm`

**rm** = **R**e**m**ove（削除）

> [!WARNING]
> **削除は取り消せません！**
>
> Linuxには「ごみ箱」の概念がないため、`rm`で削除したファイルは復元できません。
> 削除前に必ず確認しましょう！

```bash
# ファイルを削除（確認あり）
rm -i file1.txt

# 複数ファイルを削除
rm file1.txt file2.txt

# 空のディレクトリを削除
rmdir empty_folder

# ディレクトリと中身をすべて削除（-r = recursive）
rm -r myfolder

# 強制削除（-f = force、確認なし）※危険！
rm -rf myfolder
```

> [!DANGER]
> **絶対に実行してはいけない「バルスコマンド」**
>
> ```bash
> sudo rm -rf /
> ```
>
> これは**システム全体を削除**してしまいます！
> - `sudo`: 管理者権限で実行
> - `rm`: 削除
> - `-r`: ディレクトリごと
> - `-f`: 強制（確認なし）
> - `/`: ルートディレクトリ（すべて）
>
> 実行すると、OSが起動できなくなります。絶対に実行しないでください！

---

## 4. ファイル内容の表示

### ファイルの全内容を表示する - `cat`

**cat** = con**CAT**enate（連結）

```bash
# ファイルの内容を表示
cat file1.txt

# 複数ファイルを連結して表示
cat file1.txt file2.txt

# 行番号付きで表示
cat -n file1.txt
```

---

### ページごとに表示する - `less`

長いファイルを見やすく表示します：

```bash
less largefile.txt
```

**操作方法**:
- `Space`: 次のページ
- `b`: 前のページ
- `/検索語`: 検索
- `q`: 終了

---

### ファイルの先頭/末尾を表示する

```bash
# 先頭10行を表示
head file1.txt

# 先頭20行を表示
head -n 20 file1.txt

# 末尾10行を表示
tail file1.txt

# リアルタイムで更新を表示（ログファイルの監視）
tail -f /var/log/syslog
```

---

## 5. パーミッション（権限）の基礎

### パーミッションとは

Linuxでは、ファイルやディレクトリに**誰が何をできるか**を設定できます。

```bash
$ ls -l
-rw-r--r-- 1 user group  1234 Nov 14 10:30 file1.txt
drwxr-xr-x 2 user group  4096 Nov 14 10:29 Documents
```

**パーミッションの読み方**:

```
-rw-r--r--
│││ │ │ │
│││ │ │ └─ その他のユーザーの権限（r-- = 読み取りのみ）
│││ │ └─── グループの権限（r-- = 読み取りのみ）
│││ └───── 所有者の権限（rw- = 読み書き可）
││└─────── ファイルタイプ（- = ファイル、d = ディレクトリ）
```

**権限の種類**:
- `r`: **Read**（読み取り）
- `w`: **Write**（書き込み）
- `x`: **eXecute**（実行）

### パーミッションを変更する - `chmod`

```bash
# 所有者に実行権限を追加
chmod u+x script.sh

# すべてのユーザーに読み取り権限を追加
chmod a+r file.txt

# 数値で指定（755 = rwxr-xr-x）
chmod 755 script.sh
```

**数値の意味**:
- `7` = `rwx`（4+2+1）
- `6` = `rw-`（4+2）
- `5` = `r-x`（4+1）
- `4` = `r--`

---

## 6. 便利な機能

### コマンド履歴

```bash
# コマンド履歴を表示
history

# 最後の20件を表示
history 20

# 直前のコマンドを再実行
!!

# 履歴番号123のコマンドを実行
!123

# "git"で始まる最後のコマンドを実行
!git
```

### エイリアス（ショートカット）

よく使うコマンドに短い名前をつけられます：

```bash
# エイリアスを設定
alias ll='ls -lah'
alias ..='cd ..'

# 設定を永続化（.bashrcに追加）
echo "alias ll='ls -lah'" >> ~/.bashrc
```

### ワイルドカード

```bash
# .txtで終わるすべてのファイル
ls *.txt

# file で始まるすべてのファイル
ls file*

# 1文字だけマッチ
ls file?.txt

# 複数パターン
ls *.{txt,md}
```

---

## 7. パッケージマネージャー

### Ubuntu/Debian - apt

```bash
# パッケージリストを更新
sudo apt update

# パッケージをインストール
sudo apt install vim

# パッケージを削除
sudo apt remove vim

# システムをアップグレード
sudo apt upgrade
```

### Fedora/RHEL - dnf/yum

```bash
# パッケージをインストール
sudo dnf install vim

# パッケージを削除
sudo dnf remove vim
```

### Arch Linux - pacman

```bash
# パッケージをインストール
sudo pacman -S vim

# パッケージを削除
sudo pacman -R vim
```

---

## 8. ヘルプとマニュアル

### ヘルプを表示する

```bash
# コマンドのヘルプを表示
ls --help

# マニュアルページを表示
man ls

# コマンドの簡単な説明
whatis ls

# コマンドの場所を表示
which ls
```

---

## 付録: コマンド早見表

| コマンド | 機能 | 例 |
|---------|------|-----|
| `pwd` | 現在位置確認 | `pwd` |
| `ls` | ファイル一覧 | `ls -la` |
| `cd` | 場所移動 | `cd Documents` |
| `mkdir` | ディレクトリ作成 | `mkdir -p a/b/c` |
| `touch` | ファイル作成 | `touch file.txt` |
| `cp` | コピー | `cp -r src dst` |
| `mv` | 移動/リネーム | `mv old new` |
| `rm` | 削除 | `rm -i file.txt` |
| `cat` | ファイル内容表示 | `cat file.txt` |
| `less` | ページ表示 | `less file.txt` |
| `head` | 先頭表示 | `head -n 20 file.txt` |
| `tail` | 末尾表示 | `tail -f log.txt` |
| `chmod` | 権限変更 | `chmod 755 script.sh` |
| `history` | コマンド履歴 | `history 20` |
| `man` | マニュアル表示 | `man ls` |

---

## まとめ

Linux CLIの基本を学びました！

✅ ターミナルの起動と基本操作
✅ ファイル・ディレクトリの操作
✅ パーミッション（権限）の理解
✅ コマンド履歴とワイルドカード
✅ パッケージマネージャーの使い方

次の章では、さらに高度なファイル操作とテキスト処理を学びます！

---

**参考リソース**:
- [Linux 入門 - Zenn](https://zenn.dev/farstep/books/linux-beginners-guide)
- [The Linux Command Line (日本語版)](https://linuxcommand.org/)
- Ubuntu公式ドキュメント

---

**前へ**: [01. 基礎編 README](./README.md)
**次へ**: [02. ファイル操作編](../02_ファイル操作編/README.md)
