# AWS CLI

## インストール

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

## 初期設定

```console
aws configure
```

## ネットワーク

### ゲートウェイIDの取得

```console
aws ec2 describe-vpn-gateways \
  --region <リージョン> \
  --query "VpnGateways[?Tags[?Key=='Name' && Value=='nameタグの名前']].VpnGatewayId" \
  --output text
```

### サイト間VPN接続の設定サンプルダウンロード

```console
aws ec2 get-vpn-connection-device-sample-configuration \
  --vpn-connection-id <VPN-ID> \
  --vpn-connection-device-type-id <デバイスタイプID> \
  --internet-key-exchange-version ikev1 \
  --region ${region} \
  --output text > vpn-sample.txt
```

#### デバイスタイプIDの取得

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
