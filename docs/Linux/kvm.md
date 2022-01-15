# KVM

## 一覧

```console
$ virsh list --all
```

## VMの起動

```console
$ virsh start <domain-name>
```

## 実行中VMへコンソール接続

```console
$ virsh console <domain-name>
```

VM(domain)名は`list`で確認する。

## 停止

シャットダウンするには`shutdown`する。

```console
$ virsh shutdown <domain-name>
```

強制停止は`destroy`する。

```console
$ virsh destroy <domain-name>
```

## 自動起動

有効化

```console
$ virsh autostart <domain-name>
```

無効化

```console
$ virsh autostart --disable <domain-name>
```

確認は`virsh dominfo <domain-name>`かな？
