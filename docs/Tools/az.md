# Azure CLI

## 結果のquery

[Azure CLI での JMESPath クエリ コマンドの結果 | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/query-azure-cli?tabs=concepts%2Cbash)

たとえば、「VNetの一覧からリソースグループ名とVNet名が指定のもののリソースIDを取得」であれば以下

```console
az network vnet list --query "[?name=='${vnet_name}' && resourceGroup=='${resource_group}'].id"
```

## network

### VNet

[az network vnet | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/network/vnet?view=azure-cli-latest)

#### VNetID取得

```console
az network vnet show \
  --name ${vnet_name} \
  --resource-group ${resource_group} \
  --query id
```

#### VNet作成

VNetを単体で作成する

```console
az network vnet create \
  --resource-group ${resource_group} \
  --name ${vnet_name} \
  --address-prefixes 192.168.0.0/16
```

VNetとサブネットを同時に作成

```console
az network vnet create \
  --resource-group ${resource_group} \
  --name ${vnet_name} \
  --address-prefixes 192.168.0.0/16 \
  --subnet-name ${subnet_name} \
  --subnet-prefixes 102.168.10.0/24 \
  --network-security-group ${nsg_name}
```

### サブネット

[az network vnet subnet | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/network/vnet/subnet?view=azure-cli-latest)

#### サブネットID取得

```console
az network vnet subnet show \
  --vnet-name ${vnet_name} \
  --resource-group ${resource_group} \
  --name ${subnet_name} \
  --query id
```

#### サブネット作成

VNetのアドレス範囲であることは必須

```console
az network vnet subnet create \
  --name ${subnet_name} \
  --vnet-name ${vnet_name} \
  --resource-group ${resource_group} \
  --network-security-group ${nsg_name} \
  --address-prefixes 192.168.10.0/24
```

#### サービスエンドポイント一覧

```console
az network vnet list-endpoint-services
```

### ネットワークセキュリティグループ

#### NSG作成

NSGの箱を作る

[az network nsg | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/network/nsg?view=azure-cli-latest)

```console
az network nsg create \
  --name ${nsg_name} \
  --resource-group ${resource_group}
```

#### ルール作成

[az network nsg rule | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/network/nsg/rule?view=azure-cli-latest)

```console
az network nsg rule create \
  --nsg-name ${nsg_name} \
  --resource-group ${resoure_group} \
  --name ${nsg_rule_name} \
  --access Allow \
  --priority 256 \
  --source-address-prefixes ${src_address_prefixes} \
  --source-port-ranges ${src_port_ranges} \
  --destination-address-prefixes ${dst_address_prefixes} \
  --destination-port-ranges ${dst_port_ranges} \
  --protocol ${protocol}
```

| 値                    | 設定例                                                 |
| -------------------- | --------------------------------------------------- |
| `*-address-prefixes` | IPアドレス(`192.168.0.0/24`など)や`VirtualNetwork`などサービスタグ |
| `*-port-ranges`      | ポート番号(値のみ、範囲指定`1024-4096`など)、`*`                    |

## storage account

### セキュリティ

[Azure Storage ファイアウォールおよび仮想ネットワークを構成する | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/storage/common/storage-network-security?tabs=azure-cli)

### キーの取得

[az storage account keys | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/account/keys?view=azure-cli-latest)

```console
az storage account keys list \
  --resoure-group ${resource_group} \
  --account-name ${storage_account_name}
```

2つヒットするはずなので、`--query '[0].value' -o tsv` とかやれば取り出せる。
