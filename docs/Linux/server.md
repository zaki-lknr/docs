# サーバー関連

## samba

### ユーザー追加

ユーザー`zaki`を作成する

```console
pdbedit -a zaki
```

### 登録済みユーザー一覧

```console
pdbedit -L
```

### firewalld設定

```console
# 使用してるインタフェースがどのゾーンか確認
firewall-cmd --get-active-zones
# 対象ゾーンに許可追加
firewall-cmd --permanent --zone=FedoraServer --add-service=samba
firewall-cmd --reload
```

## HAProxy

### 外部コマンドを使った死活チェック

スクリプトはUPの場合は戻り値を0、DOWNは非0を返すように用意する。
スクリプト実行の際には、第3引数に接続先ホストのアドレス、第4引数にポート番号が渡される。

```
global
  external-check

frontend foobar
  bind *:8080
  default_backend bazqux

backend bazqux
  option external-check
  external-check path "/usr/bin:/bin"
  external-check command /path/to/script.sh
  server host1 192.168.0.121:8080 check
  server host2 192.168.0.122:8080 check
```

### ssl検証無視

```
backend servers
  server host1 192.168.0.68:6443 ssl verify none check
```
