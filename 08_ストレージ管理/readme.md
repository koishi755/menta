# ディスクとパーティション
## ディスク
ディスクは、データを保存するための物理的な記憶媒体です。一般的にはHDD（ハードディスクドライブ）やSSD（ソリッドステートドライブ）を指します。これらのディスクは、1つの大きなデータストレージのように見えますが、その中に複数のパーティションを作成することができます。ディスクのパーティション形式は以下の２つがあります。


パーティション形式|特徴
-|-
MBR（Master Boot Record）|最大4つのプライマリパーティションを作成可能。2TBまでのディスクサイズに対応。
GPT（GUID Partition Table）|制限なくパーティションを作成でき、2TBを超えるディスクサイズにも対応。

## パーティション
パーティションは、ディスクを分割して管理する単位です。1つの物理ディスク上に複数のパーティションを作成することで、ディスクを複数の領域に分けて異なる用途に利用できます。

![](https://storage.googleapis.com/zenn-user-upload/c62e61af112a-20240816.png)

## HDDの仕組み

名前|説明
-|-
セクタ|ディスク上でデータを格納する最小単位です。通常、1セクタのサイズは512バイトが一般的。
クラスター|複数のセクタをまとめたデータ管理の単位です。ファイルシステムが効率よくディスクを使用できるように、複数のセクタを1つのクラスタとして扱います。
トラック|ディスク上にある同心円状の記録領域です。

![](https://storage.googleapis.com/zenn-user-upload/ebe0cdcba36d-20240816.png)

## ディスクとパーティションの確認
ディスクは/dev配下に配置されます。ディスクデバイスは、/devディレクトリ内で特定の名前で表現されます。

/dev/sd〇という形式でディスクが配置され、1番目のディスクからaから順番に名前が付けられます。
- /dev/sda 最初のディスク。
- /dev/sdb 2番目のディスク。

<br>


パーティションは/dev/sd〇nという形式で、〇番目のディスクのn番目のパーティションという形式で表示されます。

- /dev/sda1 最初のディスク (/dev/sda) の1番目のパーティション
- /dev/sdb2 2番目のディスク (/dev/sdb) の2番目のパーティション


### lsblk コマンド

lsblkは、ブロックデバイスの情報を表示するコマンドです。主にディスクやパーティションの構造を確認するために使用されます。デバイスの名前、サイズ、タイプ（ディスク、パーティション、LVMなど）、マウントポイントなどが確認できます。

<br>

書式

```
lsblk [オプション]
```

<br>

オプション

オプション|説明
-|-
-a, --all|空のデバイスも含めてすべてのデバイスを表示する
-f, --fs|ファイルシステムの情報（UUID、マウントポイントなど）も表示する
-l, --list|ツリー形式ではなく、リスト形式で表示する
-p, --paths|フルパス形式（例: /dev/sda1）でデバイスを表示する
-r, --raw|デバイスツリーの表示を抑制し、生のデバイスリストを表示する


```bash
[testuser@localhost ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk
├─sda1        8:1    0  600M  0 part /boot/efi
├─sda2        8:2    0    1G  0 part /boot
└─sda3        8:3    0 18.4G  0 part
  ├─cs-root  253:0   0 16.4G  0 lvm  /
  └─cs-swap  253:1   0    2G  0 lvm  [SWAP]
sr0          11:0    1 1024M  0 rom
```

仮想マシンにディスクを追加して表示が変わるか確認してみます。

対象の仮想マシンを選択し、「設定」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/b74b258e368d-20240815.png)

<br>


「ハードディスクの追加」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/8578cd2469ce-20240815.png)

<br>

「作成」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/5edf6fa07c61-20240815.png)

<br>

「次へ」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/f0358de5c57f-20240815.png)

<br>

「次へ」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/c1a24a290549-20240815.png)

<br>

１．作成する仮想ディスクのパスと名前を設定します。
２．ディスクのサイズを指定します。
３．「完了」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/911b346f7321-20240815.png)

<br>

作成した仮想ディスクを選択し、「Choose」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/88a46494c0ba-20240815.png)

<br>

「OK」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/ea3defd4f81e-20240815.png)

<br>

再度lsblkを実行するとsdbが新しく表示されました。

```bash
[testuser@localhost ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk
├─sda1        8:1    0  600M  0 part /boot/efi
├─sda2        8:2    0    1G  0 part /boot
└─sda3        8:3    0 18.4G  0 part
  ├─cs-root  253:0   0 16.4G  0 lvm  /
  └─cs-swap  253:1   0    2G  0 lvm  [SWAP]
sdb           8:16   0    5G  0 disk
sr0          11:0    1 1024M  0 rom
```


## パーティションの作成・削除・変更


### fdisk コマンド

