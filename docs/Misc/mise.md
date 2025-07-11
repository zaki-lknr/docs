# mise

## インストール

### Fedora

`dnf`で入れられる。

```console
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager addrepo --from-repofile=https://mise.jdx.dev/rpm/mise.repo
sudo dnf install -y mise
```

### 初期設定

`mise`のパスはインストール方法によって読み替える。  
`dnf`で入れた場合は`/usr/bin/mise`にあるので

### completion


```console
Error: usage CLI not found. This is required for completions to work in mise.
See https://usage.jdx.dev for more information.
```

このエラーが出る場合は

```console
zaki@cloud-dev2:~$ mise plugins install usage
mise plugin:usage ✓ https://github.com/jdx/mise-usage.git#fe3888a  
zaki@cloud-dev2:~$ mise use -g usage
mise usage@2.0.3 ✓ installed                                                                                 mise ~/.config/mise/config.toml tools: usage@2.0.3
```

<https://github.com/jdx/mise/issues/1710>

## パッケージ検索

### 指定パッケージ(コマンド)のバージョン一覧

```console
mise ls-remote <command>
```

## パッケージセットアップ

### (例) Python 3.12.8のインストール

```console
zaki@cloud-dev2:~$ mise install python@3.12.8
mise hint use multiple versions simultaneously with mise use python@3.12 python@3.11
mise hint installing precompiled python from astral-sh/python-build-standalone
if you experience issues with this python (e.g.: running poetry), switch to python-build by running mise settings python.compile=1
mise python@3.12.8 ✓ installed   
```

### カレントディレクトリ以下でこのpythonを有効にする

```console
zaki@cloud-dev2:~/tmp$ mise use python@3.12.8
mise ~/tmp/mise.toml tools: python@3.12.8
```

```console
zaki@cloud-dev2:~/tmp$ which python
~/.local/share/mise/installs/python/3.12.8/bin/python
zaki@cloud-dev2:~/tmp$ cd 
zaki@cloud-dev2:~$ which python
/usr/bin/python
```

### ユーザー環境グローバルにこのpythonを有効にする

```console
zaki@cloud-dev2:~/local/tmp$ mise use -g python@3.12.8
mise WARN  python is defined in ~/local/tmp/mise.toml which overrides the global config (~/.config/mise/config.toml)
mise ~/.config/mise/config.toml tools: python@3.12.8
```

```console
zaki@cloud-dev2:~/local/tmp$ which python
~/.local/share/mise/installs/python/3.12.8/bin/python
zaki@cloud-dev2:~/local/tmp$ cd
zaki@cloud-dev2:~$ which python
~/.local/share/mise/installs/python/3.12.8/bin/python
zaki@cloud-dev2:~$ 
```

```console
zaki@cloud-dev2:~$ sudo su -
最終ログイン: 2024/08/13 (火) 10:16:16 JST 端末:pts/1
root@cloud-dev2:~# which python
/usr/bin/python
root@cloud-dev2:~# 
```
