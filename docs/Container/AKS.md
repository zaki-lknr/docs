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

## ログとイベントを記録する

監視->分析情報で、Container insightsを有効（収集データに「ログとイベント」を選択）にする。  
これでLog Analyticsワークスペースの画面から収集情報を検索できる。

## ストレージの拡張

defaultのAzure DiskのStorage Class定義の内容は`allowVolumeExpansion`が`true`になっており、拡張に対応していることが確認できる。  
拡張するにはPVCの定義を変更する。

以下コマンドで10Giサイズに拡張する。

```console
kubectl patch -n namespace pvc pvc-resource -p '{"spec":{"resources":{"requests":{"storage": "10Gi"}}}}'
```

縮小は不可
