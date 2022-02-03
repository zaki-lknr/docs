# ssh関連

## ssh client

### 鍵作る

```console
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa_nopass -N ""
Generating public/private rsa key pair.
Your identification has been saved in /home/zaki/.ssh/id_rsa_nopass.
Your public key has been saved in /home/zaki/.ssh/id_rsa_nopass.pub.
The key fingerprint is:
SHA256:vDhmhHyGepTu929HGu0oPPKS/2HrJexF97CBWu9k0M4 zaki@cloud-dev
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|                 |
|                 |
|   . + .     o   |
|    * + S  .= =  |
|   + + . o.+o* = |
|  . o =o. **o E .|
|   o o=.+o+*o+   |
|    .. *+B*.  .  |
+----[SHA256]-----+
$ ls ~/.ssh/
id_rsa  id_rsa.pub  id_rsa_nopass  id_rsa_nopass.pub  known_hosts
```

### リモートへ公開鍵コピー

```console
$ ssh-copy-id -i ~/.ssh/id_rsa_nopass 192.168.0.31
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/zaki/.ssh/id_rsa_nopass.pub"
The authenticity of host '192.168.0.31 (192.168.0.31)' can't be established.
ECDSA key fingerprint is SHA256:WQpvcX7UfYAmdFIMfhak4pCevQvOjZkIZ/fpE4Sofm4.
ECDSA key fingerprint is MD5:97:15:37:72:88:ab:59:01:ad:3c:a2:e9:10:90:11:59.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
zaki@192.168.0.31's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh '192.168.0.31'"
and check to make sure that only the key(s) you wanted were added.

$
```

### 鍵指定してssh

```console
$ ssh -i ~/.ssh/id_rsa_nopass 192.168.0.31
Last login: Tue Aug 11 14:52:32 2020 from 192.168.0.18
$ 
```

### 多段アクセス

`ProxyCommand`を使った情報がたくさんヒットするけど、`ProxyJump (-J)`を使うと簡単。  
ローカルから192.168.0.19→172.16.1.0→172.29.0.89という順でssh多段アクセスしたい場合は、以下の通り。

```console
$ ssh 172.29.0.89 -J 192.168.0.19,172.16.1.0
```

記述の仕方は

```text
ssh ゴール(終着点) -J 踏み台ホストをアクセス順にカンマ区切りで羅列
```

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ローカルから192.168.0.19→172.16.1.0→172.29.0.89という順でssh多段アクセスしたい場合は、<br>$ ssh 172.29.0.89 -J 192.168.0.19,172.16.1.0<br>でいける。 <a href="https://t.co/7xHlxvxFTr">pic.twitter.com/7xHlxvxFTr</a></p>&mdash; z a k i 🌈🌉 (@zaki_hmkc) <a href="https://twitter.com/zaki_hmkc/status/1487383030251462658?ref_src=twsrc%5Etfw">January 29, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

#### 多段アクセスとポートフォワードのコンボ

`-L`で普通に指定すればOK。  
機能自体は後述。

上の多段アクセス例であれば、

```console
$ ssh 172.29.0.89 -J 192.168.0.19,172.16.1.0 -L 8899:localhost:8080
```

とすれば、「`172.29.0.89`から見た`localhost:8080`」へ対して、「ローカル(sshを実行してる出発点)の`localhost:8899`」をフォワードする。

### ポートフォワード

#### ローカルフォワード

```console
$ ssh rhel8 -L 25080:10.88.250.10:80
```

`rhel8`へsshログインしつつ、`rhel8`から見て`10.88.250.10:80`へのアクセスをローカルの`25080/TCP`でListenする。  
(ローカルの`25080/TCP`へアクセスすると`rhel8`から見た`10.88.250.10:80`へ転送される)

---

## sshd

### 設定ファイルsyntax check

```console
# sshd -t
```

規定に無い設定キー(というかゴミ)があったりするとエラーになる

```console
# sshd -t
/etc/ssh/sshd_config: line 140: Bad configuration option: a
/etc/ssh/sshd_config: terminating, 1 bad configuration options
```

### 有効になる設定のリスト

実行する環境の`/etc/ssh/sshd_config`の結果、どの設定が適用されるかのすべての内容が表示される。  
(`/etc/ssh_sshd_config`に書かなかったデフォルトの設定含めて出力される)

```console
# sshd -T
```

デフォルトとの差分を見たい場合は以下で見ようと思えば見れる。

```console
# diff -u <(sshd -T -f /dev/null)  <(sshd -T)
```

### ポート番号の変更

SELinuxが有効の場合はsshdの設定変更だけでは有効にならない。  
`Port 25022`を設定したときに、、

```
# systemctl restart sshd
Job for sshd.service failed because the control process exited with error code. See "systemctl status sshd.service" and "journalctl -xe" for details.
# journalctl -xe
sshd[20141]: error: Bind to port 25022 on 0.0.0.0 failed: Permission denied.
sshd[20141]: error: Bind to port 25022 on :: failed: Permission denied.
sshd[20141]: fatal: Cannot bind any address.
systemd[1]: sshd.service: main process exited, code=exited, status=255/n/a
systemd[1]: Failed to start OpenSSH server daemon.
```

こんなエラーが出る場合は、以下を実行。

```console
# semanage port -a -t ssh_port_t -p tcp 25022
```

これで`systemctl restart sshd`が有効になる。
まぁsshd_configの`Port`のすぐ上にコメントで書かれてるんだけど。

## 鍵

### フィンガープリント確認

```console
$ ssh-keygen -l -f ~/.ssh/id_rsa.pub
4096 SHA256:6Qt0+Nikcx/NpgM4xu/4WnDpqhj8L+OjtDEP5O2a7lw no comment (RSA)
```

### 秘密鍵から公開鍵を作成

```console
$ ssh-keygen -y -f ~/.ssh/id_rsa
```

STDOUTへ出力されるので、`> id_rsa.pub`とかファイルへ出力すればOK
