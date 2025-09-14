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

### リポジトリ削除

追加したときのリポジトリ名(`helm repo list`で確認可能)を指定して`remove`する。

```console
$ helm repo list
NAME            URL
hashicorp       https://helm.releases.hashicorp.com
fluent          https://fluent.github.io/helm-charts
stable          https://charts.helm.sh/stable
elastic         https://helm.elastic.co
awx-operator    https://ansible-community.github.io/awx-operator-helm/
gitlab          https://charts.gitlab.io/
$ helm repo remove stable
"stable" has been removed from your repositories
```

### ローカルのリポジトリ情報を更新

```console
helm repo update
```

特定のリポジトリのみ更新するには引数を追加する。

```console
helm repo update prometheus-community
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

### チャートのバージョン一覧

チャート検索でチャート名を付与しつつ`-l`を追加

```console
$ helm search repo fluent/fluentd -l
NAME            CHART VERSION   APP VERSION     DESCRIPTION                
fluent/fluentd  0.3.9           v1.14.6         A Helm chart for Kubernetes
fluent/fluentd  0.3.8           v1.12.4         A Helm chart for Kubernetes
fluent/fluentd  0.3.7           v1.12.4         A Helm chart for Kubernetes
fluent/fluentd  0.3.6           v1.12.4         A Helm chart for Kubernetes
fluent/fluentd  0.3.5           v1.12.4         A Helm chart for Kubernetes
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

### チャートのインストール

```console
helm install my-app repo/chart -n namespace -f values.yaml
```

```console
helm install stable/mysql --generate-name --create-namespace -n mysql -f values.yaml
```

### アップグレード

```console
helm upgrade <release> <chart> [-n <namespace>] [-f values.yaml]
```

未インストールであればインストールし、インストール済みであればアップグレードするには`--install`を付加

```console
helm upgrade --install sample-logstash elastic/logstash -f values.yaml --create-namespace -n logging
```

### チャートインストール時のコマンドライン引数でパラメタ指定(--set)

values.yaml以外にコマンドラインオプションからもパラメタ指定できる。

```console
helm upgrade ... --set key=value --set config.server.port=8443
```

### チャートのバージョン指定インストール

インストール時にチャートのバージョンを指定するには`--version`を使う

```console
helm install ... --version x.y.z
```

### チャートのダウンロード(tgz形式)

```console
helm pull fluent/fluent-bit --version 0.43.0
```

### チャートインストールに使われるマニフェスト生成

`install`の代わりに`template`を使う

```console
helm template stable/mysql --generate-name --create-namespace -n mysql
```

※ heml 3.14だと`--create-namespace`が効かない

### リリース一覧

実行中のリリースの一覧

```console
$ helm list [-n <namespace-name>]
```

### リリースの削除

```console
helm uninstall <char-name> [-n <namespace-name>]
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

## チャート作成

### テンプレート作成

```console
helm create mychart
```

以下のファイルが生成される。不要なファイルは削除して作りこめばOK

```console
$ find mychart/
mychart/
mychart/Chart.yaml
mychart/values.yaml
mychart/.helmignore
mychart/templates
mychart/templates/ingress.yaml
mychart/templates/deployment.yaml
mychart/templates/service.yaml
mychart/templates/serviceaccount.yaml
mychart/templates/hpa.yaml
mychart/templates/NOTES.txt
mychart/templates/_helpers.tpl
mychart/templates/tests
mychart/templates/tests/test-connection.yaml
mychart/charts
```

### 変数

| 変数                 | 内容                  |
| ------------------ | ------------------- |
| .Release.Name      | リリース名               |
| .Release.Namespace | デプロイ先ネームスペース        |
| .Values.*          | values.yamlファイル定義変数 |

## プラグイン

### 一覧

```console
$ helm plugin ls
NAME    VERSION DESCRIPTION                           
diff    3.12.3  Preview helm upgrade changes as a diff
```

### diff

install

```console
helm plugin install https://github.com/databus23/helm-diff
```

更新時のdiffを確認するには、`helm diff upgrade`に実際のupgrade時のすべての引数を指定する。

```console
helm diff upgrade ...
```
