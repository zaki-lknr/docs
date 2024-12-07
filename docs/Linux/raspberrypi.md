# Raspberry Pi

## ログ

### rsyslog

デフォルトでは入ってないので`/var/log/syslog`は無い。  
`sudo apt-get install rsyslog`すればログファイルに出力されるようになる。

rsyslogが無くても`journalctl`でログは確認できる。

## 状態取得

### 本体モデル名の確認

```console
zaki@rasp5-01:~ $ cat /proc/device-tree/model 
Raspberry Pi 5 Model B Rev 1.0
```

### OS確認

`lsb_release -a`を実行

```console
zaki@rasp4-01:~ $ lsb_release -a
No LSB modules are available.
Distributor ID: Raspbian
Description:    Raspbian GNU/Linux 11 (bullseye)
Release:        11
Codename:       bullseye
```

```console
zaki@rasp5-01:~ $ lsb_release -a
No LSB modules are available.
Distributor ID: Debian
Description:    Debian GNU/Linux 12 (bookworm)
Release:        12
Codename:       bookworm
```

### 温度

```console
zaki@rasp4-01:~ $ vcgencmd measure_temp
temp=44.3'C
```
