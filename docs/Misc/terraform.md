# Terraform

## インストール

[Install Terraform | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/install-cli)

### apt

```console
$ sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install terraform
```

## CLI

### フォーマッタ

standard styleへ変換する。

```console
terraform fmt -recursive
```

`-recursive`無しの場合はカレントディレクトリのみが対象

### providerバージョン確認

```console
terraform providers -version
```

といわれてるけど`providers`が無くても出力は同じ。

### providerバージョンアップグレード

tfファイルのprovicersの定義で指定バージョンを変更して、以下コマンド実行。

```console
terraform init -upgrade
```

### リソース一覧

```console
terraform state list
```

### workspace

#### 現在のworkspace

```console
$ terraform workspace show
default
```

#### 一覧

```console
$ terraform workspace list
  default
* 1
  2
```

#### 変更

workspace `2` に変更

```console
$ terraform workspace select 2
Switched to workspace "2".
```

設定値は`.terraform/environment`に記録される。

### variable

#### 引数

```console
terraform plan -var='enable_wan=false'
```

### デバッグログ

verboseオプション的なものはなく、変数指定する。

```console
TF_LOG=DEBUG terraform command
```

[Debugging | Terraform | HashiCorp Developer](https://developer.hashicorp.com/terraform/internals/debugging)

### 指定モジュールのみ処理

`-target=module.モジュール名`で指定可能

```console
terraform plan -target=module.my-module
```

## backend設定

### S3

backendの定義を追加

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }

  backend "s3" {
    bucket = "bucket-name"
    key    = "terraform.tfstate"
    region = "ap-northeast-1"
  }
}

provider "aws" {
  region  = "ap-northeast-1"
}
```

もともとローカルでtfstate作っていた状態からS3へ移行する場合は、

1. S3バケット作成
2. tfstateをS3アップロード
3. ソースに`backend`定義追加 (バケット名・キー(ファイル)名・リージョン指定)

ここまではセットで。  
で、

4. ローカルのtfstateを退避
5. `terraform init`実行 (S3設定が反映される)
6. `terraform plan`で差分がないことを確認

でいける。

### S3 + DynamoDBでのロック

DynamoDBでテーブルを作成

- 名前は任意
- パーティションキーは`LockID`
- その他はデフォルト

あとはバックエンド設定に以下追加

```terraform
  backend "s3" {
    bucket         = S3バケット名
    key            = "terraform.tfstate"
    region         = リージョン
    dynamodb_table = DynamoDBテーブル名
    encrypt        = true
  }
```

### ロックしたままになった場合の解除

処理中の強制中断や、実行ホストのシャットダウンなどで、ロック状態のままになると、次のTerraform実行はできなくなる。  
解除するには `terraform force-unlock ID` を実行する。

IDはエラーメッセージ中の以下の値

```console
Error message: operation error DynamoDB: PutItem, https response error
StatusCode: 400, RequestID: ....
ConditionalCheckFailedException: The conditional request failed
Lock Info:
  ID:        ********
  Path:      /path/to/terraform.tfstate
  Operation: OperationTypeApply
  Who:       ...
  Version:   1.9.1
  Created:   ...
  Info:
```

## 構文

### count

EC2を3つ立てNameタグに連番を振る

```terraform
resource "aws_instance" "cloud-vm" {
  count           = 3
  instance_type   = "t2.medium"
  :

  tags = {
    Name = "cloud-${count.index}"
  }
}
```

EIPの付与と連動させるには、[element function](https://developer.hashicorp.com/terraform/language/functions/element)を使って要素を参照する。

```terraform
resource "aws_eip" "cloud-eip" {
  count      = 3
  instance   = "${element(aws_instance.cloud-vm.*.id, count.index)}"
  depends_on = [ aws_internet_gateway.igw ]

}
```

### 変数

#### 変数参照

`vm_ami_id`という変数を定義しているなら以下。

```terraform
ami = var.vm_ami_id
```

#### 補完

文字列リテラルの中で参照する場合は「補完」で書式は以下。

```terraform
tags = {
  Name = "${var.name_prefix}-server"
}
```

### テンプレート

#### エスケープ

`%{...}`という文字をそのまま使いたい場合は`%%{...}`

```
curl -k https://localhost:6443 -w '%%{http_code}\n' -sS -o /dev/null 2>/dev/null
```

### 関数

#### file

[file - Functions - Configuration Language | Terraform | HashiCorp Developer](https://developer.hashicorp.com/terraform/language/functions/file)

引数のファイルの内容を取り込む。  
EC2のユーザーデータ/Cloud-initのファイルや、ポリシーのJSON等を外部ファイルに定義できる。

```tf
resource "aws_instance" "dev-vm" {
  ami                         = ...
  instance_type               = "t2.medium"
  key_name                    = aws_key_pair.key.id
  subnet_id                   = aws_subnet.subnet.id
  vpc_security_group_ids      = [aws_security_group.sg.id]
  associate_public_ip_address = true

  user_data = file("${path.module}/cloud-config.yaml")

  ...
}
```

```yaml
#cloud-config

chpasswd: { expire: false }
ssh_pwauth: true

packages:
- python3.12
```

#### templatefile

`file()`は外部ファイルを固定値として読み込むのみだが、`templatefile()`ならテンプレートとして変数を展開する。

```tf
  user_data = templatefile("${path.module}/user-data.sh", {
    git_username = var.git_username
    git_password = var.git_password
  })
```

user-data.sh

```sh
#!/bin/bash -ex

git clone https://${git_username}:${git_password}@git.example.org/repos/apps /root/apps
```

## resource

### デフォルトのルートテーブル

VPCのパラメタの`default_route_table_id`を参照する。

```terraform
route_table_ids = [aws_vpc.my_vpc.default_route_table_id]
```

## docs

[terraform-docs](https://terraform-docs.io/)  
Terraformコードからドキュメント生成するツール。

CLIをインストールできるがコンテナ実行でも生成できる。  
Podmanであれば、Terraformコードのあるディレクトリで以下を実行すれば`README.md`が生成される。

```console
podman run --rm -v "$(pwd):/terraform-docs" quay.io/terraform-docs/terraform-docs:0.18.0 markdown /terraform-docs --output-file README.md
```
