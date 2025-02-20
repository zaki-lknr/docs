# Amazon Linux

## AL2023のdnf update

`dnf update`だけではパッケージ更新されない。

> ディストリビューションは Amazon Linux パッケージリポジトリの特定のバージョンにロックされるため、更新を適用する方法とタイミングを制御できます。デフォルトでは、Amazon Linux 2 とは異なり、dnf update コマンドはインストール済みのパッケージを更新しません
>
> [Amazon Linux 2023 – 長期サポート付きのクラウド最適化 Linux ディストリビューション | Amazon Web Services ブログ](https://aws.amazon.com/jp/blogs/news/amazon-linux-2023-a-cloud-optimized-linux-distribution-with-long-term-support/)

更新するには以下のように`--releasever`でリポジトリバージョンを指定する。

```console
dnf upgrade --releasever=2023.6.20250211
```

リリースバージョンは下記コマンドで確認。

```console
dnf check-release-update
```

`latest`指定で最新が対象になる。

```console
dnf upgrade --releasever=latest
```
