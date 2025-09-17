# Azure CLI

## extension

### 一覧

```console
az extension list
```

### インストール済み拡張の更新

`aks-preview`を更新する

```console
az extension update --name aks-preview
```

## 結果のquery

[Azure CLI での JMESPath クエリ コマンドの結果 | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/query-azure-cli?tabs=concepts%2Cbash)

### 辞書型データのリストから指定キーの値一覧

AKSのクラスター名一覧を取得

```console
az aks list [-g resource-group] --query '[].name'
```

### 辞書型データのリストから指定キーが特定の値のものを検索(and)

VNetの一覧からリソースグループ名とVNet名が指定のもののリソースIDを取得

```console
az network vnet list --query "[?name=='${vnet_name}' && resourceGroup=='${resource_group}'].id"
```

#### 文字列を含む(部分一致)検索

`name`キーに`dev`を含むデータの検索

```console
az aks list --query "[?contains(name,'dev')]"
```

オブジェクト全体でなくここからさらにnameのみの一覧を出力するなら以下。

```console
az aks list --query "[?contains(name,'dev')].name"
```

## アカウント

### アカウント情報

```console
az account list
```

### テナントID一覧

```console
az account tenant list
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

#### ルール削除

```console
az network nsg rule delete \
  --nsg-name ${nsg_name} \
  --resource-group ${resoure_group} \
  --name ${nsg_rule_name} \
  --no-wait
```

<https://learn.microsoft.com/ja-jp/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-delete>

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

#### Aレコード削除

```console
az network private-dns record-set a delete \
  --resoure-group ${resource_group} \
  --zone-name "private.example.org" \
  --name hostname
```

y/nを聞かれるが、`-y`を付与すれば強制削除

#### Aレコード一覧

```console
az network private-dns record-set a list \
  --resoure-group ${resource_group} \
  --zone-name "private.example.org"
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

### Application Gateway

#### SKUをStandard v2からWAF v2に変更する

[Azure Application Gateway WAF ポリシーにアップグレードする | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/web-application-firewall/ag/upgrade-ag-waf-policy?tabs=portal)

基本構文は`az network application-gateway update --resource-group ${rg} --name ${appgw} --sku WAF_v2`だが、これだけだとエラーになる。
「WAF v2に変更」と「WAFポリシーの設定」を同時に行う必要がある。

```console
# WAFポリシーの作成
az network application-gateway waf-policy create \
  --name ${waf_policy_name} \
  --resource-group ${resource_group} \
  --version 3.2

# WAFポリシー設定
az network application-gateway waf-policy policy-setting update \
  --policy-name ${waf_policy_name} \
  --resource-group ${resource_group} \
  --request-body-check ${check} \
  --mode ${waf_mode} \
  --state ${waf_state}

# WAFのSKUをWAF_v2に更新
waf_id=$(az network application-gateway waf-policy show -g ${resource_group} -n ${waf_policy_name} --query id -o tsv)
az network application-gateway update \
  --name ${appgw_name} \
  --resource-group ${resouce_group} \
  --sku WAF_v2 \
  --set sku.tier=WAF_v2 \
  --set firewallPolicy.id=${waf_id}
```

## storage account

### ファイル共有

[az storage file | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/file?view=azure-cli-latest)

#### ファイル一覧

[az storage file | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-list)

```console
az storage file list \
  --account-name ${account_name} \
  --account-key ${account_key} \
  --share-name ${share_name}
```

#### ファイルダウンロード

[az storage file | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download)

```console
az storage file download \
  --path ${shared_file_path} \
  --dest ${output_dir_name} \
  --share-name ${share_name} \
  --account-name ${account_name} \
  --account-key ${account_key}
```

`dest`はドキュメントには「パス」と書かれているが、実際に動かすとディレクトリ名になる。  
(`destのディレクトリ名`/`元のファイル名`で保存される)

#### ファイルBlobのダウンロード

[az storage blob | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-download)

```console
az storage blob download \
  --container-name ${container_name} \
  --name ${blob_filepath} \
  --file ${output_dir_name} \
  --account-name ${storage_account} \
  --account-key ${account_key}
```

`container_name`はコンテナーの名称、`blob_filepath`はコンテナー内におけるファイルのパス、`output_dir_name`は出力先(ダウンロード先のファイル名)

### セキュリティ

[Azure Storage ファイアウォールおよび仮想ネットワークを構成する | Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/storage/common/storage-network-security?tabs=azure-cli)

#### トラフィックのデフォルト拒否設定

```console
az storage account update \
  --resource-group ${resource_group} \
  --name ${storage_account_name} \
  --default-action Deny
```

#### VNET/サブネットを追加

```console
az storage account network-rule add \
  --resource-group ${resource_group} \
  --account-name ${storage_account_name} \
  --subnet ${subnet_id}
```

ただしこれを実行するにはサブネット側エンドポイントのポリシー設定が必要

```console
az network vnet subnet update \
  --name ${subnet} \
  --vnet-name ${vnet} \
  --resource-group ${resource_group} \
  --service-endpoints Microsoft.Storage
```

#### 許可IPアドレスの追加

```console
az storage account network-rule add \
  --resource-group ${resource_group} \
  --account-name ${storage_account_name} \
  --ip-address ${ip_address}
```

### キーの取得

[az storage account keys | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/storage/account/keys?view=azure-cli-latest)

```console
az storage account keys list \
  --resource-group ${resource_group} \
  --account-name ${storage_account_name}
```

2つヒットするはずなので、`--query '[0].value' -o tsv` とかやれば取り出せる。

## AKS

### クラスター一覧

```console
az aks list [-g <resource-group>]
```

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

### ノードプールの詳細

```console
az aks nodepool show -g ${resource_group} --cluster-name ${cluster_name} -n ${nodepool_name}
```

### ノードのスケーリング

```console
az aks scale \
  --resoure-group ${resource_group} \
  --name ${nodepool_name} \
  --nodepool-name ${nodepool_name} \
  --node-count 3
```
