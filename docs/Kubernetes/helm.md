# Helm

## インストール

[Helm | Helm のインストール](https://helm.sh/ja/docs/intro/install/)

OSのパッケージ管理で対応してなければ、バイナリを`/usr/local/bin/helm`に置けばOK.  
インストール用スクリプトもある。

aptはあるけど、今のとこyun/dnfはないんだよな。

### apt

```console
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

## コマンド

### リポジトリ一覧

```console
$ helm repo list
NAME                    URL                                               
prometheus-community    https://prometheus-community.github.io/helm-charts
rook-release            https://charts.rook.io/release                    
stable                  https://charts.helm.sh/stable                     
grafana                 https://grafana.github.io/helm-charts             
elastic                 https://helm.elastic.co                           
metallb                 https://metallb.github.io/metallb   
```

### リポジトリを追加

```console
$ helm repo add stable https://charts.helm.sh/stable
```

とか

```console
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

とか

### ローカルのリポジトリ情報を更新

多分`apt-get update`的な動作。

```console
$ helm repo update
```

### チャート一覧

登録済み全リポジトリのチャート一覧

```console
$ helm search repo
```

指定リポジトリのチャート一覧

```console
$ helm search repo <repository-name>
```

### チャートの検索

```console
$ helm search repo prometheus
NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION                                       
prometheus-community/kube-prometheus-stack              16.12.1         0.48.1          kube-prometheus-stack collects Kubernetes manif...
prometheus-community/prometheus                         14.3.0          2.26.0          Prometheus is a monitoring system and time seri...
prometheus-community/prometheus-adapter                 2.14.2          v0.8.4          A Helm chart for k8s prometheus adapter           
prometheus-community/prometheus-blackbox-exporter       4.15.0          0.19.0          Prometheus Blackbox Exporter                      
prometheus-community/prometheus-cloudwatch-expo...      0.16.0          0.10.0          A Helm chart for prometheus cloudwatch-exporter   
:
:
```

### チャートのデフォルト値

```console
$ helm show values metallb/metallb
# Default values for metallb.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

:
:
```

これをファイル出力すればインストール用のカスタマイズYAML作れる

### リリース一覧

実行中のリリースの一覧

```console
$ helm list [-n <namespace-name>]
```

### リリースの設定値

チャートからリリースされているアプリケーションの設定値を`helm get values`で確認できる。

```console
[zaki@fedora-node ~]$ helm ls -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
grafana         monitoring      1               2021-07-26 16:23:47.276215637 +0900 JST deployed        grafana-6.14.1          8.0.5      
prometheus      monitoring      1               2021-07-26 14:24:12.737306448 +0900 JST deployed        prometheus-14.4.1       2.26.0     
[zaki@fedora-node ~]$ helm get values -n monitoring grafana
USER-SUPPLIED VALUES:
persistence:
  accessModes:
  - ReadWriteOnce
  enabled: true
  size: 1Gi
  type: pvc
service:
  type: LoadBalancer
```

## チャートインストール例

### Fluent Bit

[fluent/helm-charts: Helm Charts for Fluentd and Fluent Bit](https://github.com/fluent/helm-charts)

リポジトリ追加

```console
helm repo add fluent https://fluent.github.io/helm-charts
```

インストール

```console
helm upgrade --install fluent-bit fluent/fluent-bit -n logging --create-namespace -f values.yaml
```

## 環境変数

Helmで使用する環境編素は`helm env`で確認できる。

```console
$ helm env
HELM_BIN="helm"
HELM_CACHE_HOME="/home/zaki/.cache/helm"
HELM_CONFIG_HOME="/home/zaki/.config/helm"
HELM_DATA_HOME="/home/zaki/.local/share/helm"
HELM_DEBUG="false"
HELM_KUBEAPISERVER=""
HELM_KUBEASGROUPS=""
HELM_KUBEASUSER=""
HELM_KUBECAFILE=""
HELM_KUBECONTEXT=""
HELM_KUBETOKEN=""
HELM_MAX_HISTORY="10"
HELM_NAMESPACE="default"
HELM_PLUGINS="/home/zaki/.local/share/helm/plugins"
HELM_REGISTRY_CONFIG="/home/zaki/.config/helm/registry.json"
HELM_REPOSITORY_CACHE="/home/zaki/.cache/helm/repository"
HELM_REPOSITORY_CONFIG="/home/zaki/.config/helm/repositories.yaml"
```

`HELM_CONFIG_HOME`を設定すれば、`HELM_REGISTRY_CONFIG`などは連動する。

```
$ HELM_CONFIG_HOME=/var/tmp helm env | grep var
HELM_CONFIG_HOME="/var/tmp"
HELM_REGISTRY_CONFIG="/var/tmp/registry.json"
HELM_REPOSITORY_CONFIG="/var/tmp/repositories.yaml"
```
