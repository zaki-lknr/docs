# Raspberry Pi

## ログ

### rsyslog

デフォルトでは入ってないので`/var/log/syslog`は無い。  
`sudo apt-get install rsyslog`すればログファイルに出力されるようになる。

rsyslogが無くても`journalctl`でログは確認できる。

### 状態取得

#### 温度

```console
zaki@rasp4-01:~ $ vcgencmd measure_temp
temp=44.3'C
```