fdiskは、ディスクパーティションの作成、削除、変更を行うためのコマンドです。主にMBR（マスターブートレコード）パーティションテーブルを操作するために使用されます。ディスクのパーティションテーブルを管理し、ディスクのパーティション構成を変更するのに便利です。

<br>

書式

```
fdisk [オプション] デバイス
```

<br>

オプション

オプション|説明
-|-
-l, --list|ディスクとそのパーティションのリストを表示する
-t, --type|パーティションタイプを変更する

<br>

**パーティションの作成**

```bash
# /dev/sdbの状態を確認。パーティションは作成されていない
[testuser@localhost ~]$ sudo fdisk -l /dev/sdb
ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト

# 対話モードでパーティションを作成
[testuser@localhost ~]$ sudo fdisk /dev/sdb

fdisk (util-linux 2.37.4) へようこそ。
ここで設定した内容は、書き込みコマンドを実行するまでメモリのみに保持されます。
書き込みコマンドを使用する際は、注意して実行してください。

デバイスには認識可能なパーティション情報が含まれていません。
新しい DOS ディスクラベルを作成しました。識別子は 0xd1aa22b6 です。

# 対話モードのコマンド「p」を実行し、パーティションテーブルを表示させます。
コマンド (m でヘルプ): p
ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0xd1aa22b6

# 対話モードのコマンド「n」を実行し、新規パーティションを作成します。
# 「n」実行後、何も入力せずEnter。規定値であるpを選択したことになります。
コマンド (m でヘルプ): n
パーティションタイプ
   p   基本パーティション (0 プライマリ, 0 拡張, 4 空き)
   e   拡張領域 (論理パーティションが入ります)
選択 (既定値 p):

# パーティション番号-->既定値
# 最初のセクタ-->既定値
# 最終セクタ--> +2G として2GBのパーティションを作成します。
既定の回答 p であるものとみなします。
パーティション番号 (1-4, 既定値 1):
最初のセクタ (2048-10485759, 既定値 2048):
最終セクタ, +/-セクタ番号 または +/-サイズ{K,M,G,T,P} (2048-10485759, 既定値 10485759): +2G

新しいパーティション 1 をタイプ Linux、サイズ 2 GiB で作成しました。

# 同じ要領で新しいパーティションを作成します。
# 今度は全て規定値を選択します。
コマンド (m でヘルプ): n
パーティションタイプ
   p   基本パーティション (1 プライマリ, 0 拡張, 3 空き)
   e   拡張領域 (論理パーティションが入ります)
選択 (既定値 p):

既定の回答 p であるものとみなします。
パーティション番号 (2-4, 既定値 2):
最初のセクタ (4196352-10485759, 既定値 4196352):
最終セクタ, +/-セクタ番号 または +/-サイズ{K,M,G,T,P} (4196352-10485759, 既定値 10485759):

新しいパーティション 2 をタイプ Linux、サイズ 3 GiB で作成しました。

# パーティションが２つ作成されたことが確認できます
コマンド (m でヘルプ): p

ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0xd1aa22b6

デバイス   起動 開始位置 終了位置  セクタ サイズ Id タイプ
/dev/sdb1           2048  4196351 4194304     2G 83 Linux
/dev/sdb2        4196352 10485759 6289408     3G 83 Linux

# 対話モードのコマンド「w」を実行し、パーティションテーブルを保存して終了します。
コマンド (m でヘルプ): w

パーティション情報が変更されました。

ioctl() を呼び出してパーティション情報を再読み込みします。
ディスクを同期しています。

# パーティションsdb1、sdb2が作成されたことを確認。
[testuser@localhost ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk
├─sda1        8:1    0  600M  0 part /boot/efi
├─sda2        8:2    0    1G  0 part /boot
└─sda3        8:3    0 18.4G  0 part
  ├─cs-root  253:0   0 16.4G  0 lvm  /
  └─cs-swap  253:1   0    2G  0 lvm  [SWAP]
sdb           8:16   0    5G  0 disk
├─sdb1        8:17   0    2G  0 part
└─sdb2        8:18   0    3G  0 part
sr0          11:0    1 1024M  0 rom

[testuser@localhost ~]$ sudo fdisk -l /dev/sdb
ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0xd1aa22b6

デバイス   起動 開始位置 終了位置  セクタ サイズ Id タイプ
/dev/sdb1           2048  4196351 4194304     2G 83 Linux
/dev/sdb2        4196352 10485759 6289408     3G 83 Linux

# パーティションテーブルタイプでMBRを指定して、fdiskを起動
[testuser@localhost ~]$ sudo fdisk -t dos /dev/sdb

# パーティションテーブルタイプでGPTを指定して、fdiskを起動
[testuser@localhost ~]$ sudo fdisk -t gpt /dev/sdb
```

<br>

**fdliskの見方**

1行目

```
ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
```


