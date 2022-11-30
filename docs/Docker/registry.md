# Registry

## API

### リポジトリ一覧(イメージ一覧)

```console
curl https://registry.example.org/v2/_catalog
```

### イメージのタグ一覧

リポジトリ一覧でヒットしたリポジトリ名をパスに含める

```console
curl https://registry.example.org/v2/<repository-name>/tags/list
```
