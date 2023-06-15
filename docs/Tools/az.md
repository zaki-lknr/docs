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

### public ip

#### アドレス一覧

```console
az network public-ip list \
  --resoure-group ${resource_group}
```

必要なアドレスは`--query`で取り出す。

```console
az network public-ip list \
  --resoure-group ${resource_group} \
  --query "[?id=='$publicip_id'].ipAddress" \
  --o tsv
```

### DNS

#### レコード作成

```console
az network dns record-set a add-record \
  --resoure-group ${resource_group} \
  --zone-name "example.org" \
  --ipv4-address ${publicip_address} \
  --record-set-name ${recordset_name}
```

### Private DNS

#### ゾーン作成

```console
az network private-dns zone create \
  --resoure-group ${resource_group} \
  --name "private.example.org"
```

#### Aレコード作成

```console
az network private-dns record-set a add-record \
  --resoure-group ${resource_group} \
  --zone-name "private.example.org"
  --ipv4-adderss ${ipaddr} \
  --record-set-name hostname
```

#### 仮想ネットワークとのリンク作成

ゾーンと仮想ネットワークが同じリソースグループであれば、`--virtual-network`は仮想ネットワーク名だけで良い。

```console
az network private-dns link vnet create \
  --zone "private.example.org" \
  --resource-group ${resource_group} \
  --virtual-network ${vnet_name} \
  --name ${link_name}
```

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

## AKS

### 使用可能バージョンの確認

```console
az aks get-versions --query 'orchestrators[].orchestratorVersion'
```

ロケールを指定するには以下

```console
az aks get-versions -l eastus --query 'orchestrators[].orchestratorVersion'
```

### KUBECONFIGの取得

```console
az aks get-credentials \
  --resoure-group ${resource_group} \
  --name ${cluster_name}
```

`--overwrite-existing`を付与すると確認無しで上書きされる。

### ノードプールの追加

[az aks nodepool | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add)

```console
az aks nodepool add \
  --resoure-group ${resource_group} \
  --name ${nodepool_name} \
  --cluster-name ${cluster_name} \
  --kubernetes-version ${k8s_version} \
  --mode User \
  --os-type Linux \
  --vnet-subnet-id ${vnet_subnet_id} \
  --node-vm-size Standard_B2s \
  --node-count 2 \
  --max-pods 32
```