ディスク /dev/sdb: 5 GiB
fdiskで指定したディスクが/dev/sdbであり、その容量が5 GiBであることを表す

5368709120 バイト, 10485760 セクタ
ディスクの総容量（5 GiB）をバイト単位で表示。またセクタ数も表示。

<br>

２～６行目

```bash
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0xd1aa22b6
```

| 項目                        | 説明                                                                                  |
|-----------------------------|---------------------------------------------------------------------------------------|
| ディスク型式                | VBOX HARDDISKは、VirtualBoxの仮想ディスクを示しています。これは物理ディスクではなく仮想マシン内の仮想ディスクです。 |
| セクタサイズ (論理 / 物理)  | 1セクタのサイズは512バイトです。論理セクタと物理セクタのサイズが同じです。                                      |
| I/O サイズ (最小 / 推奨)     | 1回の最小および推奨読み書き単位も512バイトです。                                                         |
| ディスクラベルのタイプ       | パーティションテーブルの形式がdos（MBR, Master Boot Record）であることを示しています。                          |
| ディスク識別子              | このディスクのユニークな識別子が0xd1aa22b6です。                                                         |

<br>

7行目以降

```bash
デバイス   起動 開始位置 終了位置  セクタ サイズ Id タイプ
/dev/sdb1           2048  4196351 4194304     2G 83 Linux
/dev/sdb2        4196352 10485759 6289408     3G 83 Linux
```

| 項目       | 説明                                                                                             |
|------------|--------------------------------------------------------------------------------------------------|
| デバイス   | パーティションやディスクの名前を表します。例：`/dev/sdb1`は2番目のディスク（`/dev/sdb`）の1番目のパーティションです。 |
| 起動       | ブート可能なパーティションの場合、この列に `*` が表示されます。通常、ブートローダーが存在するパーティションです。   |
| 開始位置   | パーティションがディスク上で開始するセクタの位置を表します。ディスクのセクタ番号で表記されます。                   |
| 終了位置   | パーティションがディスク上で終了するセクタの位置を示します。                                         |
| セクタ     | パーティションが占有するセクタ数を表します。セクタはディスクの物理的な最小の記録単位です（通常1セクタ = 512バイト）。|
| サイズ     | パーティションのサイズをGBやMBで表示します。                                                       |
| Id         | パーティションのタイプを示す識別子です。`83` はLinuxファイルシステムを示します。                       |
| タイプ     | パーティションのファイルシステムタイプを表します。例：`Linux`はLinuxファイルシステムを意味します。   |



**パーティションの削除**

```bash
[testuser@localhost ~]$ sudo fdisk /dev/sdb

fdisk (util-linux 2.37.4) へようこそ。
ここで設定した内容は、書き込みコマンドを実行するまでメモリのみに保持されます。
書き込みコマンドを使用する際は、注意して実行してください。

# 対話モードのコマンド「d」を実行
# 今回はパーティション番号2を指定
コマンド (m でヘルプ): d
パーティション番号 (1,2, 既定値 2): 2

パーティション 2 を削除しました。

# 対話モードのコマンド「w」を実行し、パーティションテーブルを保存して終了します。
コマンド (m でヘルプ): w
パーティション情報が変更されました。
ioctl() を呼び出してパーティション情報を再読み込みします。
ディスクを同期しています。

# パーティション2が削除されたことを確認
[testuser@localhost ~]$ sudo fdisk -l /dev/sdb
ディスク /dev/sdb: 5 GiB, 5368709120 バイト, 10485760 セクタ
ディスク型式: VBOX HARDDISK
単位: セクタ (1 * 512 = 512 バイト)
セクタサイズ (論理 / 物理): 512 バイト / 512 バイト
I/O サイズ (最小 / 推奨): 512 バイト / 512 バイト
ディスクラベルのタイプ: dos
ディスク識別子: 0xd1aa22b6

デバイス   起動 開始位置 終了位置  セクタ サイズ Id タイプ
/dev/sdb1           2048  4196351 4194304     2G 83 Linux
```


### parted コマンド

`parted`は、ディスクのパーティション操作を行うためのツールです。GPTやMS-DOSなどのパーティションテーブルの作成、パーティションの作成や削除、変更が可能です。

<br>

書式


```bash
parted [オプション] [デバイス]
```


<br>

オプション

オプション|説明
-|-
-a|パーティションのアライメント方法を指定します
-l|すべてのディスクのパーティション情報をリスト表示します
-s|対話的なプロンプトを抑制してスクリプト的に動作します
-m|機械可読形式で出力します（スクリプトやプログラムで利用）


既存パーティションテーブルの変更
**こちらはデータが削除されるので注意してください**

