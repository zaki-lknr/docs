# Raspberry Pi

## ログ

### rsyslog

デフォルトでは入ってないので`/var/log/syslog`は無い。  
`sudo apt-get install rsyslog`すればログファイルに出力されるようになる。

rsyslogが無くても`journalctl`でログは確認できる。
