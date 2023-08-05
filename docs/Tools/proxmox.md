# Proxmox (PVE)

## VM定義ファイル関連

```console
root@pve:~# ls -F /etc/pve/nodes/pve/qemu-server
100.conf  102.conf  202.conf  301.conf  303.conf
101.conf  201.conf  203.conf  302.conf
```

中身は

```console
root@pve:~# cat /etc/pve/nodes/pve/qemu-server/301.conf 
boot: order=scsi0
cores: 2
memory: 4096
name: k8su-master
net0: e1000=CA:0D:8C:5E:46:C3,bridge=vmbr0,firewall=1
scsi0: wdblue1000gb:vm-301-disk-0
scsihw: pvscsi
smbios1: uuid=b9b7f350-0b74-46df-bd59-0318fc99372c
vmgenid: 5e1f1e65-0eb0-4cd1-867c-277b48d91616
```

実データはこれかな？

```console
root@pve:/# ls -l /dev/wdblue1000gb/vm-301-disk-0 
lrwxrwxrwx 1 root root 8 Aug  5 10:13 /dev/wdblue1000gb/vm-301-disk-0 -> ../dm-15
root@pve:/# ls -l /dev/dm-15
brw-rw---- 1 root disk 253, 15 Aug  5 10:13 /dev/dm-15
root@pve:/# file /dev/dm-15
/dev/dm-15: block special (253/15)
```