```bash
# 対話モードに入る（事前にテスト用のパーティションは削除しています）
[testuser@localhost ~]$ sudo parted /dev/sdb
GNU Parted 3.5
/dev/sdb を使用
GNU Parted へようこそ！ コマンド一覧を見るには 'help' と入力してください。

# 現在の状態を確認（パーティションテーブル: msdos　MBRとなっている）
(parted) print
モデル: ATA VBOX HARDDISK (scsi)
ディスク /dev/sdb: 5369MB
セクタサイズ (論理/物理): 512B/512B
パーティションテーブル: msdos
ディスクフラグ:

番号  開始  終了  サイズ  タイプ  ファイルシステム  フラグ

# パーティションテーブルをGPTに変更
(parted) mklabel gpt
警告: いま存在している /dev/sdb のディスクラベルは破壊され、このディスクの全データが失われます。続行しますか？
はい(Y)/Yes/いいえ(N)/No? y
エラー: パーティション 1 (/dev/sdb 上) に書きこまれましたが、カーネルに変更を伝えることができませんでした。おそらく、使用中だったのが原因 だと思われます。そのため、古いパーティション情報がそのまま使われます。さらなる変更をする前に再起動してください。
無視(I)/Ignore/取消(C)/Cancel? I


# パーティションテーブルがgptに変更されたことを確認
(parted) print
モデル: ATA VBOX HARDDISK (scsi)
ディスク /dev/sdb: 5369MB
セクタサイズ (論理/物理): 512B/512B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始  終了  サイズ  ファイルシステム  名前  フラグ

(parted) quit
```

<br>

-sを使った形式でパーティションを作成

```bash
# 一度ディスクを初期化
[testuser@localhost ~]$ sudo dd if=/dev/zero of=/dev/sdb bs=32M status=progress

# パーティションテーブルgptで作成
[testuser@localhost ~]$ sudo parted -s /dev/sdb mklabel gpt

# 新しく2GBのパーティションを作成
[testuser@localhost ~]$ sudo parted -s /dev/sdb mkpart primary 1MiB 2049MiB

# 新しいパーティションが作成されたことを確認
[testuser@localhost ~]$ sudo parted -s /dev/sdb print
モデル: ATA VBOX HARDDISK (scsi)
ディスク /dev/sdb: 5369MB
セクタサイズ (論理/物理): 512B/512B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始    終了    サイズ  ファイルシステム  名前     フラグ
 1    1049kB  2149MB  2147MB  xfs               primary
```

<br>

partedの対話モード

```bash
# 一度ディスクを初期化
[testuser@localhost ~]$ sudo dd if=/dev/zero of=/dev/sdb bs=32M status=progress

# 対話モードに入る
[testuser@localhost ~]$ sudo parted /dev/sdb
GNU Parted 3.5
/dev/sdb を使用
GNU Parted へようこそ！ コマンド一覧を見るには 'help' と入力してください。

# MBRのパーティションテーブルを作成
(parted) mklabel msdos

# 新しいパーティションを作成
(parted) mkpart
パーティションの種類?  primary/プライマリ/extended/拡張? primary
ファイルシステムの種類?  [ext2]?
開始? 1
終了? 2049

# 作成したパーティションを確認
(parted) print
モデル: ATA VBOX HARDDISK (scsi)
ディスク /dev/sdb: 5369MB
セクタサイズ (論理/物理): 512B/512B
パーティションテーブル: msdos
ディスクフラグ:

番号  開始    終了    サイズ  タイプ   ファイルシステム  フラグ
 1    1049kB  2049MB  2048MB  primary  ext2
```

# ファイルシステムの作成とマウント
## ファイルシステムの作成

ファイルシステムとは、データを効率的に保存、管理、アクセスするための仕組みです。


ファイルシステムの役割

| 役割                 | 説明                                                           |
|----------------------|----------------------------------------------------------------|
| データの整理         | ファイルシステムはデータをファイルとして管理し、ファイルに名前を付けてディレクトリに整理します。 |
| メタデータの管理     | ファイルの作成日時、変更日時、アクセス権限、所有者などのメタデータを管理します。             |
| 空き領域の管理       | ディスクの空き領域を追跡し、新しいファイルがどこに保存されるかを決定します。                  |
| データの保護と復旧   | データの整合性を保ち、エラーが発生した場合に回復するための機能を提供します（例：ジャーナリング機能）。 |

<br>

主なファイルシステム

