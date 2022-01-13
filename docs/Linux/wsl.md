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
