

# ユーザーとグループ管理

## ユーザーの作成と削除

### useradd コマンド

新しいユーザーを作成するには、useradd コマンドを使用します。

<br>

書式

```
useradd [オプション] ユーザー名
```

#### useradd コマンドの主なオプション

| オプション | 説明 |
|------------|------|
| -c "コメント" | ユーザーのコメント（フルネームなど）を指定します。 |
| -d ホームディレクトリ | ユーザーのホームディレクトリを指定します。指定しない場合は /home/ユーザー名 になります。 |
| -e YYYY-MM-DD | ユーザーアカウントの有効期限を指定します。 |
| -G グループ1,グループ2 | ユーザーを追加する補助グループを指定します。 |
| -s シェル | ユーザーのログインシェルを指定します。指定しない場合はデフォルトのシェルになります。 |


<br>

実行例

```bash
# user taroを追加。その際コメント、ホームディレクトリ、グループ、ログインシェルを指定
[testuser@localhost work]$ sudo useradd -c "Taro Yamada" -d /home/taro -G wheel -s /bin/bash taro
[sudo] testuser のパスワード:

# user taroが作成されたことを確認
[testuser@localhost work]$ id taro
uid=1002(taro) gid=1003(taro) groups=1003(taro),10(wheel)
```

### userdel コマンド

既存のユーザーを削除するには、userdel コマンドを使用します。

<br>

書式

```bash
userdel [オプション] ユーザー名
```

実行例

```bash
# ホームディレクトリ毎ユーザーを削除
[testuser@localhost work]$ sudo userdel -r taro
[sudo] testuser のパスワード:

# 削除されたことを確認
[testuser@localhost work]$ id taro
id: `taro': no such user
```

## グループの作成と削除

### groupadd コマンド

新しいグループを作成するには、groupadd コマンドを使用します。

```bash
groupadd [オプション] グループ名
```

#### groupadd コマンドの主なオプション

| オプション | 説明 |
|------------|------|
| -g グループID | グループID (GID) を指定します。指定しない場合はシステムが自動で決定します。 |
| -r | システムグループを作成します。通常のグループとは異なり、システムグループは特別な用途に使われます。 |

<br>

実行例

```bash
# GID 9999でグループを作成
[testuser@localhost ~]$ sudo groupadd -g 9999 test-group

# グループが作成されたことを確認
[testuser@localhost ~]$ grep 9999 /etc/group
test-group:x:9999:

# システムグループを作成
[testuser@localhost ~]$ sudo groupadd -r backup

# グループが作成されたことを確認
[testuser@localhost ~]$ getent group | grep backup
backup:x:980:
```

### groupdel コマンド

既存のグループを削除するには、groupdel コマンドを使用します。

```bash
# グループの削除
[testuser@localhost ~]$ sudo groupdel backup

# グループが削除されたことを確認
[testuser@localhost ~]$ getent group | grep backup
[testuser@localhost ~]$
```


## ユーザー情報の変更

### usermod コマンド

usermod コマンドは、既存のユーザーアカウントの情報を変更するために使用されます。

書式

```
usermod [オプション] ユーザー名
```


```bash
# テスト用ユーザーを作成
[testuser@localhost ~]$ sudo useradd -c "Taro Yamada" -d /home/yamada/test -G wheel -s /bin/bash yamada

# 現在の状態を確認
[testuser@localhost work]$ getent passwd | grep yamada
yamada:x:1002:1002:Taro Yamada:/home/yamada/test:/bin/bash

# ユーザーのコメントを変更する
[testuser@localhost ~]$ sudo usermod -c "New Comment" yamada

# ホームディレクトリを変更する
[testuser@localhost ~]$ sudo usermod -d /home/yamada/ yamada

# コメント、ホームディレクトリが変更された
[testuser@localhost work]$ getent passwd | grep yamada
yamada:x:1002:1002:Taro Yamada:/home/yamada/:/bin/bash

```

<br>

グループの変更

```bash
# ユーザーの情報確認
[testuser@localhost work]$ id yamada
uid=1002(yamada) gid=1002(yamada) groups=1002(yamada),10(wheel)

# プライマリグループを変更
[testuser@localhost ~]$ sudo usermod -g test-group yamada

# プライマリグループが変更された
[testuser@localhost ~]$ id yamada
uid=1002(yamada) gid=9999(test-group) groups=9999(test-group),10(wheel)

