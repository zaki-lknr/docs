# Helm

## インストール

[Helm | Helm のインストール](https://helm.sh/ja/docs/intro/install/)

OSのパッケージ管理で対応してなければ、バイナリを`/usr/local/bin/helm`に置けばOK.  
インストール用スクリプトもある。

aptはあるけど、今のとこyun/dnfはないんだよな。

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
