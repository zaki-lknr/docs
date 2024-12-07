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

### 温度

```console
zaki@rasp4-01:~ $ vcgencmd measure_temp
temp=44.3'C
```