# テスト用グループ作成
[testuser@localhost ~]$ sudo groupadd sub-group

# サブグループに追加
[testuser@localhost ~]$ sudo usermod -aG sub-group yamada

# サブグループに追加されたことが確認できる
[testuser@localhost ~]$ id yamada
uid=1002(yamada) gid=9999(test-group) groups=9999(test-group),10(wheel),10001(sub-group)

# サブグループをtest-group,wheelにする（sub-groupから抜ける）
[testuser@localhost ~]$ sudo usermod -G test-group,wheel yamada

# sub-groupから抜けていることを確認
[testuser@localhost ~]$ id yamada
uid=1002(yamada) gid=9999(test-group) groups=9999(test-group),10(wheel)


```

<br>

アカウントロック

```bash
[testuser@localhost ~]$ sudo passwd yamada
ユーザー yamada のパスワードを変更。
新しい パスワード:
正しくないパスワード: このパスワードは 8 文字未満の文字列です
新しい パスワードを再入力してください:
passwd: すべての認証トークンが正しく更新できました。

# アカウントをロックする
[testuser@localhost ~]$ sudo usermod -L yamada

# ロックされたことを確認
[testuser@localhost ~]$ sudo passwd -S yamada
yamada LK 2024-08-07 0 99999 7 -1 (パスワードはロック済み。)

# 認証が失敗します
[testuser@localhost ~]$ su - yamada
パスワード:
su: 認証失敗

# アカウントのロックを解除する
[testuser@localhost ~]$ sudo usermod -U yamada

# 再度パスワードを設定します
[testuser@localhost ~]$ sudo passwd yamada
ユーザー yamada のパスワードを変更。
新しい パスワード:
正しくないパスワード: このパスワードは 8 文字未満の文字列です
新しい パスワードを再入力してください:
passwd: すべての認証トークンが正しく更新できました。

# アカウントロックの状態を確認
[testuser@localhost ~]$ sudo passwd -S yamada
yamada PS 2024-08-07 0 99999 7 -1 (パスワード設定済み、SHA512 暗号化。)
[testuser@localhost ~]$
```

# パーミッションと所有権の設定

## ファイルとディレクトリの所有権

Linuxでは、ファイルとディレクトリには3つの主要な属性があります: 所有者（ユーザー）、グループ、その他（その他のユーザー）。これにより、アクセス制御が可能になります。

| **項目** | **説明** |
|----------|----------|
| 所有者（User） | ファイルやディレクトリを作成したユーザー。 |
| グループ（Group） | 所有者が所属するグループ。 |
| その他（Others） | 所有者でもグループメンバーでもない全てのユーザー。 |

## パーミッション（権限）

ファイルやディレクトリには3種類の権限が設定できます。それぞれの権限は、読み取り（r）、書き込み（w）、実行（x）です。パーミッションは、所有者、グループ、その他のユーザーに対して設定されます。

| **権限** | **説明** |
|----------|----------|
| 読み取り（r） | ファイルの内容を読み取ることができる。 |
| 書き込み（w） | ファイルの内容を変更することができる。 |
| 実行（x） | ファイルを実行することができる（ディレクトリの場合は、そのディレクトリにアクセスして内容を表示することができる）。 |

### パーミッションの表示

ls -l コマンドを使用して、ファイルやディレクトリの詳細な情報を表示できます。出力例とその説明は以下の通りです。

```bash
[testuser@localhost work]$ ls -l
-rw-r--r--. 1 testuser testuser 0  7月 20 12:46 test1
```

| **位置** | **説明** |
|----------|----------|
| 1        | ファイルタイプ（`-` は通常ファイル、`d` はディレクトリ） |
| 2-4      | 所有者のパーミッション（読み取り、書き込み、実行） |
| 5-7      | グループのパーミッション（読み取り、書き込み、実行） |
| 8-10     | その他のユーザーのパーミッション（読み取り、書き込み、実行） |


### パーミッションの変更

chmod コマンドを使用して、ファイルやディレクトリのパーミッションを変更できます。chmod コマンドは、符号（記号モード）または数字（数値モード）で権限を設定できます。

**記号モード**

| **記号** | **説明** |
|----------|----------|
| u        | ユーザー/所有者 |
| g        | グループ |
| o        | その他 |
| a        | 全ユーザー |


```bash
chmod u+x ファイル    # 所有者に実行権限を追加
chmod go-w ファイル   # グループとその他のユーザーから書き込み権限を削除
chmod a=r ファイル    # 全ユーザーに読み取り権限を付与し、書き込みと実行権限を削除
```

所有者に実行権限を追加

```bash:実行例
chmod u+x ファイル
```

```bash:実行例
[testuser@localhost work]$ ll
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 20 12:46 test1

