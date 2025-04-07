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

## help

```ps1
Get-Help 〇〇
```

<https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.core/get-help>

## httpアクセス

[curl/PowerShellでHTTPアクセスいろいろ - Qiita](https://qiita.com/zaki-lknr/items/8950f6acea20961a8afc)

## コマンドレット

### ファイルのハッシュ値

`Get-FileHash`で取れる。

```ps1
PS C:\Users\zakih\Downloads> Get-FileHash .\r2tokimeki-0.1.0.zip

Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
SHA256          04B71F5099FDBC96A8497AD88D16C4169F978E931F0D59B7AAE3E2DC8B383CE1       C:\Users\zakih\Downloads\r2to...

```

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

#### 型を確認する

`GetType()`を使用する。

```ps1
$flen_values.GetType()
```

これで型を確認できる。  
例えばこんな出力になる。

```console
IsPublic IsSerial Name                                     BaseType
-------- -------- ----                                     --------
True     True     Byte[]                                   System.Array
```

### ファイル情報

```ps1
Get-ItemProperty <FILE_PATH>
```

[Get-ItemProperty (Microsoft.PowerShell.Management) - PowerShell | Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.management/get-itemproperty?view=powershell-7.5)

#### 一覧表示

```ps1
PS C:\Users\zakih\Desktop> Get-ItemProperty .\P1490954.jpg | Format-List


    ディレクトリ: C:\Users\zakih\Desktop



Name           : P1490954.jpg
Length         : 110922
CreationTime   : 2025/01/23 17:39:18
LastWriteTime  : 2025/01/23 17:39:18
LastAccessTime : 2025/04/06 12:00:01
Mode           : -a----
LinkType       :
Target         : {}
VersionInfo    : File:             C:\Users\zakih\Desktop\P1490954.jpg
                 InternalName:
                 OriginalFilename:
                 FileVersion:
                 FileDescription:
                 Product:
                 ProductVersion:
                 Debug:            False
                 Patched:          False
                 PreRelease:       False
                 PrivateBuild:     False
                 SpecialBuild:     False
                 Language:
```

#### 指定プロパティのみ

`-Name`を使用。

```ps1
PS C:\Users\zakih\Desktop> Get-ItemProperty .\P1490954.jpg -Name LastWriteTime


LastWriteTime : 2025/01/23 17:39:18
PSPath        : Microsoft.PowerShell.Core\FileSystem::C:\Users\zakih\Desktop\P1490954.jpg
PSParentPath  : Microsoft.PowerShell.Core\FileSystem::C:\Users\zakih\Desktop
PSChildName   : P1490954.jpg
PSDrive       : C
PSProvider    : Microsoft.PowerShell.Core\FileSystem
```

あるいは直接参照。

```ps1
PS C:\Users\zakih\Desktop> (Get-ItemProperty .\P1490954.jpg).LastWriteTime

2025年1月23日 17:39:18
```

## Azure PowerShell

### Azure Az PowerShellインストール

[Azure Az PowerShell モジュールをインストールする | Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/azure/install-az-ps)

```ps1
Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
```

### サインイン

```ps1
Connect-AzAccount
```

ディレクトリが複数ある場合は指定の必要がある。

```ps1
Connect-AzAccount -TenantId ********
```

### ディレクトリ切り替え

```ps1
Select-AzContext ...
```

サインイン済みのディレクトリはtabでサジェストできる。
