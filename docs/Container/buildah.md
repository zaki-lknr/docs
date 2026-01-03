# Buildah

- [第20章 Buildah でコンテナーイメージの構築 | コンテナーの構築、実行、および管理 | Red Hat Enterprise Linux | 9 | Red Hat Documentation](https://docs.redhat.com/ja/documentation/red_hat_enterprise_linux/9/html/building_running_and_managing_containers/assembly_building-container-images-with-buildah)
- [DockerユーザーのためのPodmanとBuildahの紹介 - 赤帽エンジニアブログ](https://rheb.hatenablog.com/entry/2020/07/16/podman_buidah_for_docker_users)

## ビルド

```console
buildah build -t my-registry.example.org:5000/repo/app:latest .
```

### Containerfile指定

```console
buildah build -f path/to/Containerfile -t my-registry.example.org:5000/repo/app:latest .
```

### アーキテクチャ指定

amd64のマシン上でも`--arch arm64`を付与すれば、arm64版イメージビルドできる。

```console
buildah build -f Dockerfile --arch arm64 -t buildah:arm64
```

## push

`docker push`などと同じ構文

```console
buildah push my-registry.example.org:5000/repo/image:latest
```

src dst形式でも実行できるため、ビルド時にFQDNを付けていなくても直接pushできる。

```console
buildah push localhost/image:latest my-registry.example.org:5000/repo/image:latest
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
