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

### providerバージョン確認

```console
terraform providers -version
```

といわれてるけど`providers`が無くても出力は同じ。

### リソース一覧

```console
terraform state list
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