| ファイルシステム | 説明                                                                                     | 特徴                                                               |
|-----------------|------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| ext4        | Linuxで広く使用されているファイルシステム。                                              | ジャーナリング、データ整合性、最大1EBのサイズサポート、動的サイズ変更可能 |
| XFS         | 高性能でスケーラブルなファイルシステム。                                                  | 大容量サポート、ジャーナリング、動的サイズ変更、スナップショット機能     |
| NTFS        | Windowsで使用されるファイルシステム。                                                     | 高度なセキュリティ、ジャーナリング、大容量サポート、アクセス制御リスト    |
| FAT32       | 古いファイルシステムで、幅広い互換性を持つ。                                               | 最大2TBのパーティションサイズ、ファイルサイズは最大4GB                |
| exFAT       | FAT32の制限を克服した、特にフラッシュドライブやSDカード向けのファイルシステム。               | 大容量ファイルサポート、軽量、パフォーマンスが良好                      |
| Btrfs       | Linux用の次世代ファイルシステムで、高度な機能を提供。                                      | スナップショット、チェックサム、動的サイズ変更、内蔵RAIDサポート         |
| ZFS         | 高度なデータ保護機能を持つファイルシステム。                                                | データ整合性、スナップショット、RAID機能、動的ストレージ管理            |


<br>

ファイルシステムの要素

今回はXFSを例に説明します。

