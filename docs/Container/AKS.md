# AKS

## FAQ

[Azure Kubernetes Service (AKS) についてよく寄せられる質問 - Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/aks/faq)

## ノードに接続する

[Azure Kubernetes Service (AKS) クラスター ノードへの接続 - Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/aks/node-access)

```console
# ノードを確認
kubectl get node -o wide
# 特権コンテナを起動し接続
kubectl debug node/aks-******** -it --image=mcr.microsoft.com/dotnet/runtime-deps:6.0
# 特権コンテナのシェルでchroot
chroot /host
```

これでノードOSのルートファイルシステムにアクセスできる。  
終了後はCompletedになっている特権コンテナのPodを削除する

```console
# 特権コンテナを削除
kubectl delete pod node-debugger-aks-********
```
