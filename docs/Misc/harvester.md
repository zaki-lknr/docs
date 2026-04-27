# Harvester

## インストール

- [Quick start](https://github.com/harvester/harvester#quick-start)
- [Deploy a High-Availability Cluster | Harvester](https://docs.harvesterhci.io/v1.7/getting-started/deploy-ha-cluster)
- [Deploy a Single-Node Cluster | Harvester](https://docs.harvesterhci.io/v1.7/getting-started/deploy-singlenode-cluster)

## ホストと同じネットワークにVMをデプロイ

Virtual Machine Networksを「UntaggedNetwork」で作成してVMに設定する

## ノードのシェルを起動

コンソール画面で`F12`押下

## ノードへのssh

```console
ssh rancher@<harvester-host>
```

ログイン後、`sudo su -`で昇格できる。
