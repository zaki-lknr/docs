# Azure CLI

## 結果のquery

[Azure CLI での JMESPath クエリ コマンドの結果 | Microsoft Learn](https://learn.microsoft.com/ja-jp/cli/azure/query-azure-cli?tabs=concepts%2Cbash)

たとえば、「VNetの一覧からリソースグループ名とVNet名が指定のもののリソースIDを取得」であれば以下

```console
az network vnet list --query "[?name=='${vnet_name}' && resourceGroup=='${resource_group}'].id"
```

## network

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
