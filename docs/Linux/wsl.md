# WSL

## 一覧

```console
wsl --list
```

インストール可能なディストリビューション一覧

```console
wsl --list --online
```

## インストール

```console
wsl --install
```

デフォルトは`Ubuntu`がインストールされる。  
ディストリビューションを指定するには以下。

```console
wsl --install -d Ubuntu-20.04
```

インストールが完了すると起動まで行われ、初期設定が開始される。  
Ubuntuの場合、作成するアカウントの入力を行う。

## WSLバージョンの設定

WSL 2に設定するには以下。

```console
wsl --set-version Ubuntu-20.04 2
```

実行すると以下の通り。

```console
PS C:\Users\h_miyazaki> wsl --set-version Ubuntu-20.04 2
変換中です。この処理には数分かかることがあります...
WSL 2 との主な違いについては、https://aka.ms/wsl2 を参照してください
変換が完了しました。
```

バージョンを確認するには以下。

```console
wsl -l -v
```

## rebootする

WSLのターミナルで`reboot`実行しても以下エラーで実行できない。

```console
zaki@wensley:~$ sudo reboot
[sudo] password for zaki: 
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
Failed to talk to init daemon.
```

再起動するには、ホストOSの`wsl`コマンドでterminateすることで停止するので、再接続する。

```console
wsl --terminate Ubuntu-20.04
```

## 停止する

```console
PS C:\Users\h_miyazaki> wsl -l -v
  NAME                      STATE           VERSION
* docker-desktop-data       Running         2
  Ubuntu-20.04              Running         1
```

`Running`になっているホストを停止するには、↑と同様`--terminate | -t`で停止する。

```console
wsl -t Ubuntu-20.04
```

## 環境の削除

```console
> wsl --unregister ubuntu-20.04-container
登録を解除しています...
```

## VM環境のエクスポート

tar形式にエクスポートする。

```console
wsl --export Ubuntu-20.04 Ubuntu-20.04.tar
```

カレントに `Ubuntu-20.04.tar` ファイルが作成される。

## VM環境のインポート

エクスポートしたtarファイルをWSLに登録する。  
名前を変更することで、環境のコピーとなる。

書式は `wsl --import ディストリビューション名 インストール先ファイルパス インポートするtarファイル`。

```console
wsl --import ubuntu-20.04-container .\work\wsl\ubuntu-20.04-container .\Ubuntu-20.04.tar
```

制限として、デフォルトユーザーが`root`になってしまう仕様がある。

## デフォルトユーザーの変更

PowerShellで設定可能。

```console
PS C:\Users\zaki> Function WSL-SetDefaultUser ($distro, $user) { Get-ItemProperty Registry::HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss\*\ DistributionName | Where-Object -Property DistributionName -eq $distro | Set-ItemProperty -Name DefaultUid -Value ((wsl -d $distro -u $user -e id -u) | Out-String); };
PS C:\Users\zaki> WSL-SetDefaultUser ubuntu-20.04-container zaki
```
