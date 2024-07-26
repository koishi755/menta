# SELinux

SELinux (Security-Enhanced Linux) は、Linuxカーネルに統合されたセキュリティモジュールで、強制アクセス制御 (MAC: Mandatory Access Control) を提供します。これは、システム管理者が定義したポリシーに基づいてアクセス権限を制御し、より高いセキュリティレベルを実現するための仕組みです。開発環境などでは無効にしておく方が良い場合もあります。

## SELinuxのステータス
SELinuxのステータスには主に以下の3つがあります：

| ステータス|説明|
|-|-|
| **Enforcing (強制モード)** | - SELinuxが有効になっており、ポリシーに従ってアクセス制御を強制的に適用します。<br>- 許可されていない操作はブロックされ、ログに記録されます。<br>- 通常の運用時に使用されるモードです。 |
| **Permissive (許可モード)**| - SELinuxが有効になっていますが、ポリシーに違反する操作があっても実際にはブロックされません。<br>- 違反があった場合はログに記録されるだけで、操作は実行されます。<br>- ポリシーのデバッグや新しいポリシーのテスト時に使用されます。 |
| **Disabled (無効モード)**  | - SELinuxが完全に無効になっています。<br>- ポリシーは適用されず、SELinuxの機能も使用されません。<br>- セキュリティ強化が不要な場合や、SELinuxが原因でシステムのトラブルシューティングを行う場合に一時的に使用されることがあります。 |

<br>

SELinuxの現在のステータスを確認するには、以下のコマンドを使用します。

```
getenforce
```

<br>

現在は有効な状態になっています。
```
[root@localhost ~]# getenforce
Enforcing <-- 有効
```

<br>

より詳細な状態を確認するには「sestatus」コマンドを使用します。

```
sestatus
```

```
[root@localhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
```

## ステータスの変更

SELinuxを一時的（システム再起動まで）に無効にする。


```
setenforce 0
```

```.bash
[root@localhost ~]# getenforce　<--状態確認
Enforcing　<--有効状態
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# setenforce 0　<--一時的に無効化
[root@localhost ~]#
[root@localhost ~]#
[root@localhost ~]# getenforce　<--状態確認
Permissive　　<--無効
```

<br>

永続的に無効にするには「/etc/selinux/config」のSELINUXの行を「permissive」または「disabled」に変更します。

```
vim /etc/selinux/config
```

```:/etc/selinux/config
SELINUX=disabled
```

<br>

**sed を使用して変更**

Enforcingモードに変更する場合
```
sudo sed -i 's/^SELINUX=.*/SELINUX=enforcing/' /etc/selinux/config
```

<br>

Permissiveモードに変更する場合

```
sudo sed -i 's/^SELINUX=.*/SELINUX=permissive/' /etc/selinux/config
```

<br>

Disabledモードに変更する場合

```
sudo sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config
```

<br>

設定を適用させるため再起動します。

```
reboot
```

<br>

# SSHの設定

SSH (Secure Shell) は、安全なリモートログインと他のネットワークサービスへの安全なアクセスを提供するプロトコルです。SSHは暗号化技術を使用してデータを保護し、認証を強化します。SSHには様々な設定項目がありますが、今回はrootユーザーによるログインを制限します。

## rootでのログインの制限

設定ファイルのバックアップを取得します。

```
cp -p /etc/ssh/sshd_config /etc/ssh/sshd_config_backup
```

<br>

設定ファイルを開きます。

```
vim /etc/ssh/sshd_config
```

<br>

今回は「#PermitRootLogin prohibit-password」となっており、コメントアウトされていました。コメントはそのままで「PermitRootLogin no」を追記します。

```diff :/etc/ssh/sshd_config
# Authentication:

#LoginGraceTime 2m
#PermitRootLogin prohibit-password
+ PermitRootLogin no
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10
```

<br>

設定を反映させます。

```
systemctl reload sshd
```
