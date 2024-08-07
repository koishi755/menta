# シェルとは
シェルはコマンドインタプリタで、標準入力またはファイルから読み込んだコマンド実行すします。

![](https://storage.googleapis.com/zenn-user-upload/212829100fbe-20240717.png)


## シェルの変数
シェルには環境を調整する変数としてシェル変数と環境変数があります。

変数|説明
-|-
シェル変数|設定されたシェルだけが使用できる変数。子プロセスに引き継がれない。
環境変数|設定されたシェルとそのシェルで起動したプログラムが使用する変数。シェル変数をエクスポート宣言することで作成する。

<br>

主なシェル変数
変数名|説明
-|-
PWD|カレントディレクトリの絶対パス
PATH|コマンド検索パス
HOME|ユーザーのホームディレクトリ
PS1|プロンプトを定義
HISTFILE|コマンド履歴を格納するファイルを定義
LANG|言語情報

### 変数の設定、削除

現在のLANG変数を確認
```
echo $LANG
```

<br>

LANG変数を削除
```
unset LANG
```
<br>

LANG変数を設定
```
LANG=C; export LANG
```



<br>
実行例
LANG変数を削除した後、dateコマンドの結果が英語となります。


```bash
# 現在の環境変数LANGが日本語となっていることを確認
[testuser@localhost ~]$ echo $LANG
ja_JP.UTF-8

# dateコマンドを実行した時、日本語で表示されます。
[testuser@localhost ~]$ date
2024年  7月 26日 金曜日 19:52:29 JST

# LANG変数を削除
[testuser@localhost ~]$ unset LANG

# LANG変数を確認すると何もないことが分かります。
[testuser@localhost ~]$ echo $LANG

# dateコマンドを実行すると英語表記になりました。
[testuser@localhost ~]$ date
Fri Jul 26 19:52:51 JST 2024
```

## 環境変数を設定

```
export LANG=ja_JP.UTF-8
```
<br>
環境変数にLANGが設定されたか確認
```
printenv | grep LANG
```

![](https://storage.googleapis.com/zenn-user-upload/fb9d1c2b1333-20240717.png)

## コマンドの履歴

シェルから実行したコマンドの実行履歴を「history」コマンドで確認することができます。またキーの操作で履歴を呼び出すことができます。

キー操作|説明
-|-
↑（上矢印キー）|1つ前のコマンドを表示する
↓（下矢印キー）|1つ後のコマンドを表示する
!!|直前に実行したコマンドを実行する
!履歴番号|指定した履歴番号のコマンドを実行する
!文字列|指定した文字列から始まる直近のコマンドを実行する

コマンドの履歴はデフォルトではホームディレクトリの.bash_historyファイルに格納されています。

# マニュアル

/usr/share/manディレクトリ配下にマニュアルページが置かれています。


オプション | 説明
-|- 
-a| 指定したすべてのマニュアルページを表示します。
-k| 指定したキーワードに関連するマニュアルページの簡単な説明を検索します（`apropos`と同じ）。
-f| 指定したコマンドやプログラムに関連するマニュアルページを一覧表示します（`whatis`と同じ）。
-l| 指定したファイル名をマニュアルページとして表示します。
-s| 指定したセクション番号のマニュアルページを検索して表示します。
-C| 指定した設定ファイルを使用して`man`を実行します。
--help| `man`コマンドのヘルプメッセージを表示します。
--version| `man`コマンドのバージョン情報を表示します。

<br>

今回はlsのマニュアルを開きます。


```
man ls
```

<br>


以下のような画面が開けたと思います。

![](https://storage.googleapis.com/zenn-user-upload/f810e27425a3-20240718.png)

<br>

マニュアルページでは矢印キーでの操作の他、別のキーで操作することができます。

キー操作|説明
-|-
Enter|次の行を表示
スペース|次のページを表示
b|前のページを表示
h|ヘルプを表示
q|manコマンドの終了
/文字列|指定した文字列でマニュアルページ内を検索


<br>

## トラブルシューティング

以下のようなメッセージが出た場合、manデータベースが適切に設定されていない可能性があります。この場合、manデータベースを更新する必要があります。CentOS 9では、mandbコマンドを使用してデータベースを更新できます。

```
[root@localhost ~]# man -f ls
ls: 適切なものはありませんでした。
```

<br>

まず、mandbコマンドを使用してデータベースを更新します。

```
sudo mandb
```


```
[root@localhost ~]# man -f ls
ls (1)               - list directory contents
ls (1p)              - list directory contents
```


ls (1): 標準のユーザコマンドについてのマニュアルページです。これは通常、Linuxディストリビューションが提供するデフォルトのlsコマンドについて説明しています。


```
man 1 ls
```


ls (1p): POSIX（Portable Operating System Interface）の標準に準拠したコマンドについてのマニュアルページです。POSIXは、Unix互換システム間の互換性を確保するための一連の標準規格です。ls (1p)は、POSIX標準に準拠したlsコマンドの仕様を説明しています。


```
man 1p ls
```

# ユーザーの切り替え

「su」コマンドで現在のユーザーを別のユーザーに切り替えることができます。<br>
rootユーザーに切り替える

```
su -
```
<br>
指定したユーザーに切り替える

```
su - [ユーザー名]
```

<br>
また「su」コマンドを使用する際に、何も指定しない場合はインタラクティブシェルが起動され、「su -」を使用するとログインシェルが起動されるという違いがあります。


**インタラクティブシェル**
インタラクティブシェルとは、ユーザーが直接コマンドを入力し、その結果を即座に見ることができるシェルです。インタラクティブシェルでは、ユーザーとシェルが対話的にやり取りするため、ユーザーが一つずつコマンドを入力し、それに対する出力を見ながら次のコマンドを入力する形になります。

**ログインシェル**
ログインシェルとは、ユーザーがシステムにログインした時に最初に起動されるシェルのことです。ログインシェルは、システム全体の環境設定を読み込むための特別な設定があります。


以下は「su」でrootユーザーに切り替え、インタラクティブシェルが起動されています。こちらではtestuserのホームディレクトリが移動していないことが分かります。

![](https://storage.googleapis.com/zenn-user-upload/8eb2fd3042f1-20240718.png)

## シェルの設定ファイル(bash)

### ログインシェルの設定ファイル

以下のファイルは、ログインシェルの起動時に実行され、ユーザーのシェル環境を設定するために使用されます。

| 順序 | ファイル名               | 説明                                                       |
|------|-------------------------|------------------------------------------------------------|
| 1    | /etc/profile         | システム全体のログインシェルの初期化設定を行う。           |
| 2    | ~/.bash_profile     | ユーザーのホームディレクトリにあるログインシェルの初期化設定。|
| 3    | ~/.bash_login         | `~/.bash_profile` が存在しない場合に実行される。            |
| 4    | ~/.profile            | `~/.bash_profile` と `~/.bash_login` が存在しない場合に実行される。|


### 非ログインシェル、インタラクティブシェル

非ログインシェル、インタラクティブシェルが起動されると「~/.bashrc」が実行されます。

**非ログインシェル**
非ログインシェルは、ユーザーがシステムにログインしていない状態で起動されるシェルです。通常、GUI環境からターミナルを開いたときや、既にログインしているセッションから新しいシェルを開始したときに起動されます。


# ファイルとディレクトリの管理

ファイルとディレクトリの操作について確認します。

## カレントディレクトリ

![](https://storage.googleapis.com/zenn-user-upload/09d01468122c-20240719.png)

**カレントディレクトリとは**
カレントディレクトリとは、ユーザーが現在作業を行っているディレクトリのことです。システム上のファイルやディレクトリを操作する際の基準となる場所を指します。

**カレントディレクトリの確認方法**
カレントディレクトリを確認するためには、pwd（print working directory）コマンドを使用します。

```bash:カレントディレクトリの表示
pwd
```

**ディレクトリの移動**
カレントディレクトリを変更するためには、cd（change directory）コマンドを使用します。

```bash:ディレクトリの移動
cd [任意のディレクトリ]
```


**特殊なディレクトリ**

いくつかの特殊なディレクトリもカレントディレクトリの変更に使用できます。

記号|説明
-|-
.|現在のディレクトリを表します。
..|親ディレクトリを表します。
~|ホームディレクトリを表します。

## ファイル、ディレクトリの情報を表示

ファイルやディレクトリの情報を表示するには、 ls コマンドを使用します。


ディレクトリ情報の表示
```bash:ディレクトリ情報の表示
ls [オプション] [ディレクトリ名]
```
<br>
ファイル情報の表示
```bash:ファイル情報の表示
ls [オプション] [ファイル名]
```

<br>

**オプション**
| オプション              | 説明                                                                                      |
|-------------------------|-------------------------------------------------------------------------------------------|
| -a, --all               | `.` で始まるエントリーを無視しない。                                                         |
| -d, --directory         | ディレクトリ自体を表示する。                                                                |
| -h, --human-readable    | -l と -s と併用時、サイズを 1K、234M、2G などの形式で表示する。                                |
| -i, --inode             | 各ファイルのインデックス番号を表示する。                                                     |
| -l                      | 長いリスト形式を使用する。                                                                  |
| -L, --dereference       | シンボリックリンクのファイル情報を表示する際、リンクではなくそのファイルの情報を表示する。     |
| -r, --reverse           | ソート順序を逆にする。                                                                      |
| -R, --recursive         | サブディレクトリを再帰的にリストする。                                                       |
| -s, --size              | 各ファイルの割り当てられたサイズ（ブロック単位）を表示する。                                   |
| -S                      | ファイルサイズでソートし、最大のものから順に表示する。                                         |
| --sort=WORD             | 名前の代わりにWORDでソートする。none (-U)、size (-S)、time (-t)、version (-v)、extension (-X)が指定可能。 |
| --time=WORD             | デフォルトの変更時間を使用する。-l と併用時、WORDは表示する時間を決定する。--sort=time と併用時、WORDでソートする（新しい順）。 |
| --time-style=TIME_STYLE | -l と併用時の時間/日付の形式を指定する。                                                     |
| -t                      | 時間でソートし、最新のものから表示する。                                                       |
| -v                      | テキスト内の（バージョン）番号を自然な順


実行例

```bash
ls -lah --sort=time --time=ctime
```

![](https://storage.googleapis.com/zenn-user-upload/ac1578f4e35f-20240718.png)

<br>

## ファイルの内容を表示

ファイルの内容を表示するには「cat」コマンドなどを使用します。

### ファイルの内容を連結して表示

cat コマンドは、ファイルの内容を標準出力に連結して表示するためのコマンドです。cat は "concatenate"（連結）の略です。

```bash:ファイルの内容を連結して表示
cat [オプション] [ファイル名]
```

**オプション**

| オプション                  | 説明                                                         |
|-----------------------------|--------------------------------------------------------------|
| -A, --show-all          | -vET と同等                                                |
| -b, --number-nonblank   | 空行を除いて行番号を付ける。-n を無効にする                |
| -e                        | -vE と同等                                                |
| -E, --show-ends         | 各行の末尾に $ を表示する                                 |
| -n, --number            | すべての出力行に行番号を付ける                              |
| -s, --squeeze-blank     | 連続する空行を抑制する                                      |
| -t                        | -vT と同等                                                |
| -T, --show-tabs         | タブ文字を ^I として表示する                              |
| -v, --show-nonprinting  | 改行（LFD）とタブ（TAB）以外の非表示文字を ^ 記号と M- 表記で表示する |


ファイルが指定されていない場合、またはファイルが - の場合、標準入力を読み取ります。

![](https://storage.googleapis.com/zenn-user-upload/a6fd149686bd-20240718.png)

### ファイルの内容を表示

moreは、テキストを一度に1画面分ずつページ送りするためのコマンドです。

```bash:ファイルの内容を表示
more [オプション] ファイル
```

<br>

lessもファイルの内容を表示させるためのコマンドです。less は more(1) に似たプログラムですが、ファイル内を前方だけでなく後方にも移動できます。また、less はテキストエディタのように全体のファイルを読み込む必要がないため、大きな入力ファイルでも高速に起動します。

```bash:ファイルの内容を表示
less ファイル
```

### 行番号をつけてファイルの内容を表示


行番号をつけてファイルの内容を表示するには nl コマンドを使用します。

```bash:行番号をつけてファイルの内容を表示
nl [オプション]... [ファイル]...
```

---------

**nl コマンドと cat -n の違い**


**nl コマンド**
- nl コマンドは、ファイルに行番号を付けて出力しますが、オプションを用いて行番号のスタイルや範囲を指定することができます。例えば、空行の扱いやヘッダー・フッターの行にも番号を付けることができます。
- より柔軟にカスタマイズ可能であり、さまざまな形式で行番号を挿入できます（全ての行に番号を挿入するか、空行を除外するかなど）。
  
**cat -n コマンド**

- cat -n は cat コマンドに -n オプションを付けて使う方法で、単純にファイルの各行に連番を挿入します。
- 行番号の挿入が基本的であり、オプションでスタイルや番号のフォーマットを変更することはできません。

## ファイル、ディレクトリの作成、移動、コピー、削除

### ファイル、ディレクトリの作成

#### ディレクトリの作成

ディレクトリの作成には mkdir コマンドを使用します。

```bash:ディレクトリの作成
mkdir [オプション] ディレクトリ
```

<br>

**オプション**

| オプション           | 説明                                                                                   |
|----------------------|----------------------------------------------------------------------------------------|
| -m, --mode=MODE      | ファイルモードを設定します（chmod と同様）。a=rwx - umask ではありません。                        |
| -p, --parents        | 親ディレクトリが必要であれば作成し、既存の場合はエラーになりません。                              |
| -v, --verbose        | それぞれの作成されたディレクトリについてメッセージを表示します。                                    |


<br>

**実行例**

```bash
mkdir -pv dir1/dir2
```

```bash
[testuser@localhost work]$ mkdir -pv dir1/dir2
mkdir: ディレクトリ 'dir1' を作成しました
mkdir: ディレクトリ 'dir1/dir2' を作成しました
```

#### ファイルのタイムスタンプを変更

touchコマンドでファイルのタイムスタンプを変更することができます。
存在しないファイルを指定した時、-cが指定されていない場合は空のファイルとして作成されます。

```bash:ファイルのタイムスタンプを変更
touch [オプション] ファイル
```

<br>

**オプション**
| オプション       | 説明                                                                                  |
|------------------|--------------------------------------------------------------------------------------|
| -a               | アクセス時間のみ変更します                                                           |
| -c, --no-create  | ファイルを作成しません                                                               |
| -m               | 修正時間のみ変更します                                                               |
| -r, --reference=FILE | このファイルの時間を現在時刻の代わりに使用します                                        |
| -t STAMP         | [[CC]YY]MMDDhhmm[.ss]の代わりに現在時刻を使用します                                    |


**実行例**

```bash
[testuser@localhost work]$ stat test1
  File: test1
  Size: 0               Blocks: 0          IO Block: 4096   通常の空ファイル
Device: fd00h/64768d    Inode: 103833085   Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/testuser)   Gid: ( 1000/testuser)
Context: unconfined_u:object_r:user_home_t:s0
Access: 2024-07-18 18:47:19.000000000 +0900
Modify: 2024-07-18 18:47:19.000000000 +0900
Change: 2024-07-18 18:47:19.147337824 +0900
 Birth: 2024-07-18 18:34:58.026924329 +0900
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ touch -a test1
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ stat test1
  File: test1
  Size: 0               Blocks: 0          IO Block: 4096   通常の空ファイル
Device: fd00h/64768d    Inode: 103833085   Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/testuser)   Gid: ( 1000/testuser)
Context: unconfined_u:object_r:user_home_t:s0
Access: 2024-07-18 18:48:34.290435416 +0900
Modify: 2024-07-18 18:47:19.000000000 +0900
Change: 2024-07-18 18:48:34.290435416 +0900
 Birth: 2024-07-18 18:34:58.026924329 +0900
```

### ファイル、ディレクトリの移動

ファイル、ディレクトリの移動には mv コマンドを使用します。主に以下の使用用途があります。

ファイルの移動
```bash:ファイルの移動
mv [OPTION] 移動元ファイル 移動先ディレクトリ
```

ファイルの名前変更
```bash:ファイルの名前変更
mv [OPTION] 移動元ファイル 移動先ファイル
```

ディレクトリの移動
```bash:ディレクトリの移動
mv [OPTION] 移動元ディレクトリ 移動先ディレクトリ
```

<br>


**オプション**
| オプション           | 説明                                                                                   |
|----------------------|---------------------------------------------------------------------------------------|
| --backup[=CONTROL]   | 各既存の宛先ファイルにバックアップを作成します。                                         |
| -b                   | --backup と同様ですが、引数は受け付けません。                                           |
| -f, --force          | 上書きする前に確認せずに実行します。                                                    |
| -i, --interactive    | 上書きする前に確認します。                                                             |
| -n, --no-clobber     | 既存のファイルを上書きしません。                                                        |
| -t, --target-directory=DIRECTORY | 全ての SOURCE 引数を DIRECTORY に移動します。                                   |
| -T, --no-target-directory | DEST を通常のファイルとして扱います。                                                 |
| -u, --update         | SOURCE ファイルが宛先ファイルより新しい場合、または宛先ファイルが存在しない場合にのみ移動します。 |
| -v, --verbose        | 実行されている操作の説明を表示します。                                                  |
| -Z, --context        | 宛先ファイルの SELinux セキュリティコンテキストをデフォルトのタイプに設定します。           |

<br>

**実行例**

```
[testuser@localhost work]$ ll
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 18 19:08 test
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ mv test test1
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ ll
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 18 19:08 test1
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ touch test
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ mv --backup=numbered test test1
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ ll
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 18 19:09 test1
-rw-r--r--. 1 testuser testuser 0  7月 18 19:08 test1.~1~
```

### ファイル、ディレクトリのコピー

ファイルやディレクトリを複製する場合は cp コマンドを使用します。


ファイルのコピー
```bash:ファイルのコピー
cp [OPTION] コピー元ファイル コピー先ファイル
```

ファイルのコピー
```bash:ファイルのコピー
cp [OPTION] コピー元ファイル コピー先ディレクトリ
```


ディレクトリのコピー
```bash:ディレクトリのコピー
cp [OPTION] コピー元ディレクトリ コピー先ディレクトリ
```

**オプション**
| オプション                 | 説明                                                                                  |
|----------------------------|--------------------------------------------------------------------------------------|
| --backup[=CONTROL]         | 各既存の宛先ファイルにバックアップを作成します                                        |
| -b                         | --backup と同じですが、引数を受け付けません                                           |
| -f, --force                | 既存の宛先ファイルを開けない場合に削除して再試行します（-n オプションが使用されている場合は無視されます） |
| -i, --interactive          | 上書きする前に確認します（前の -n オプションを上書きします）                            |
| -H                         | SOURCE のコマンドラインシンボリックリンクをたどります                                  |
| -l, --link                 | ファイルをコピーする代わりにハードリンクを作成します                                   |
| -n, --no-clobber           | 既存のファイルを上書きしません（前の -i オプションを上書きします）                       |
| -p                         | -preserve=mode,ownership,timestamps と同じ                                             |
| --preserve[=ATTR_LIST]     | 指定した属性を保持します（デフォルト: mode,ownership,timestamps）、追加属性: context, links, xattr, all |
| -R, -r, --recursive        | ディレクトリを再帰的にコピーします                                                     |
| -u, --update               | SOURCE ファイルが目的地ファイルより新しい場合、または目的地ファイルが存在しない場合にのみコピーします |

<br>

**ファイルコピー実行例**

```bash
[testuser@localhost work]$ touch test1 # test用のファイルを作成
[testuser@localhost work]$
[testuser@localhost work]$ cp -p test1 test1_copy # test1をtest1_copyとしてコピーをする
[testuser@localhost work]$
[testuser@localhost work]$ ll # 以下のようにtest1_copyが作成されたことが確認できる
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 18 23:23 test1
-rw-r--r--. 1 testuser testuser 0  7月 18 23:23 test1_copy
```

<br>

**ディレクトリコピー実行例**

```bash
[testuser@localhost work]$ ll # カレントディレクトリにdir1、dir2があることを確認
合計 0
drwxr-xr-x. 3 testuser testuser 18  7月 18 23:28 dir1
drwxr-xr-x. 3 testuser testuser 18  7月 18 23:28 dir2
[testuser@localhost work]$
[testuser@localhost work]$ cp -rp dir1 dir2 # dir1をdir2/配下にコピーする
[testuser@localhost work]$
[testuser@localhost work]$ ll dir2 # dir2配下にdir1がコピーされたことを確認
合計 0
drwxr-xr-x. 3 testuser testuser 18  7月 18 23:28 dir1
```

### ファイル、ディレクトリの削除

ファイルやディレクトリを削除するにはrmコマンドを使用します。

ファイルの削除
```bash:ファイルの削除
rm [オプション] ファイル
```

ディレクトリの削除
```bash:ディレクトリの削除
rm [オプション] ディレクトリ
```

<br>

**オプション**

| オプション       | 説明                 |
|------------------|---------------------------------------|
| -f, --force      | 存在しないファイルや引数を無視し、プロンプトを表示しない         |
| -i               | 削除する前に毎回確認する                                   |
| -r, -R, --recursive | ディレクトリとその内容を再帰的に削除する            |
| -d, --dir        | 空のディレクトリを削除する             |
| -v, --verbose    | 実行中の操作を表示する |

<br>

**実行例**

```
[testuser@localhost work]$ ll # カレントディレクトリにdir1ディレクトリが存在している
合計 0
drwxr-xr-x. 3 testuser testuser 18  7月 18 23:46 dir1
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ rm dir1/ # オプション無しだとディレクトリが削除できない
rm: 'dir1/' を削除できません: ディレクトリです
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ rm -r dir1/ # -r オプションをつけて実行
[testuser@localhost work]$
[testuser@localhost work]$
[testuser@localhost work]$ ll # dir1が削除されたことを確認
合計 0
```

## アーカイブ

「アーカイブ」とは、複数のファイルやディレクトリを1つのファイルにまとめることを指します。通常、アーカイブはバックアップやファイルの転送、圧縮のために使用されます。tarコマンドでアーカイブを管理することができます。

<br>
**アーカイブの作成**
```bash:アーカイブの作成
tar -cvf アーカイブファイル アーカイブ対象のディレクトリ
```

アーカイブの展開
```bash:アーカイブの展開
tar -xvf アーカイブファイル
```


<br>

| オプション                           | 説明                                                                                   |
|------------------------------------|----------------------------------------------------------------------------------------|
| -c, --create                        | アーカイブを作成します。                                                                       |
| -f, --file=ARCHIVE                  | 操作対象のアーカイブファイルを指定します。                                                     |
| -v, --verbose                       | 処理の詳細を表示します。                                                                       |
| -z, --gzip                          | gzipで圧縮または解凍します。                                                                   |
| -j, --bzip2                         | bzip2で圧縮または解凍します。                                                                  |
| -t, --list                          | アーカイブ内のファイルを表示します。                                                             |
| -x, --extract                       | アーカイブを解凍します。                                                                       |
| -d, --diff                          | アーカイブと現在のファイルシステムとの差分を表示します。                                            |
| -u, --update                        | アーカイブ内のファイルが新しい場合にのみ更新します。                                               |
| -r, --append                        | アーカイブにファイルを追加します。                                                               |
| --delete                            | アーカイブからファイルを削除します。                                                             |
| --exclude=PATTERN                   | 特定のパターンに一致するファイルを除外します。                                                     |
| --wildcards                         | ワイルドカードによるファイル指定を許可します。                                                     |
| --strip-components=NUMBER           | アーカイブ内のファイル名から指定した数のディレクトリ階層を削除します。                                 |
| --directory=DIR                     | アーカイブを展開するディレクトリを指定します。                                                     |
| --totals                            | 処理の終了時に統計情報を表示します。                                                             |
| -C, --directory=DIR                 | 作業ディレクトリを変更してから操作を実行します。|

<br>
**実行例（アーカイブ作成）**

```bash
# テスト用ファイルの作成
[testuser@localhost work]$ mkdir -p dir1/dir2 && touch dir1/test1 dir1/test2 dir1/dir2/test3 dir1/dir2/test4

# カレントディレクトリに存在するディレクトリとファイルの構造を確認
[testuser@localhost work]$ tree dir1/ 
dir1/
├── dir2
│   ├── test3
│   └── test4
├── test1
└── test2

# dir1をtest_archive.tarという名前でアーカイブする
[testuser@localhost work]$ tar -cvf test_archive.tar dir1/ 
dir1/
dir1/test1
dir1/test2
dir1/dir2/
dir1/dir2/test3
dir1/dir2/test4
[testuser@localhost work]$

# アーカイブ（test_archive.tar）が作成されたことを確認
[testuser@localhost work]$ ll
合計 12
drwxr-xr-x. 3 testuser testuser    44  7月 19 00:18 dir1
-rw-r--r--. 1 testuser testuser 10240  7月 19 00:23 test_archive.tar
```
<br>

**展開（アーカイブ展開）**

```bash
# もともとあったdir1を削除
[testuser@localhost work]$ rm -r dir1/
[testuser@localhost work]$

# test_archive.tar を展開
[testuser@localhost work]$ tar -xvf test_archive.tar
dir1/
dir1/test1
dir1/test2
dir1/dir2/
dir1/dir2/test3
dir1/dir2/test4
[testuser@localhost work]$

# 展開されたことを確認
[testuser@localhost work]$ ll
合計 12
drwxr-xr-x. 3 testuser testuser    44  7月 19 00:18 dir1
-rw-r--r--. 1 testuser testuser 10240  7月 19 00:23 test_archive.tar
```

## ファイルの変換とコピー

ddコマンドは、UNIXおよびUnix系システムで利用されるユーティリティコマンドで、ファイルのコピーと変換を行うためのツールです。主な機能として以下のようなことが挙げられます：

- ファイルのコピー: ファイルやデバイスから別のファイルやデバイスにデータをコピーします。
- データの変換: コピーする際に、データの形式やエンディアンを変換することができます。
- ブロック単位の操作: データの読み込みや書き込みをブロック単位で行い、パフォーマンスを調整することが可能です。


**オプション**

| オプション | 説明 |
|------------|------|
| if       | 入力ファイルを指定します |
| of       | 出力ファイルを指定します|
| bs       | ブロックサイズを指定します|

<br>

**ファイルのコピー**

```bash
dd if=入力ファイル of=出力ファイル bs=4k
```

<br>

**デバイスのバックアップ**

```bash
dd if=/dev/sda of=backup.img bs=1M
```

<br>

**デバイスのゼロ化**

```bash
dd if=/dev/zero of=/dev/sdb bs=1M
```

<br>

**ダミーファイルの作成**

```bash
dd if=/dev/zero of=dummyfile bs=1M count=1
```


## 重要なディレクトリ

| ディレクトリ | 説明 |
|--------------|------|
| /          | ルートディレクトリ。全てのディレクトリとファイルの最上位。 |
| /bin       | 基本的なユーザーコマンドのバイナリファイルが格納されている。 |
| /boot      | システムの起動に必要なファイルが格納されている。カーネルイメージやブートローダーの設定ファイルが含まれている。 |
| /dev       | システム上のデバイスファイルが格納されている。 |
| /etc       | システム全体の設定ファイルが格納されている。ほとんどのソフトウェアの設定ファイルやシステムの設定スクリプトがここに置かれる。 |
| /home      | 各ユーザーのホームディレクトリが格納される場所。ユーザーごとにディレクトリが作成され、個人のデータや設定ファイルが保存される。 |
| /lib       | システムの基本的なライブラリファイルが格納されている。特に、/binや/sbinにあるプログラムが必要とするライブラリがここに置かれる。 |
| /media     | リムーバブルメディア（例：CD-ROM、USBドライブ）を自動的にマウントするためのマウントポイントが格納されている。 |
| /mnt       | 一時的にマウントされたファイルシステムのマウントポイントとして使用される。手動でマウントする場合に利用されることが多い。 |
| /opt       | オプションのソフトウェアパッケージが格納されるディレクトリ。システムのデフォルトではない追加のアプリケーションがインストールされることが多い。 |
| /proc      | システムプロセスやカーネルの情報が格納されている仮想ファイルシステム。プロセス情報やシステム状態をリアルタイムで確認するために使用される。 |
| /root      | システム管理者（rootユーザー）のホームディレクトリ。通常のユーザーのホームディレクトリは/homeにありますが、rootユーザーのホームディレクトリは/rootにある。 |
| /run       | システムの初期化後、ブートプロセス中やデーモンの稼働中に必要な一時ファイルが格納される一時ファイルシステム。 |
| /sbin      | システム管理用のバイナリファイルが格納されているディレクトリ。主にシステム管理者が使用するコマンドが含まれている。 |
| /srv       | サーバーデータが格納されるディレクトリ。例えば、WebサーバやFTPサーバのデータがここに置かれる。 |
| /tmp       | 一時ファイルが格納されるディレクトリ。システムやアプリケーションが一時的に使用するファイルがここに保存される。再起動時には通常クリアされる。 |
| /usr       | ユーザーが使用するアプリケーションやライブラリが格納されるディレクトリ。以下のようなサブディレクトリがある: <br> - /usr/bin: ユーザー向けアプリケーションのバイナリファイル。 <br> - /usr/lib: ユーザー向けアプリケーションのライブラリファイル。 <br> - /usr/local: ローカルにインストールされたソフトウェアパッケージ。 <br> - /usr/share: アーキテクチャに依存しない共有データ。 |
| /var       | 変動するデータ（ログファイル、メールキュー、スプールファイルなど）が格納されるディレクトリ。以下のようなサブディレクトリがある: <br> - /var/log: システムログファイル。 <br> - /var/mail: メールスプール。 <br> - /var/spool: 一時的に保存されるデータ（印刷ジョブ、メールジョブなど）。 <br> - /var/tmp: より長期にわたって保存される一時ファイル。 |

## シンボリックリンクとハードリンク

### シンボリックリンク

シンボリックリンクは、ファイルシステム内の別のファイルやディレクトリへのポインタを提供する特殊なファイルです。Windowsのショートカットのようなものです。

#### 特徴

| **特徴** | **説明** |
|----------|----------|
| パスを記憶 | シンボリックリンクは、リンク先のファイルやディレクトリの場所（パス）を記憶します。 |
| デッドリンク | リンク先のファイルやディレクトリが削除されたり移動されたりすると、シンボリックリンクは壊れたリンク（デッドリンク）になります。 |
| 異なるファイルシステム | リンク先のファイルやディレクトリが異なるファイルシステムに存在していてもシンボリックリンクを作成できます。 |

#### 作成方法

シンボリックリンクの作成にはlnコマンドを使用します。


```bash:書式
ln [オプション]... [-T] ターゲット リンク名
```

オプション

| オプション | 説明 |
| - | - |
| --backup[=CONTROL] | 既存の宛先ファイルのバックアップを作成 |
| -b | --backup と同様だが引数を受け付けない |
| -d, -F, --directory | スーパーユーザーがディレクトリにハードリンクを試みることを許可（システム制限のため失敗する可能性あり） |
| -f, --force | 既存の宛先ファイルを削除 |
| -i, --interactive | 宛先を削除するかどうかを確認 |
| -L, --logical | シンボリックリンクのターゲットを参照 |
| -n, --no-dereference | リンク名がディレクトリへのシンボリックリンクである場合、通常のファイルとして扱う |
| -P, --physical | シンボリックリンクに直接ハードリンクを作成 |
| -s, --symbolic | シンボリックリンクを作成 |
| -v, --verbose | 各リンクされたファイルの名前を表示 |

実行例

```bash
# 現在のディレクトリに何もないことを確認
[testuser@localhost work]$ ls -l
合計 0

# テスト用のディレクトリ、ファイルを作成
[testuser@localhost work]$ mkdir link-test
[testuser@localhost work]$ touch link-test/test-file

# シンボリックリンクを作成
[testuser@localhost work]$ ln -s link-test/test-file test-file-link

# シンボリックリンクが作成されたことを確認
[testuser@localhost work]$ ls -l
合計 0
drwxr-xr-x. 2 testuser testuser 23  7月 26 15:25 link-test
lrwxrwxrwx. 1 testuser testuser 19  7月 26 15:25 test-file-link -> link-test/test-file
```

### ハードリンク

#### 特徴

- ハードリンクは、同じファイルシステム内の既存のファイルに対する別の参照を提供します。ハードリンクは、リンク元のファイルのデータブロックを共有します。
- ハードリンクは、ファイルが削除されても他のリンクからアクセス可能である限りデータを保持します。
- ハードリンクは、同じファイルシステム内でのみ作成可能です。

#### 作成方法

ハードリンクは、ln コマンドを使用して作成します。以下の例では、target_file というファイルに対するハードリンク hardlink を作成します。

```
ln target_file hardlink
```

```bash
# カレントディレクトリに何もないことを確認
[testuser@localhost work]$ ls -l
合計 0

# テスト用ファイルの作成
[testuser@localhost work]$ mkdir hard-link-test && touch hard-link-test/test-link

# ハードリンクの作成
[testuser@localhost work]$ ln hard-link-test/test-link hardlink

# ハードリンクが作成されたことを確認。同じiノード番号を持っていない
[testuser@localhost work]$ ls -liR
.:
合計 0
70001823 drwxr-xr-x 2 testuser testuser 23  7月 26 23:41 hard-link-test
70067721 -rw-r--r-- 2 testuser testuser  0  7月 26 23:41 hardlink

./hard-link-test:
合計 0
70067721 -rw-r--r-- 2 testuser testuser 0  7月 26 23:41 test-link

# ハードリンクに文字列を書き込む
[testuser@localhost work]$ echo 'hardlink-test' >> hardlink

# ハードリンクの元となったファイルに書き込んだ文字が存在することを確認
[testuser@localhost work]$ cat hard-link-test/test-link
hardlink-test

# ハードリンクの元となったファイルを削除
[testuser@localhost work]$ rm -rf hard-link-test/

# ハードリンクの中には同じ文字が存在することを確認
[testuser@localhost work]$ cat hardlink
hardlink-test
```

#### iノード

iノード（inode）は、UNIX系ファイルシステム（例えば、Ext3、Ext4など）においてファイルやディレクトリのメタデータを格納するデータ構造です。iノードには、ファイルやディレクトリの属性情報が含まれており、ファイルシステムがこれらを管理するために使用します。





# ファイル操作コマンド

## ファイルの検索

ファイルシステム内でファイルやディレクトリを検索するための2つの主要なコマンドについて説明します。


### find コマンド

find コマンドは、指定したディレクトリ内でファイルやディレクトリを検索するために使用されます。名前、サイズ、作成日、ファイルタイプなど、さまざまな条件で検索を行うことができます。


書式

```bash
find [-H] [-L] [-P] [-D debugopts] [-Olevel] [開始点...] [式]
```

#### オプション


| オプション | 説明 |
|------------|------|
| -P | シンボリックリンクを決してたどらない。これはデフォルトの動作です。findがファイルの情報を調べたり表示したりする際、シンボリックリンク自体のプロパティが使用されます。 |
| -L | シンボリックリンクをたどる。findがファイルの情報を調べたり表示したりする際、リンク自体ではなく、リンクが指すファイルのプロパティが使用されます（リンクが壊れている場合やリンク先のファイルを調べられない場合を除く）。このオプションの使用は -noleaf を意味します。 |
| -H | 検索の開始パスがシンボリックリンクの場合、そのリンクを辿るが、それ以降のシンボリックリンクは辿らない。 |
| -D debugopts | 診断情報を表示します。これは、findが期待通りに動作しない理由を診断するのに役立ちます。debugオプションのリストはカンマで区切ります。debugオプションの互換性はfindutilsのリリース間で保証されません。 |
| -Olevel | クエリの最適化を有効にします。findプログラムはテストを再編成して実行速度を向上させますが、全体の効果は維持されます。副作用のあるプレディケートはお互いの相対位置が変更されません。 |

<br>

実行例


```bash
# テスト用のディレクトリ、ファイル、シンボリックリンクの作成
[testuser@localhost work]$ mkdir test-dir && touch test-dir/find-test-file && ln -s test-dir test-dir-link

# シンボリックリンクが作成されたことを確認
[testuser@localhost work]$ ls -Rl
.:
合計 0
drwxr-xr-x 2 testuser testuser 28  7月 27 03:57 test-dir
lrwxrwxrwx 1 testuser testuser  8  7月 27 03:57 test-dir-link -> test-dir

./test-dir:
合計 0
-rw-r--r-- 1 testuser testuser 0  7月 27 03:57 find-test-file

# シンボリックリンクを辿って検索
[testuser@localhost work]$ find -H test-dir-link -name "find*"
test-dir-link/find-test-file

# シンボリックリンクを辿らない場合は、何も表示されなかった
[testuser@localhost work]$ find -P test-dir-link -name "find*"
[testuser@localhost work]$

```

#### EXPRESSION（式）

コマンドラインの開始点のリストの後に続く部分が「式」です。これはファイルをマッチさせる方法と、マッチしたファイルに対して何をするかを定義するクエリ仕様のようなものです。式は以下の要素で構成されます。

| カテゴリ| 説明|例|
|---------|----|---|
| テスト  | 通常ファイルの属性に基づいて真または偽の値を返します。例えば、-emptyテストは、ファイルが空である場合にのみ真を返します。 | -empty                                |
| アクション | 副作用を持ち（標準出力に何かを表示するなど）、通常、成功したかどうかに基づいて真または偽を返します。例えば、-printアクションは、現在のファイルの名前を標準出力に表示します。 | -print                              |
| グローバルオプション | コマンドラインのどの部分で指定されても、テストやアクションの動作に影響を与えます。グローバルオプションは常に真を返します。例えば、-depthオプションは、ファイルシステムを深さ優先でトラバースさせます。 | -depth                              |
| 位置オプション | それ以降のテストやアクションにのみ影響を与えます。位置オプションも常に真を返します。例えば、-regextypeオプションは、後でコマンドラインに現れる正規表現の方言を指定します。 | -regextype                           |


#### GLOBAL OPTIONS（グローバルオプション）


グローバルオプションは常に真を返します。グローバルオプションは、コマンドライン上でそれより前に現れるテストに対しても有効です。混乱を避けるために、グローバルオプションは開始点のリストの後、最初のテスト、位置オプション、またはアクションの直前に指定するべきです。他の場所でグローバルオプションを指定すると、findはこの指定が混乱を招く可能性があるという警告メッセージを表示します。

| オプション  | 説明 |
|----------------------------------|--------------------------|
| -help, --help | findのコマンドライン使用法の概要を表示し、終了します。 |
| -maxdepth levels| 開始点の下で最大でlevels（非負整数）のディレクトリ層を降りることを指定します。-maxdepth 0を使用すると、テストやアクションは開始点自体にのみ適用されます。|
| -mindepth levels| levels（非負整数）よりも浅いレベルでテストやアクションを適用しません。-mindepth 1を使用すると、開始点を除くすべてのファイルを処理します。  |

<br>

実行例

```bash
# テスト用のファイルを作成
[testuser@localhost work]$ mkdir -p dir1/dir2/dir3 && touch dir1/dir2/test-file1 dir1/dir2/dir3/test-file2

# ディレクトリの構造を確認
[testuser@localhost work]$ tree
.
└── dir1
    └── dir2
        ├── dir3
        │   └── test-file2
        └── test-file1

3 directories, 2 files

# dir1から2階層目までのディレクトリ内で検索したためtest-file1しか表示されない
[testuser@localhost work]$ find dir1 -maxdepth 2 -type f
dir1/dir2/test-file1

# dir1から3階層目から検索したためtest-file2のみ表示される
[testuser@localhost work]$ find dir1 -mindepth 3 -type f
dir1/dir2/dir3/test-file2
```



#### TESTS（テスト）



テストでは、現在調査中のファイルに対して他のファイルを参照する場合があります。そのため、次のテストが現在調査中のファイルをどのように扱うかを理解しておくと便利です。

数値引数|説明
-|-
+n|n より大きい場合
-n|n より小さい場合
n|ちょうど n の場合

| オプション                      | 説明                                                                                                                                                              |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -empty                        | ファイルが空である（サイズがゼロバイトである）場合に真を返します。                                                                                                  |
| -executable                   | ファイルが実行可能である場合に真を返します。システムのPATH環境変数によっては、-executableは実行可能なバイナリがパスにない場合もあります。この場合、-execを使用してファイルを直接テストできます。 |
| -name pattern                 | ファイル名が指定したpatternに一致する場合に真を返します。patternは*（任意の文字列）、?（任意の1文字）、[]（文字クラス）を含むパターンが指定できます。                                      |
| -size n[cwbkMG]               | ファイルサイズがnで指定された単位（バイト、ワード、ブロック、キロバイト、メガバイト、ギガバイト）と一致する場合に真を返します。                                    |
| -ctime n   | ファイルのステータスが最後に変更されたのが n*24 時間以内、n*24 時間以上、またはちょうど n*24 時間前であるかどうかをテストします。                 |
| -mmin n    | ファイルのデータが最後に変更されたのが n 分以内、n 分以上、またはちょうど n 分前であるかどうかをテストします。                                                       |
| -mtime n   | ファイルのデータが最後に変更されたのが n*24 時間以内、n*24 時間以上、またはちょうど n*24 時間前であるかどうかをテストします。                                         |
| -user username                | ファイルの所有者がusernameである場合に真を返します。                                                                                                               |
type|検索するファイルの種類を指定るすることができます。

<br>

type一覧<br>


| 種類 | 説明                                                           |
|------|----------------------------------------------------------------|
| b    | ブロック (バッファリングされた) 特殊ファイル                   |
| c    | キャラクター (バッファリングされない) 特殊ファイル             |
| d    | ディレクトリ                                                   |
| p    | 名前付きパイプ (FIFO)                                          |
| f    | 通常ファイル                                                   |
| l    | シンボリックリンク; -L オプションまたは -follow オプションが有効な場合は常に false ただし、シンボリックリンクが壊れている場合は例外。-L が有効な場合にシンボリックリンクを検索するには -xtype を使用 |
| s    | ソケット                                                       |

<br>

実行例

```bash
# テスト用ファイル作成
[testuser@localhost work]$ mkdir -p dir1/dir2/dir3 \
 && touch dir1/dir2/test-file1 dir1/dir2/dir3/test-file2 dir1/find-file \
 && echo "find-test" > dir1/dir2/test-file1

# ディレクトリの構造を確認
[testuser@localhost work]$ tree
.
└── dir1
    ├── dir2
    │   ├── dir3
    │   │   └── test-file2
    │   └── test-file1
    └── find-file

# ファイルが0バイト(-empty)かつファイル名にfindと入るファイルを検索
[testuser@localhost work]$ find dir1 -empty -name find*
dir1/find-file

# ディレクトリのみ表示
[testuser@localhost work]$ find dir1 -type d
dir1
dir1/dir2
dir1/dir2/dir3

# 10日以内に変更があったファイルを検索
[testuser@localhost work]$ find -ctime -10 -type f
./dir1/dir2/dir3/test-file2
./dir1/dir2/test-file1
./dir1/find-file

# 10日以上前に変更があった、/etc/sshの配下のファイルを検索
[testuser@localhost work]$ sudo find /etc/ssh/ -ctime +10 -type f | sudo xargs ls -l
-rw-r--r--. 1 root root     578094  7月 10 00:15 /etc/ssh/moduli
-rw-r--r--. 1 root root       1921  7月 10 00:15 /etc/ssh/ssh_config
-rw-r--r--. 1 root root        581  7月 10 00:16 /etc/ssh/ssh_config.d/50-redhat.conf
```


#### ACTIONS（アクション）


アクションは副作用を持ち、通常、成功したかどうかに基づいて真または偽を返します。


| オプション                      | 説明                                                                                                                                                          |
|---------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -delete                       | 現在のファイルを削除します。このアクションは、-depthオプションを暗示します。                                                                                  |
| -exec command {} +            | 現在のファイルを引数としてcommandを実行します。{}+は、見つかったすべてのファイルを1回のコマンド実行で処理することを意味します。                             |
| -exec command {} \;           | 現在のファイルを引数としてcommandを実行します。{}\;は、見つかった各ファイルごとに1回のコマンド実行を意味します。                                           |
| -ok command {} \;             | -execと似ていますが、commandを実行する前にユーザーに確認を求めます。確認が取れた場合のみコマンドが実行されます。                                            |
| -print0                       | 現在のファイルの名前をNUL文字で区切って標準出力に表示します。ファイル名にスペースや特殊文字を含むファイルの処理に役立ちます。                                     |
| -printf format                | formatに従って現在のファイルの情報を標準出力に表示します。フォーマット文字列には、%p（ファイルのパス）、%f（ファイル名）、%s（サイズ）、%t（最終変更時間）などがあります。 |
| -printx                       | 現在のファイルの名前を標準出力に表示します。-print0と似ていますが、出力はNUL文字ではなく改行で区切られます。                                                  |
| -quit                         | findをすぐに終了します。検索が終了する前にすべてのファイルを表示することなく、検索を停止します。                                                             |
| -ls                           | -printと同様に現在のファイルの情報を表示しますが、ls -lコマンドの出力形式で表示します。                                                                     |
| -fprint file                  | fileに現在のファイルの名前を出力します。-printのような動作をし、ファイルを削除します。                                                                       |

<br>

実行例

```bash
# テスト用のディレクトリ、ファイルの作成
[testuser@localhost work]$ mkdir -p dir1/dir2/dir3 \
 && touch dir1/dir2/test-file1 dir1/dir2/dir3/test-file2 dir1/find-file \
 && echo "find-test" > dir1/dir2/test-file1

# ディレクトリの構造を確認
[testuser@localhost work]$ tree
.
└── dir1
    ├── dir2
    │   ├── dir3
    │   │   └── test-file2
    │   └── test-file1
    └── find-file

# ファイルが0バイト(-empty)かつファイル名にfindと入るファイルを削除
[testuser@localhost work]$ find dir1 -empty -name find* -delete

#  find-fileが無くなっていることが確認できます。
[testuser@localhost work]$ tree
.
└── dir1
    └── dir2
        ├── dir3
        │   └── test-file2
        └── test-file1

3 directories, 2 files
```

### locateコマンド

locateは、updatedbによって作成された1つ以上のデータベースを読み取り、少なくとも1つのパターンに一致するファイル名を標準出力に1行ずつ書き出します。

<br>

書式

```bash
locate [オプション]... パターン...
```

<br>

オプション

| オプション | 説明 |
| - | - |
| -A, --all | すべてのパターンに一致するエントリのみを表示します。 |
| -b, --basename | 指定されたパターンに対してベース名のみを一致させます。これは--wholenameの反対です。 |
| -i, --ignore-case | パターンの一致時に大文字と小文字を区別しません。 |
| -w, --wholename | 指定されたパターンに対して完全なパス名のみを一致させます。これはデフォルトの動作です。反対は--basenameで指定できます。 |



### updatedbコマンド

updatedbは、locateによって使用されるデータベースを作成または更新します。データベースがすでに存在する場合、そのデータは再度ディレクトリを読み取らないように再利用されます。updatedbは通常、cronによって毎日実行され、デフォルトのデータベースを更新します。


書式

```bash
updatedb [オプション]...
```

<br>

オプション

| オプション | 説明 |
| - | - |
| -U, --database-root PATH | PATHにルートされたファイルシステムサブツリーのスキャン結果のみを生成されたデータベースに保存します。デフォルトではファイルシステム全体がスキャンされます。locate(1)は、PATHの形式に関係なく、シンボリックリンクを含まない絶対パス名としてエントリを出力します。 |
| -o, --output FILE | デフォルトのデータベースを使用する代わりに、データベースをFILEに書き出します。 |
| -l, --require-visibility FLAG | 生成されたデータベースで「ファイルの可視性を報告前に要求する」フラグをFLAGに設定します。FLAGが0またはnoの場合、またはデータベースファイルが「他のユーザー」によって読み取れる場合、またはslocateに所有されていない場合、locate(1)はデータベースエントリを出力します。FLAGが1またはyes（デフォルト）の場合、locate(1)は各エントリの親ディレクトリの権限をチェックしてから報告します。ファイルの存在を他のユーザーから完全に隠すには、データベースグループをslocateに設定し、データベースの権限がlocate(1)以外の方法でユーザーがデータベースを読み取ることを禁止します。この可視性フラグは、データベースがslocateに所有されており、「他のユーザー」によって読み取られない場合のみチェックされます。 |
| -v, --verbose | 見つかったファイルのパス名を標準出力に出力します。 |


<br>

実行例

```bash
# テスト用のディレクトリ、ファイルを作成
[testuser@localhost work]$ mkdir -p dir1/dir2/dir3 \
 && touch dir1/dir2/locate-test-file1 dir1/dir2/dir3/locate-test-file2

# locateコマンドでlocate-test-file1を検索するが何も見つからない
[testuser@localhost work]$ locate locate-test-file1
[testuser@localhost work]$

# -Uでdir1をスキャンして/var/lib/mlocate/mlocate.dbを更新
[testuser@localhost work]$ sudo updatedb -U dir1/

# locate-test-file1が検索できた
[testuser@localhost work]$ locate locate-test-file1
/home/testuser/work/dir1/dir2/locate-test-file1

# 新しくテスト用のファイルを作成
[testuser@localhost work]$ touch dir1/dir2/locate-test-file3

# locateで検索できない
[testuser@localhost work]$ locate locate-test-file3
[testuser@localhost work]$

# dir1 の内容をスキャンしてcustom_dbを作成する
[testuser@localhost work]$ sudo updatedb -U dir1 -o custom_db

# 通常の/var/lib/mlocate/mlocate.dbからでは検索できない
[testuser@localhost work]$ locate locate-test-file3
[testuser@localhost work]$

# 検索対象にcustom_dbを指定すると検索できる
[testuser@localhost work]$ locate -d custom_db locate-test-file3
/home/testuser/work/dir1/dir2/locate-test-file3
```


## テキスト操作

テキスト操作のためのコマンドとして、grep、sed、および awk があります。これらのコマンドは、ファイル内のテキストを検索、編集、処理するために使用されます。

### grep




grep は、各 FILE で PATTERNS を検索します。PATTERNS は、改行文字で区切られた 1 つまたは複数のパターンであり、grep はパターンに一致する各行を出力します。通常、PATTERNS はシェルコマンドで grep を使用する際に引用符で囲む必要があります。

FILE が「-」の場合は標準入力を意味します。FILE が指定されない場合、再帰的検索は作業ディレクトリを調べ、非再帰的検索は標準入力を読み取ります。

<br>

書式

```bash
grep [オプション...] パターン [ファイル...]
grep [オプション...] -e パターン ... [ファイル...]
grep [オプション...] -f パターンファイル ... [ファイル...]
```

<br>

オプション

| オプション | 説明 |
| - | - |
| -E, --extended-regexp | PATTERNS を拡張正規表現 (ERE) として解釈します。 |
| -F, --fixed-strings | PATTERNS を固定文字列として解釈し、正規表現とはしません。 |
| -G, --basic-regexp | PATTERNS を基本正規表現 (BRE) として解釈します。これがデフォルトです。 |

<br>

マッチング制御

| オプション        | 説明                                                                                  |
| -                | -                                                                                    |
| -e PATTERNS, --regexp=PATTERNS | PATTERNS をパターンとして使用します。このオプションを複数回使用するか、-f (--file) オプションと組み合わせると、指定されたすべてのパターンを検索します。このオプションは、パターンが「-」で始まる場合の保護にも使用できます。 
| -i, --ignore-case  | パターンおよび入力データの大文字と小文字を無視し、大文字と小文字が異なるだけの文字が一致するようにします。   |
| -v, --invert-match | 一致の意味を反転させ、一致しない行を選択します。   

```bash
# テスト用ファイルの作成
[testuser@localhost work]$ cat <<EOF > grep_test1
apple
APPLE
banana
cherry
grape
EOF

# 複数のパターンをマッチする
[testuser@localhost work]$ grep -e apple -e cherry grep_test1
apple
cherry

# 通常、大文字と小文字は区別され、小文字のappleしか表示されません
[testuser@localhost work]$ grep apple grep_test1
apple

# -iオプションは大文字と小文字を無視するため、大文字のAPPLEも表示されました
[testuser@localhost work]$ grep -i apple grep_test1
apple
APPLE

# -vはパターンがマッチした行が表示されません。
[testuser@localhost work]$ grep -v apple grep_test1
APPLE
banana
cherry
grape
```
<br>

**正規表現**

正規表現は、文字列の検索、マッチング、および操作のためのパターンを指定する方法です。

<br>

 基本的な構文

| 構文        | 説明                                                  |
|-------------|-------------------------------------------------------|
| .         | 任意の1文字にマッチ                                    |
| ^         | 行の先頭にマッチ                                        |
| $         | 行の末尾にマッチ                                        |
| []        | 文字クラス。中括弧内の任意の1文字にマッチ                |
| [^]       | 否定の文字クラス。中括弧内の文字以外の任意の1文字にマッチ |


```bash
# 任意の1文字にマッチ   
[testuser@localhost work]$ grep -E 'n.n' grep_test1
banana

# 行の先頭にマッチ 
[testuser@localhost work]$ grep -E '^a' grep_test1
apple

# 行の末尾にマッチ 
[testuser@localhost work]$ grep -E 'y$' grep_test1
cherry
```

<br>

 文字クラス

| クラス      | 説明                                                  |
|-------------|-------------------------------------------------------|
| [abc]     | a, b, c のいずれかにマッチ                       |
| [a-z]     | 小文字のアルファベット a から z のいずれかにマッチ  |
| [^abc]    | a, b, c 以外の任意の文字にマッチ                  |


```bash
# a, b, c のいずれかにマッチ   
[testuser@localhost work]$ grep -E '[abc]' grep_test1
apple
banana
cherry
grape
```

 量指定子

| オペレーター | 説明                                                  |
|--------------|-------------------------------------------------------|
| ?          | 前の項目は任意で、最大1回マッチします。                  |
| *          | 前の項目は0回以上マッチします。                          |
| +          | 前の項目は1回以上マッチします。                          |
| {n}        | 前の項目は正確にn回マッチします。                       |
| {n,}       | 前の項目はn回以上マッチします。                         |
| {,m}       | 前の項目は最大m回マッチします。 (GNU拡張)               |
| {n,m}      | 前の項目は少なくともn回、最大m回マッチします。           |



```bash
# テスト用ファイルの作成
[testuser@localhost work]$ cat <<EOS > grep_test1
SATO: 03-1234-5678
TANAKA: 090-8765-4321
Office: 045-987-6543
TEST: 123-456
EOS

# 電話番号の形式が表示されます
[testuser@localhost work]$ grep -E '[0-9]{2,4}-[0-9]{2,4}-[0-9]{4}' grep_test1
SATO: 03-1234-5678
TANAKA: 090-8765-4321
Office: 045-987-6543

# 3桁で始まる電話番号を表示
# [^0-9]0~9の数字とマッチしない
# [0-9]{3}の数字とのマッチを3回繰り返す
[testuser@localhost work]$ grep -E '[^0-9][0-9]{3}-' grep_test1
TANAKA: 090-8765-4321
Office: 045-987-6543
TEST: 123-456
```

### sed

sed はストリームエディタです。ストリームエディタは、入力ストリーム（ファイルやパイプラインからの入力）に対して基本的なテキスト変換を行うために使用されます。一部の面では、スクリプト化された編集を許可するエディタ（例えば ed）に似ていますが、sed は入力を一度だけ処理するため、より効率的です。しかし、sed が他のタイプのエディタと特に異なるのは、パイプライン内のテキストをフィルタリングできる能力です。

```bash
sed [オプション]... {他にスクリプトがない場合はスクリプトのみ} [入力ファイル]...
```

<br>
オプション

| オプション | 説明                                         |
|------------|----------------------------------------------|
| -e       | 複数の編集コマンドを指定する場合に使用          |
| -f       | 編集コマンドを記述したファイルを指定            |
| -i       | ファイルを直接編集 (インプレース編集)           |
| -n       | 出力を抑制し、明示的に指定した行だけを出力       |

<br>
コマンド

| コマンド  | 説明                                              |
|-----------|---------------------------------------------------|
| s/pattern/replacement/ | パターンを置換                        |
| d       | 行を削除                                          |
| p       | 行を表示 (`-n` オプションと併用)                    |
| a\text  | 行の後にテキストを追加                             |
| i\text  | 行の前にテキストを挿入                             |
| c\text  | 行をテキストで置換                                 |
| q       | 処理を終了                                        |



```bash
[testuser@localhost work]$ cat <<EOS > sed_test1
hogehoge
foofoo
barbar
hogehoge
EOS

# 各行の最初にマッチしたhogeをtestに変換
[testuser@localhost work]$ sed 's/hoge/test/' sed_test1
testhoge
foofoo
barbar
testhoge

# ファイル自体はこのままだと変換されません
[testuser@localhost work]$ cat sed_test1
hogehoge
foofoo
barbar
hogehoge

# -iオプションを使うとファイルをそのまま編集できます。
[testuser@localhost work]$ sed -i 's/hoge/test/' sed_test1
[testuser@localhost work]$ cat sed_test1
testhoge
foofoo
barbar
testhoge

# gフラグを使用すると各行の全てのマッチする文字が変換される
[testuser@localhost work]$ sed -e 's/hoge/test/g' sed_test1
testtest
foofoo
barbar
testtest

# -e オプションを使用すると同時に別のマッチした文字列を変換できます。
[testuser@localhost work]$ sed -e 'sg/hoge/test/g' -e 's/foo/abc/' sed_test1
hogehoge
abcfoo
barbar
hogehoge

# 先頭にhogeがある行を削除。ここにあるように正規表現も使用可能です。
[testuser@localhost work]$ sed -e '/^hoge/d' sed_test1
foofoo
barbar

# 2aは2行目の後に文字列を追記する
[testuser@localhost work]$ sed '2a\This is appended text.' sed_test1
hogehoge
foofoo
This is appended text.
barbar
hogehoge
```