# 所有者に実行権限を追加
[testuser@localhost work]$ chmod u+x test1

# 実行権限　-rw「x」r--r--.　が付与されていることを確認　
[testuser@localhost work]$ ll
合計 0
-rwxr--r--. 1 testuser testuser 0  7月 20 12:46 test1
```

**数値モード**

権限は数字で指定します。読み取り（4）、書き込み（2）、実行（1）の合計で設定します。

```bash
chmod 644 ファイル # 所有者に読み取りと書き込み権限を、グループとその他のユーザーに読み取り権限を設定
```

```bash:実行例
# 現在のパーミッションは744
[testuser@localhost work]$ ll
合計 0
-rwxr--r--. 1 testuser testuser 0  7月 20 12:46 test1

# パーミッションの変更
[testuser@localhost work]$ chmod 644 test1

# 変更後644となっていることを確認
[testuser@localhost work]$ ll
合計 0
-rw-r--r--. 1 testuser testuser 0  7月 20 12:46 test1
```

## 所有者とグループの変更

chown コマンドを使用して、ファイルやディレクトリの所有者やグループを変更できます。

```bash
chown ユーザー:グループ ファイル
```

オプション

| オプション                | 説明                                                                                               |
|---------------------------|----------------------------------------------------------------------------------------------------|
| -c, --changes             | 変更が行われた場合にのみ報告（詳細モードに似ているが、変更があった場合にのみ報告）                   |
| -v, --verbose             | 処理された各ファイルの診断情報を出力                                                                 |
| --preserve-root           | '/'に対して再帰的に操作しない                                                                       |
| -R, --recursive           | ファイルおよびディレクトリに対して再帰的に操作                                                      |

```bash:実行例
# 現在の所有者-->testuser グループ-->testuserとなっている
[testuser@localhost work]$ ls -l
合計 0
-rw-r--r--. 1 testuser testgroup 0  7月 20 12:46 test1

# 所有者をuser1、グループをtestgroupに変更
[testuser@localhost work]$ sudo chown user1:testgroup test1

# 所有者-->user1 グループ-->testgroupとなっていることを確認
[root@localhost work] ls -l
合計 0
-rw-r--r--. 1 user1 testgroup 0  7月 20 12:46 test1
```

<br>

パーミッションのテスト例

```bash

# テスト用ユーザー作成
[testuser@localhost ~]$ sudo useradd sato

# 作成されたユーザー情報を確認
[testuser@localhost ~]$ id sato
uid=1003(sato) gid=1003(sato) groups=1003(sato)

# ユーザーのホームディレクトリのパーミッションを確認
# drwx------となっていてtestuserではアクセスできない
[testuser@localhost ~]$ ls -l /home/
合計 4
drwx------   3 sato     sato       78  8月  7 23:26 sato
drwx------. 15 testuser testuser 4096  8月  7 23:22 testuser

# 想定通りアクセスできないことを確認
[testuser@localhost ~]$ cd /home/sato
-bash: cd: /home/sato: 許可がありません

# ホームディレクトリに同じグループへのアクセス権を付与
[testuser@localhost ~]$ sudo chmod 750 /home/sato/

# drwxr-x---となりsatoグループに属しているユーザならアクセスできる状態になった
[testuser@localhost ~]$ ls -l /home/
合計 4
drwxr-x---   3 sato     sato       78  8月  7 23:26 sato
drwx------. 15 testuser testuser 4096  8月  7 23:22 testuser

# 今はまだsatoグループに属していないためアクセスできない
[testuser@localhost ~]$ cd /home/sato
-bash: cd: /home/sato: 許可がありません

# testuserをsatoグループに追加
[testuser@localhost ~]$ sudo usermod -aG sato testuser

# satoグループに追加されたことを確認
[testuser@localhost ~]$ id testuser
uid=1000(testuser) gid=1000(testuser) groups=1000(testuser),1003(sato)

# シェルがユーザーのグループメンバーシップを認識するためログインシェルを再起動
[testuser@localhost ~]$ su - testuser
パスワード:

# /home/satoにアクセスできた
[testuser@localhost ~]$ cd /home/sato/
[testuser@localhost sato]$
```
