# Azure

## 各サービス概要

- [Azure Communication Services とは | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/communication-services/overview)
    - [Azure Communication Services メールの概要 - An Azure Communication Services concept article | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/communication-services/concepts/email/email-overview)
    - [クイック スタート - Azure Communication Services を使用して電子メールを送信する方法 - An Azure Communication Services Quickstart | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/communication-services/quickstarts/email/send-email?tabs=windows%2Cconnection-string%2Csend-email-and-get-status-async%2Csync-client&pivots=platform-azportal)
    - [クイック スタート: Azure Communication Services でメール通信サービスのリソースを作成して管理する - An Azure Communication Services quickstart | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/communication-services/quickstarts/email/create-email-communication-resource?pivots=platform-azp)
- [Azure のサーバーレス コンテナー - Azure Container Instances | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/container-instances/container-instances-overview)

## Cloud Shell

[Azure Cloud Shell に関してよくあるご質問 (FAQ) | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/cloud-shell/faq-troubleshooting)

### ストレージアカウントの確認

環境変数にセットされている。

```console
echo $ACC_STORAGE_PROFILE
```

## AKS

### 監視設定

- [Azure Kubernetes Service (AKS) クラスターの監視を有効にする - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/azure-monitor/containers/kubernetes-monitoring-enable?tabs=cli)
- [Kubernetes クラスターの監視を無効にする - Azure Monitor | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/azure-monitor/containers/kubernetes-monitoring-disable)

## Monitor / Log Analytics

### AKS/Monitor

#### 収集するコンテナーログの設定変更

AKSクラスターのメニューの「監視」から、上部の「モニターの設定」押下。  
これでデプロイ時に選択するPrometheus/Grafanaやコンテナーログの設定画面が表示される。  
収集頻度や対象ネームスペースなどをここから選択。

### クエリ

#### 使用可能なCounterNameの一覧

```kusto
Perf
| where ObjectName == "K8SContainer"
| summarize count() by CounterName
```

CounterNameの一覧とそれぞれの件数が出力される
