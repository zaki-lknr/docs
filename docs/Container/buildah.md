# Buildah

## ビルド

```console
buildah build -t my-registry.example.org:5000/repo/app:latest .
```

### Containerfile指定

```console
buildah build -f path/to/Containerfile -t my-registry.example.org:5000/repo/app:latest .
```

## push

`docker push`などと同じ構文

```console
buildah push my-registry.example.org:5000/repo/image:latest
```

## dind

DockerコンテナとしてデプロイしたBuildahコンテナ内でビルドするには、`--privileged`オプション付与して起動する。

```console
docker run --name buildah -d --rm --privileged quay.io/buildah/stable:latest ...
```

無い場合はエラーになる。

```console
sh-5.3# buildah build -t test .
Error during unshare(CLONE_NEWUSER): Operation not permitted
ERRO[0000] parsing PID "": strconv.Atoi: parsing "": invalid syntax 
ERRO[0000] (Unable to determine exit status)          
```
