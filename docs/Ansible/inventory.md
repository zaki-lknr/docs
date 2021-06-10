# インベントリ

## ansible-inventoryコマンド

`ansible-inventory`コマンドを使用すると、ターゲットノード一覧やそのホストに紐づく変数を確認できる。  
ダイナミックインベントリの場合に対象ノードが何になるか確認できる。

### --list

```console
$ ansible-inventory -i inventory.ini --list
```

[このインベントリファイル](https://github.com/zaki-lknr/initialize-kubeadm-ansible/blob/master/inventory.ini)を参照した際のホストグループやホスト変数([`group_vars`など有れば参照](https://github.com/zaki-lknr/initialize-kubeadm-ansible/tree/master/group_vars))をリストアップする。

```json
{
    "_meta": {
        "hostvars": {
            "master01": {
                "additional_packages": [
                    "bash-completion",
                    "open-vm-tools"
                ],
                "additional_services": [
                    "vmtoolsd"
                ],
                "ansible_host": "192.168.0.121",
                "cluster_name": "my-kubeadm-cluster",
                "controlplane_endpoint": "k8s-master.esxi.jp-z.jp",
                "enable_firewalld": "true",
                "pod_network_cidr": "10.244.0.0/16",
                "required_ports": [
                    "6443/tcp",
                    "2379-2380/tcp",
                    "10250/tcp",
                    "10251/tcp",
                    "10252/tcp"
                ],
                "use_cni_plugin": "calico",
                "use_cri": "containerd"
            },
            "master02": {
                "additional_packages": [
                    "bash-completion",
                    "open-vm-tools"
                ],
                "additional_services": [
                    "vmtoolsd"
                ],
                "ansible_host": "192.168.0.122",
                "cluster_name": "my-kubeadm-cluster",
                "controlplane_endpoint": "k8s-master.esxi.jp-z.jp",
                "enable_firewalld": "true",
                "pod_network_cidr": "10.244.0.0/16",
                "required_ports": [
                    "6443/tcp",
                    "2379-2380/tcp",
                    "10250/tcp",
                    "10251/tcp",
                    "10252/tcp"
                ],
                "use_cni_plugin": "calico",
                "use_cri": "containerd"
            },
            "master03": {
                "additional_packages": [
                    "bash-completion",
                    "open-vm-tools"
                ],
                "additional_services": [
                    "vmtoolsd"
                ],
                "ansible_host": "192.168.0.123",
                "cluster_name": "my-kubeadm-cluster",
                "controlplane_endpoint": "k8s-master.esxi.jp-z.jp",
                "enable_firewalld": "true",
                "pod_network_cidr": "10.244.0.0/16",
                "required_ports": [
                    "6443/tcp",
                    "2379-2380/tcp",
                    "10250/tcp",
                    "10251/tcp",
                    "10252/tcp"
                ],
                "use_cni_plugin": "calico",
                "use_cri": "containerd"
            },
            "worker01": {
                "additional_packages": [
                    "bash-completion",
                    "open-vm-tools"
                ],
                "additional_services": [
                    "vmtoolsd"
                ],
                "ansible_host": "192.168.0.125",
                "cluster_name": "my-kubeadm-cluster",
                "controlplane_endpoint": "k8s-master.esxi.jp-z.jp",
                "enable_firewalld": "true",
                "pod_network_cidr": "10.244.0.0/16",
                "required_ports": [
                    "10250/tcp",
                    "30000-32767/tcp"
                ],
                "use_cni_plugin": "calico",
                "use_cri": "containerd"
            },
            "worker02": {
                "additional_packages": [
                    "bash-completion",
                    "open-vm-tools"
                ],
                "additional_services": [
                    "vmtoolsd"
                ],
                "ansible_host": "192.168.0.126",
                "cluster_name": "my-kubeadm-cluster",
                "controlplane_endpoint": "k8s-master.esxi.jp-z.jp",
                "enable_firewalld": "true",
                "pod_network_cidr": "10.244.0.0/16",
                "required_ports": [
                    "10250/tcp",
                    "30000-32767/tcp"
                ],
                "use_cni_plugin": "calico",
                "use_cri": "containerd"
            }
        }
    },
    "all": {
        "children": [
            "master",
            "ungrouped",
            "worker"
        ]
    },
    "master": {
        "hosts": [
            "master01",
            "master02",
            "master03"
        ]
    },
    "worker": {
        "hosts": [
            "worker01",
            "worker02"
        ]
    }
}
```

### --graph --vars

このオプションの場合は、グループ変数定義が独立して表示される。

```console
$ ansible-inventory -i inventory.ini --graph --vars
@all:
  |--@master:
  |  |--master01
  |  |  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |  |  |--{additional_services = ['vmtoolsd']}
  |  |  |--{ansible_host = 192.168.0.121}
  |  |  |--{cluster_name = my-kubeadm-cluster}
  |  |  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |  |  |--{enable_firewalld = true}
  |  |  |--{pod_network_cidr = 10.244.0.0/16}
  |  |  |--{required_ports = ['6443/tcp', '2379-2380/tcp', '10250/tcp', '10251/tcp', '10252/tcp']}
  |  |  |--{use_cni_plugin = calico}
  |  |  |--{use_cri = containerd}
  |  |--master02
  |  |  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |  |  |--{additional_services = ['vmtoolsd']}
  |  |  |--{ansible_host = 192.168.0.122}
  |  |  |--{cluster_name = my-kubeadm-cluster}
  |  |  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |  |  |--{enable_firewalld = true}
  |  |  |--{pod_network_cidr = 10.244.0.0/16}
  |  |  |--{required_ports = ['6443/tcp', '2379-2380/tcp', '10250/tcp', '10251/tcp', '10252/tcp']}
  |  |  |--{use_cni_plugin = calico}
  |  |  |--{use_cri = containerd}
  |  |--master03
  |  |  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |  |  |--{additional_services = ['vmtoolsd']}
  |  |  |--{ansible_host = 192.168.0.123}
  |  |  |--{cluster_name = my-kubeadm-cluster}
  |  |  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |  |  |--{enable_firewalld = true}
  |  |  |--{pod_network_cidr = 10.244.0.0/16}
  |  |  |--{required_ports = ['6443/tcp', '2379-2380/tcp', '10250/tcp', '10251/tcp', '10252/tcp']}
  |  |  |--{use_cni_plugin = calico}
  |  |  |--{use_cri = containerd}
  |  |--{required_ports = ['6443/tcp', '2379-2380/tcp', '10250/tcp', '10251/tcp', '10252/tcp']}
  |--@ungrouped:
  |--@worker:
  |  |--worker01
  |  |  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |  |  |--{additional_services = ['vmtoolsd']}
  |  |  |--{ansible_host = 192.168.0.125}
  |  |  |--{cluster_name = my-kubeadm-cluster}
  |  |  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |  |  |--{enable_firewalld = true}
  |  |  |--{pod_network_cidr = 10.244.0.0/16}
  |  |  |--{required_ports = ['10250/tcp', '30000-32767/tcp']}
  |  |  |--{use_cni_plugin = calico}
  |  |  |--{use_cri = containerd}
  |  |--worker02
  |  |  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |  |  |--{additional_services = ['vmtoolsd']}
  |  |  |--{ansible_host = 192.168.0.126}
  |  |  |--{cluster_name = my-kubeadm-cluster}
  |  |  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |  |  |--{enable_firewalld = true}
  |  |  |--{pod_network_cidr = 10.244.0.0/16}
  |  |  |--{required_ports = ['10250/tcp', '30000-32767/tcp']}
  |  |  |--{use_cni_plugin = calico}
  |  |  |--{use_cri = containerd}
  |  |--{required_ports = ['10250/tcp', '30000-32767/tcp']}
  |--{additional_packages = ['bash-completion', 'open-vm-tools']}
  |--{additional_services = ['vmtoolsd']}
  |--{cluster_name = my-kubeadm-cluster}
  |--{controlplane_endpoint = k8s-master.esxi.jp-z.jp}
  |--{enable_firewalld = true}
  |--{pod_network_cidr = 10.244.0.0/16}
  |--{use_cni_plugin = calico}
  |--{use_cri = containerd}
```

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ansible-inventory -i inventory.ini --graph --vars の結果。<br><br>インベントリ内の変数や group_vars とかの変数定義を解釈して、結局そのホストがどういう変数の値を持つか分かる。<br><br>--list でも似たような情報はわかる（2枚目） <a href="https://t.co/Y3nW49EP2C">pic.twitter.com/Y3nW49EP2C</a></p>&mdash; よこち(yokochi) (@akira6592) <a href="https://twitter.com/akira6592/status/1402625316229578752?ref_src=twsrc%5Etfw">June 9, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
