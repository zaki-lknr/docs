# AWS

## CLI

### インストール

[Installing or updating the latest version of the AWS CLI - AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

```console
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

確認

```console
$ aws --version
aws-cli/2.11.15 Python/3.11.3 Linux/4.4.0-19041-Microsoft exe/x86_64.ubuntu.20 prompt/off
```

### 初期設定

- [設定の基本 - AWS Command Line Interface](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-quickstart.html)

```console
aws configure
```

こんな感じ

```console
$ aws configure
AWS Access Key ID [None]: ....
AWS Secret Access Key [None]: ....
Default region name [None]: ap-northeast-1
Default output format [None]: json
```

設定ファイル例は以下(直接ファイルを作っても良い)

```ini
$ cat ~/.aws/credentials 
[default]
aws_access_key_id = ....
aws_secret_access_key = ....
```

### コンテナを使った実行

ECRの認証トークンを得るには以下。

```console
podman run --rm -it -v ~/.aws:/root/.aws public.ecr.aws/aws-cli/aws-cli ecr get-login-password --region ap-northeast-1
```

`aws`を指定しないのがポイント

### 各操作

#### EC2

##### VMリストアップ

```console
aws ec2 describe-instances \
  --region <リージョン>
```

###### インスタンスIDのみ一覧

```console
$ aws ec2 describe-instances --region ap-southeast-2 --query 'Reservations[].Instances[].InstanceId'
[
    "i-002bfe31881b20e07",
    "i-0d33f288e35835170"
]
```

###### Nameタグのみ一覧

```console
$ aws ec2 describe-instances --region ap-southeast-2 --query 'Reservations[].Instances[].Tags[?Key==`Name`].Value[]'
[
    "server-1",
    "server-2"
]
```

##### 指定タグのVM

```console
aws ec2 describe-instances \
  --region <リージョン> \
  --query "Reservations[].Instances[?Tags[?Key=='Name' && Value=='nameタグの名前']]"
```

#### ネットワーク

##### ゲートウェイIDの取得

```console
aws ec2 describe-vpn-gateways \
  --region <リージョン> \
  --query "VpnGateways[?Tags[?Key=='Name' && Value=='nameタグの名前']].VpnGatewayId" \
  --output text
```

##### サイト間VPN接続の設定サンプルダウンロード

[get-vpn-connection-device-sample-configuration — AWS CLI 1.29.35 Command Reference](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-vpn-connection-device-sample-configuration.html)

```console
aws ec2 get-vpn-connection-device-sample-configuration \
  --vpn-connection-id <VPN-ID> \
  --vpn-connection-device-type-id <デバイスタイプID> \
  --internet-key-exchange-version ikev1 \
  --region ${region} \
  --output text > vpn-sample.txt
```

###### デバイスタイプIDの取得

```console
aws ec2 get-vpn-connection-device-types --region ap-southeast-1

{
    "VpnConnectionDeviceTypes": [
        {
            :
            :
        },
        {
            "VpnConnectionDeviceTypeId": "9d9470de",
            "Vendor": "Fortinet",
            "Platform": "Fortigate 40+ Series",
            "Software": "FortiOS 5.0+"
        },
        :
        :
    ]
}
```

上記の「FortiOS 5.0+」のID(`9d9470de`)を変数セットするには`--query`を使って以下の通り。

```console
devicetypeid=$(aws ec2 get-vpn-connection-device-types --region ${region} --query "VpnConnectionDeviceTypes[?Vendor=='Fortinet' && Software=='FortiOS 5.0+'].VpnConnectionDeviceTypeId" --output text)
```

#### S3

##### 一覧

```console
aws s3 ls <backet-name>
```

##### ダウンロード

```console
aws s3 cp s3://<backet-name>/path/to/file /path/to/file
```

## サービス

### EC2 / Amazon Linux

- [Amazon Linux 2 から Amazon Linux 2023 への移行 - AWS Elastic Beanstalk](https://docs.aws.amazon.com/ja_jp/elasticbeanstalk/latest/dg/using-features.migration-al.generic.from-al2.html)
- [よくある質問 - Amazon Linux 2 | AWS](https://aws.amazon.com/jp/amazon-linux-2/faqs/)
- [20240216 Amazon Linux 2 を 2023 に移行してみた - Speaker Deck](https://speakerdeck.com/masaruogura/20240216-amazon-linux-2-wo-2023-niyi-xing-sitemita)
