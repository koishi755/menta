# 1-1 VirtulBox
Oracle VirtualBox（オラクル バーチャルボックス）は、コンピュータの中に仮想マシンを作成するためのソフトウェアです。今回はこちらに仮想マシンを作成し、CentOSをインストールします。

<br>

## 1-1-1 VirtulBoxのダウンロード

以下のリンクからVirtulBoxをダウンロードします。
https://www.oracle.com/jp/virtualization/technologies/vm/downloads/virtualbox-downloads.html

<br>

対応するOSのインストーラーをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/d5b956d1f46a-20240711.png)

<br>

## 1-1-2 VirtulBoxのインストール

イントーラーをダブルクリックします。
![](https://storage.googleapis.com/zenn-user-upload/4fd381f821e6-20240711.png)

<br>

「Next」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/9ea3eeae360c-20240711.png)

<br>

インストールフォルダについてデフォルトで問題なければ「Next」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/d141d0152a38-20240711.png)

<br>

「Yes」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/284ebf93c11b-20240711.png)

<br>

「Install」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/616fbdc3ba34-20240711.png)

<br>

「Finish」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/c3c798af9bb4-20240711.png)


# 1-2 CentOS
CentOSは「OS（オペレーティングシステム）」の一つです。<br>

## 1-2-1 CentOSのイメージのダウンロード

以下のサイトからCentOSのイメージをダウンロードします。
https://www.centos.org/download/

<br>

インストールするOSのアーキテクチャを確認し、Architectures以下のリンクをクリックします。
![](https://storage.googleapis.com/zenn-user-upload/abc5faac9b03-20240711.png)

<br>

私の環境ではx64だったので、「x86_64」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/446a82ddc6ef-20240711.png)

<br>

今回のインストールとは関係ないですが、ここから過去のバージョンを探すことができます。
https://vault.centos.org/


## 1-2-2 仮想マシンの作成

「新規」をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/755763081604-20240711.png)

<br>

以下の項目を設定し、「次へ」をクリックします。

項目|説明
-|-
名前|作成する仮想マシンの名前を入力します。
Folder|どのフォルダに仮想マシンを作成するか指定します。
ISO Image|インストールするISOイメージを指定します。
Skip Unattended Installation|自動インストールをスキップします。

![](https://storage.googleapis.com/zenn-user-upload/57a3646d5252-20240711.png)

<br>

仮想マシンに割り当てる メモリー、CPUを設定します。
![](https://storage.googleapis.com/zenn-user-upload/b3e351365093-20240711.png)

<br>

仮想マシンに割り当てるDiskのサイズを指定し、Diskを作成します。
![](https://storage.googleapis.com/zenn-user-upload/ccc6b9753c4d-20240711.png)

<br>

問題がなければ「完了」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/b205c807a7a9-20240711.png)

<br>

## 1-2-3 CentOSのインストール

作成した仮想マシンを選択し、「起動」をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/8780180b700e-20240711.png)

<br>

[Esc]を押してこちらのcheckはスキップできます。

![](https://storage.googleapis.com/zenn-user-upload/0fdf9d4c9590-20240711.png)

<br>

使用する言語を選択し、「続行」をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/7405845d218d-20240711.png)

<br>

！アイコンがついている「インストール先」「rootパスワード」「ユーザーの作成」を設定します。

![](https://storage.googleapis.com/zenn-user-upload/d36477b7c553-20240711.png)

<br>

**インストール先**
作成した仮想ディスクが既に選択されていると思いますので、そのまま「完了」をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/09aa72056e6e-20240711.png)

<br>

**rootパスワード**

rootパスワードを設定し、「完了」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/e700e9053257-20240711.png)

<br>

**ユーザーの作成**

任意のユーザー名を入力し、ユーザーを作成します。「フルネーム」～「パスワードの確認」まで入力したら、「完了」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/a61b10c2eefb-20240711.png)

<br>

「インストールの開始」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/bb87bcbbc4fe-20240711.png)

<br>

インストールが開始されます。
![](https://storage.googleapis.com/zenn-user-upload/9fbced40db7b-20240711.png)

<br>

インストールが完了すると再起動を求められます。「システムの再起動」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/2fccb186e93d-20240711.png)

## 1-2-4 ログイン

システム再起動後、ログイン画面が表示されます。
![](https://storage.googleapis.com/zenn-user-upload/5002afd3e86d-20240711.png)

<br>

パスワードを入力し、「Enter」を押します。
![](https://storage.googleapis.com/zenn-user-upload/4509fe571b26-20240711.png)

<br>

## 1-2-5 SSHでのログイン

「アクティビティ」-->「端末」を開きます。
![](https://storage.googleapis.com/zenn-user-upload/63868480c909-20240711.png)


rootユーザーにスイッチし、sshdの状態を確認します。

```
su -
```


```
systemctl status sshd
```

<br>

「active(running)」と表示されていることからsshdが起動していることが確認できました。

![](https://storage.googleapis.com/zenn-user-upload/0a451b0c2764-20240711.png)

<br>

仮想マシンのネットワークを確認します。仮想マシンには「NAT」が割り当てられていました。このままだとホストのPCから仮想マシンへ接続できないので、設定を変える必要があります。

![](https://storage.googleapis.com/zenn-user-upload/43dc558d7ece-20240711.png)

<br>

仮想マシン起動時にネットワークの変更をすることができないので、以下のコマンドで仮想マシンをシャットダウンします。

```
shutdown -h now
```

<br>

対象の仮想マシンを選択し、「設定」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/4dfd50480f47-20240711.png)

<br>

今回は以下の手順でアダプター2に新しいネットワークを作成します。

1. 「ネットワーク」を選択します。
2. アダプター２を選択します。
3. 「ブリッジーアダプター」を選択します。
4. 「OK」をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/7b6be5f642e8-20240711.png)

<br>

IPアドレスを確認します。

```
ip a
```

<br>

「192.168.10.194」となっていましたので、こちらIPアドレス宛にssh接続をします。
![](https://storage.googleapis.com/zenn-user-upload/1d9f02f1cd47-20240711.png)

<br>

今回はTeratermでssh接続します。ホストに先程確認したIPアドレスを入力し、「OK」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/e9bc66571599-20240711.png)

<br>

「ユーザー名」、「パスフレーズ」を入力し、「OK」をクリックします。
![](https://storage.googleapis.com/zenn-user-upload/5d60f72a83fc-20240711.png)

<br>

作成した仮想マシンにssh接続ができました。
![](https://storage.googleapis.com/zenn-user-upload/c06c4234f65b-20240711.png)


