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
