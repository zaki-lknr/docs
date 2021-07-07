# kubectl

分類変えるかも…

## create

### secret

キー名: ファイル内容のsecret作る

```console
$ cat sample.txt 
hello
kubernetes!
$ kubectl create secret generic sample-secret --from-file=lines=sample.txt 
secret/sample-secret created
$ kubectl get secret sample-secret
NAME            TYPE     DATA   AGE
sample-secret   Opaque   1      6s
```

```yaml
$ kubectl get secret sample-secret -o yaml
apiVersion: v1
data:
  lines: aGVsbG8Ka3ViZXJuZXRlcyEK
kind: Secret
metadata:
  creationTimestamp: "2021-07-07T12:12:03Z"
  name: sample-secret
  namespace: default
  resourceVersion: "1041860"
  uid: 9675c818-cb9b-4b7f-a29c-c60bf5a908aa
type: Opaque
```

```console
$ kubectl get secret sample-secret -o jsonpath='{.data.lines}' | base64 -d
hello
kubernetes!
```

これはConfigMapと同じ
