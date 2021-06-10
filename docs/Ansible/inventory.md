# インベントリ

## ansible-inventoryコマンド

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