![](https://storage.googleapis.com/zenn-user-upload/85ea52f54396-20240816.png)




| 要素                | 説明                                                                                   |
|---------------------|----------------------------------------------------------------------------------------|
| アロケーショングループ | ファイルシステムのデータを複数の部分に分割して管理します。これにより、各グループが独立してデータの割り当てや管理を行えるようになります。1つの大きなファイルシステムを複数の小さなセクション（AG）に分けることで、並列処理が可能になり、大容量ディスクでのパフォーマンスが向上します。各アロケーショングループには、未使用ブロックの情報やiノード、データブロックの管理に関するB+treeが含まれています。この構造により、ファイルシステムのメタデータとデータが分離されて管理され、効率的なアクセスが可能になります。|
| スーパーブロック     | ファイルシステム全体の基本情報を保持する。<br> **スーパーブロックの役割**<br>**1.  ファイルシステムの基本情報を保持** <br> スーパーブロックには、ファイルシステムに関する以下のような基本情報が格納されています：<br>　- **ファイルシステムのサイズ**: ファイルシステム全体の容量。<br>　- **ブロックサイズ**: データを保存するための最小単位であるブロックのサイズ。<br>　- **アロケーショングループ（AG）数**: XFSファイルシステムにおけるAGの数。<br>　- **iノード数**: ファイルやディレクトリのメタデータを保持するiノードの総数。<br><br>**2. ファイルシステムの整合性の維持** <br>　ファイルシステムが正しくマウントされ、正しい状態で使用されているかどうかを判断するための情報を保持しています。<br><br>**3. ファイルシステムのメタデータ管理**<br>ファイルシステムのメタデータを管理するための情報も含まれています。これには、未使用ブロックやiノードの管理に関する情報が含まれ、効率的なディスクスペースの管理をサポートします。<br><br>**4. バックアップスーパーブロック**<br>XFSでは2番目以降のAGのスーパーブロックはバックアップです。XFSファイルシステムでは、スーパーブロックは各アロケーショングループの先頭に配置されます。これにより、アロケーショングループごとにファイルシステムの状態を独立して管理することができます。 |
| ブロック             | ファイルシステムでデータを物理的に保存する最小単位です。XFSでは、ファイルの内容やメタデータがこのブロックに保存されます。ブロックサイズは、ファイルシステム作成時に指定します。一般的には、4KB、8KBなど。 |
| iノード             | ファイルやディレクトリのメタデータを保持するデータ構造です。iノードにはファイルの属性（サイズ、作成日時、アクセス権限、所有者など）や、データブロックへのポインタが含まれています。 |
| エクステント         | ファイルシステムでファイルデータを連続的に格納するための方法です。エクステントは、複数の連続するブロックをまとめて扱うことで、ファイルの断片化を減少させます。 |


### mkfs コマンド

`mkfs`は、新しいファイルシステムを作成するためのコマンドです。ディスクやパーティションに対してファイルシステムを初期化し、利用可能な状態にします。

<br>

書式

```
mkfs [オプション] デバイス
```


<br>

オプション

オプション|説明
-|-
-t ファイルシステムタイプ|作成するファイルシステムのタイプを指定します（例: `ext4`, `xfs`）。
-c|ファイルシステム作成前にデバイスの不良ブロックをチェックします。
-n|ファイルシステムを実際に作成せずに、どうなるかのシミュレーションを行います。
-L ラベル|ファイルシステムにラベルを付けます。

<br>

ファイルシステムの作成実行例


```bash
[testuser@localhost ~]$ sudo mkfs -t xfs /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

<br>

実行結果について

| 項目          | 説明                                                                                     |
|---------------|------------------------------------------------------------------------------------------|
| **meta-data** |                                                                                          |
| isize=512     | iノードサイズを表しています。iノードは、ファイルやディレクトリのメタデータ（例: 所有者、パーミッション）を管理する構造体です。 |
| agcount=4, agsize=131072 blks | 4つのアロケーショングループがあり、各グループに131,072ブロックが含まれています。XFSは、効率的なデータ管理のためにアロケーショングループを使用します。 |
| crc=1         | データ整合性を確保するためのCRC（Cyclic Redundancy Check）が有効です。                   |
| reflink=1     | コピーオンライト（CoW）によるデータの効率的な保存を可能にします。ファイル変更時に元のデータをコピーすることで、ストレージ容量を節約します。 |
| **data**      |                                                                                          |
| bsize=4096    | ブロックサイズが4KBであることを示しています。データはこの単位でディスクに書き込まれます。 |
| blocks=524288 | 全体で524,288ブロック（各ブロックは4KB）が使用されます。                                  |
| imaxpct=25    | iノードの最大数はデータブロックの25%に制限されています。ファイルシステム全体の25%をiノードに使えることを意味します。 |
| **naming**    |                                                                                          |
| version 2     | XFSのファイル名やディレクトリの管理方法がバージョン2で実装されています。                   |
| ascii-ci=0    | ファイル名に大文字と小文字の区別をしない設定（ASCIIの大文字小文字変換）が無効になっています。 |
| ftype=1       | ディレクトリエントリにファイルタイプが格納されていることを意味します。                     |
| **log**       |                                                                                          |
| internal log  | ログ領域が内部にあります（専用の外部ログデバイスは使用していません）。                    |
| bsize=4096    | ログのブロックサイズが4KBであることを示しています。                                        |
| blocks=16384  | 16,384ブロックがログ用に確保されています。                                                 |
| version=2     | XFSのログ構造はバージョン2を使用しています。                                               |
| **realtime**  |                                                                                          |
| none          | リアルタイム領域が無効化されています。XFSにはリアルタイム領域（特殊なデータ領域）を設定できますが、今回は使用していません。 |


## ファイルシステムのマウント

ファイルシステムの「マウント」は、オペレーティングシステムがディスクやパーティションの内容をアクセスできるようにするプロセスです。具体的には、ファイルシステムを特定のディレクトリ（マウントポイント）に関連付け、ユーザーやアプリケーションがそのファイルシステムの内容にアクセスできるようにします。

### mount コマンド

ファイルシステムを特定のディレクトリ（マウントポイント）に関連付けるために使用します。これにより、ディスクやパーティションの内容にアクセスできるようになります。

<br>

書式

```
mount [オプション] <デバイス> <マウントポイント>
```


<br>

オプション

オプション|説明
-|-
-t <タイプ>|指定したファイルシステムタイプでマウントします。例: ext4, xfs
-o <オプション>|マウントオプションを指定します。複数のオプションはカンマで区切ります。例: ro（読み取り専用）、rw（読み書き）
-a|/etc/fstab に定義された全てのファイルシステムをマウントします
-r|読み取り専用でマウントします
-v|詳細な出力を表示します




```bash
# ホームディレクトリにマウントポイント用のディレクトリを作成
[testuser@localhost ~]$ cd && mkdir mount_test

# マウント
[testuser@localhost ~]$ sudo mount /dev/sdb1 ~/mount_test/

# マウントされたことを確認
[testuser@localhost ~]$ df -Th
ファイルシス        タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs            devtmpfs   4.0M     0  4.0M    0% /dev
tmpfs               tmpfs      1.8G     0  1.8G    0% /dev/shm
tmpfs               tmpfs      729M  9.2M  720M    2% /run
efivarfs            efivarfs   256K   35K  217K   14% /sys/firmware/efi/efivars
/dev/mapper/cs-root xfs         17G  5.2G   12G   32% /
/dev/sda2           xfs        960M  339M  622M   36% /boot
/dev/sda1           vfat       599M  7.5M  592M    2% /boot/efi
tmpfs               tmpfs      365M  108K  365M    1% /run/user/1000
/dev/sdb1           xfs        2.0G   47M  1.9G    3% /home/testuser/mount_test
```

<br>

**自動的にマウントする**

```bash
# ファイルシステムをアンマウントする
[testuser@localhost ~]$ sudo umount /dev/sdb1

# fstabのバックアップを取得
sudo cp -p /etc/fstab /etc/fstab_$(date +%Y%m%d)

# /etc/fstabを編集
[testuser@localhost ~]$ echo "/dev/sdb1 $HOME/mount_test/ xfs defaults 0 0" | sudo tee -a /etc/fstab
/dev/sdb1 /home/testuser/mount_test/ xfs defaults 0 0

# fstabに記述されている内容をマウント
[testuser@localhost ~]$ sudo mount -a

# サーバーを再起動/dev/sdb1がマウントされていることを確認
[testuser@localhost ~]$ sudo shutdown -r now

# サーバーの再起動後、
[testuser@localhost ~]$ df -Th
ファイルシス        タイプ   サイズ  使用  残り 使用% マウント位置
devtmpfs            devtmpfs   4.0M     0  4.0M    0% /dev
tmpfs               tmpfs      1.8G     0  1.8G    0% /dev/shm
tmpfs               tmpfs      729M  9.2M  720M    2% /run
efivarfs            efivarfs   256K   77K  175K   31% /sys/firmware/efi/efivars
/dev/mapper/cs-root xfs         17G  5.4G   11G   33% /
/dev/sda2           xfs        960M  339M  622M   36% /boot
/dev/sdb1           xfs        2.0G   47M  1.9G    3% /home/testuser/mount_test
/dev/sda1           vfat       599M  7.5M  592M    2% /boot/efi
tmpfs               tmpfs      365M   52K  365M    1% /run/user/42
tmpfs               tmpfs      365M   36K  365M    1% /run/user/1000
```

## バックアップ


### xfsdump コマンド

XFSファイルシステムのバックアップを作成するためのコマンドです。このコマンドは、XFSファイルシステムの内容をバックアップファイルとして保存するのに使用されます。

<br>

書式

```bash
xfsdump [オプション] <バックアップ先>
```



<br>

オプション

オプション|説明
-|-
-l|レベルを指定します。バックアップのレベルを指定することで、全体バックアップや増分バックアップが可能です。
-f|バックアップファイルの名前を指定します。指定しない場合は標準出力にバックアップが作成されます。
-h|バックアップ対象のファイルシステムのサイズ情報を表示します。

<br>

dumpファイル作成例

```bash
# 先程マウントしたカレントディレクトリに何もないことを確認
[testuser@localhost ~]$ cd ~/mount_test/ && pwd && ls -l
/home/testuser/mount_test
合計 0

# テスト用ファイル作成
[testuser@localhost mount_test]$ cat <<EOS > dump_test
test
EOS

# dumpファイル作成
[testuser@localhost mount_test]$ sudo xfsdump -f ~/mount_test/dumptest.dmp /dev/sdb1
xfsdump: using file dump (drive_simple) strategy
xfsdump: version 3.1.12 (dump format 3.0) - type ^C for status and control

 ============================= dump label dialog ==============================

please enter label for this dump session (timeout in 300 sec)
 -> full_2024_08_30 # ラベルを入力します
session label entered: "full_2024_08_30"

 --------------------------------- end dialog ---------------------------------

xfsdump: level 0 dump of localhost.localdomain:/home/testuser/mount_test
xfsdump: dump date: Fri Aug 30 03:02:08 2024
xfsdump: session id: 9af1dc53-3d0f-475c-bd90-e088afd798d0
xfsdump: session label: "full_2024_08_30"
xfsdump: ino map phase 1: constructing initial dump list
xfsdump: ino map phase 2: skipping (no pruning necessary)
xfsdump: ino map phase 3: skipping (only one dump stream)
xfsdump: ino map construction complete
xfsdump: estimated dump size: 25536 bytes

 ============================= media label dialog =============================

please enter label for media in drive 0 (timeout in 300 sec)
 -> full_2024_08_30_media　# ラベルを入力します
media label entered: "full_2024_08_30_media"

 --------------------------------- end dialog ---------------------------------

xfsdump: creating dump session media file 0 (media 0, file 0)
xfsdump: dumping ino map
xfsdump: dumping directories
xfsdump: dumping non-directory files
xfsdump: ending media file
xfsdump: media file size 22872 bytes
xfsdump: dump size (non-dir files) : 544 bytes
xfsdump: dump complete: 19 seconds elapsed
xfsdump: Dump Summary:
xfsdump:   stream 0 /home/testuser/mount_test/dumptest.dmp OK (success)
xfsdump: Dump Status: SUCCESS

# dumpファイルが作成された
[testuser@localhost mount_test]$ ls -l dumptest.dmp
-rw-r--r--. 1 root root 22872  8月 30 03:02 dumptest.dmp
```

<br>


## リストア

### xfsrestore コマンド

`xfsrestore` コマンドは、`xfsdump` で作成したバックアップファイルからデータをリストア（復元）するために使用します。`xfsdump` で取得したファイルシステムのスナップショットを、指定されたディレクトリに復元することが可能です。

<br>

書式

```
xfsrestore -f <バックアップファイル> <復元先ディレクトリ>
```



<br>

オプション

オプション|説明
-|-
-f|バックアップファイルを指定します。
-i|インタラクティブモードで動作し、ファイルやディレクトリを個別に復元できます。
-I|セッションのインベントリを表示します。
-r|差分バックアップの復元を行います。
-v|復元の詳細な情報を出力します。

<br>

リストア実行例

```bash
[testuser@localhost mount_test]$ xfsrestore -I

# ■■■中略■■■

file system 2:
        fs id:          f18b6036-4212-42d2-b4d5-23fb9dac5943
        session 0:
                mount point:    localhost.localdomain:/home/testuser/mount_test
                device:         localhost.localdomain:/dev/sdb1
                time:           Fri Aug 30 03:02:08 2024
                session label:  "full_2024_08_30"
                session id:     9af1dc53-3d0f-475c-bd90-e088afd798d0
                level:          0
                resumed:        NO
                subtree:        NO
                streams:        1
                stream 0:
                        pathname:       /home/testuser/mount_test/dumptest.dmp
                        start:          ino 131 offset 0
                        end:            ino 133 offset 0
                        interrupted:    NO
                        media files:    1
                        media file 0:
                                mfile index:    0
                                mfile type:     data
                                mfile size:     22872
                                mfile start:    ino 131 offset 0
                                mfile end:      ino 133 offset 0
                                media label:    "full_2024_08_30_media"
                                media id:       1d4b2345-5dc0-44d2-94a4-2cd500eadc5a

# テスト用ファイルのファイルを削除
[testuser@localhost ~]$ rm ~/mount_test/dump_test

# リストア用ファイルを移動
# リストア用のファイルがリストア先にあるとリストア対象とみなされ、dumpファイルが0バイトになってしまうため
[testuser@localhost mount_test]$ mv dumptest.dmp ~ && cd

# リストア実施
[testuser@localhost ~]$ xfsrestore -f dumptest.dmp ~/mount_test/
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.12 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description:
xfsrestore: hostname: localhost.localdomain
xfsrestore: mount point: /home/testuser/mount_test
xfsrestore: volume: /dev/sdb1
xfsrestore: session time: Fri Aug 30 03:02:08 2024
xfsrestore: level: 0
xfsrestore: session label: "full_2024_08_30"
xfsrestore: media label: "full_2024_08_30_media"
xfsrestore: file system id: f18b6036-4212-42d2-b4d5-23fb9dac5943
xfsrestore: session id: 9af1dc53-3d0f-475c-bd90-e088afd798d0
xfsrestore: media id: 1d4b2345-5dc0-44d2-94a4-2cd500eadc5a
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 1 directories and 2 entries processed
xfsrestore: directory post-processing
xfsrestore: restoring non-directory files
xfsrestore: restore complete: 0 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /home/testuser/dumptest.dmp OK (success)
xfsrestore: Restore Status: SUCCESS

# dump_testが復元できた
[testuser@localhost ~]$ ll mount_test/
合計 4
-rw-r--r--. 1 testuser testuser 5  8月 30 03:01 dump_test
-rw-r--r--. 1 testuser testuser 0  8月 30 03:02 dumptest.dmp


# インタラクティブモードでファイルを指定して復元
[testuser@localhost ~]$ xfsrestore -i -f dumptest.dmp ~/mount_test/
xfsrestore: using file dump (drive_simple) strategy
xfsrestore: version 3.1.12 (dump format 3.0) - type ^C for status and control
xfsrestore: searching media for dump
xfsrestore: examining media file 0
xfsrestore: dump description:
xfsrestore: hostname: localhost.localdomain
xfsrestore: mount point: /home/testuser/mount_test
xfsrestore: volume: /dev/sdb1
xfsrestore: session time: Fri Aug 30 03:02:08 2024
xfsrestore: level: 0
xfsrestore: session label: "full_2024_08_30"
xfsrestore: media label: "full_2024_08_30_media"
xfsrestore: file system id: f18b6036-4212-42d2-b4d5-23fb9dac5943
xfsrestore: session id: 9af1dc53-3d0f-475c-bd90-e088afd798d0
xfsrestore: media id: 1d4b2345-5dc0-44d2-94a4-2cd500eadc5a
xfsrestore: using online session inventory
xfsrestore: searching media for directory dump
xfsrestore: reading directories
xfsrestore: 1 directories and 2 entries processed
xfsrestore: directory post-processing

 ========================== subtree selection dialog ==========================

# 現在使用できるコマンドが表示される
the following commands are available:
        pwd
        ls [ <path> ]
        cd [ <path> ]
        add [ <path> ]
        delete [ <path> ]
        extract
        quit
        help

# 復元できるファイルを確認
 -> ls
             132 dumptest.dmp
             131 dump_test

# dump_testを復元対象に加える
 -> add dump_test

# 選択したファイルやディレクトリを復元
 -> extract

 --------------------------------- end dialog ---------------------------------

xfsrestore: restoring non-directory files
xfsrestore: restore complete: 68 seconds elapsed
xfsrestore: Restore Summary:
xfsrestore:   stream 0 /home/testuser/dumptest.dmp OK (success)
xfsrestore: Restore Status: SUCCESS
```
