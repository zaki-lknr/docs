# Prometheus

## インストール

### Docker

暫定でデフォルト(サンプル)設定なら以下で起動する

```console
docker run -d -p 9090:9090 quay.io/prometheus/prometheus:v3.2.1
```

## 設定

デフォルト(サンプル)は以下

[prometheus/documentation/examples/prometheus.yml at main · prometheus/prometheus](https://github.com/prometheus/prometheus/blob/main/documentation/examples/prometheus.yml)
