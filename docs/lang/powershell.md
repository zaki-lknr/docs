# PowerShell

## 実行初期設定

```text
このシステムではスクリプトの実行が無効になっているため、ファイル C:\path\to\foobar.ps1 を読み込むことができません。詳細については、「about_Execution_Policies」(https://go.microsoft.c
om/fwlink/?LinkID=135170) を参照してください。
    + CategoryInfo          : セキュリティ エラー: (: ) []、ParentContainsErrorRecordException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

そのときに一時的に許可するには

```ps1
PS> Set-ExecutionPolicy RemoteSigned -Scope Process
```

システムワイドに設定するには、管理者権限で

```ps1
PS> Set-ExecutionPolicy RemoteSigned
```

## version確認

```ps1
PS> $PSVersionTable
```

## httpアクセス

[curl/PowerShellでHTTPアクセスいろいろ - Qiita](https://qiita.com/zaki-lknr/items/8950f6acea20961a8afc)

## コーディング

### 型

#### 配列

```ps1
PS C:\Users\zaki> $list = @(1,2,3,"foo","bar","baz")
PS C:\Users\zaki> $list
1
2
3
foo
bar
baz
PS C:\Users\zaki>
```

#### 連想配列

```ps1
PS C:\Users\zaki> $dict = @{key1="value1"; key2=123}
PS C:\Users\zaki> $dict

Name                           Value
----                           -----
key1                           value1
key2                           123


PS C:\Users\zaki>
```
