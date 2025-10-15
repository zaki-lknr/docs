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

## Exporter

### Node Exporter

[prometheus/node_exporter: Exporter for machine metrics](https://github.com/prometheus/node_exporter)

これもDockerでデプロイできる

```console
docker run -d \
  --name=node_exporter \
  --publish 9100:9100 \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  --restart=always \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```

このNode ExporterをPrometheusから監視するには以下の設定

```yaml
  - job_name: 'node'
    static_configs:
    - targets: ['192.168.0.16:9100']
```
