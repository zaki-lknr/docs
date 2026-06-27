# Oracle Cloud

## VM(Ubuntu)のfirewall設定

クラウドリソースのセキュリティグループ以外に(UWFでなく)OSの`iptables`も有効になってるため穴あけが必要。  
永続設定は`/etc/iptables/rules.v4`に保存する。

<https://docs.oracle.com/ja-jp/iaas/Content/Compute/known-issues.htm#ufw>
