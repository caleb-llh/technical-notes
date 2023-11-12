```table-of-contents
```
# kubernetes components
##### what are the control plane components?
- **kube-apiserver** (input): horizontally scalable API server to read/manipulate cluster state. only kube-apiserver talks to etcd
- **etcd** (memory): stores cluster metadata and desired state. key-name of etcd documents also follow a hierarchical naming similar to kubernetes API resources
- **kube-controller-manager**(brain): runs all the controllers by default (e.g. NodeController, ReplicationController) unless specified otherwise during start up. controllers watches/poll etcd (via kube-apisever) for changes and calls kube-apiserver to achieve the desired state.
- **kube-scheduler**(output): simply decides which node should run which pod. but it is the node kubelet that starts the pod.
- **cloud-controller-manager**(output): runs controllers (NodeController, RouteController, ServiceController). calls the cloud API.
- **dns**: not strictly required. e.g. coredns

##### what are the node components?
- **kubelet**(input): registers node, listens to kube-apiserver, provides periodic status reports, communicates with container runtime (e.g. docker, cri-o, containerd) in a "CRI" way to manage container lifecycle
- **kube-proxy**: 
	- allow pods in different nodes to communicate through services. 
	- services are virtual components - just a mapping of service IP to pod IPs. kube-proxy modifies iptable rules (iptable handles NAT) to store these mappings.
	- pod network (internal virtual network) spans across cluster, is created by a network plugin

##### what are the TCP ports that are distinct to kubernetes?
- 2379,2380 - etcd
- 6443 - kube-api
- 10250 - kubelet
- 10257 - kube-controller-manager
- 10259 - kube-scheduler
- 30000- 32767 - NodePort services
https://kubernetes.io/docs/reference/networking/ports-and-protocols/

# kubernetes system files

##### how does kubeadm deploy kubernetes components?
- kubeadm deploys kube-proxy as DaemonSets, and control plane components as Pods. (core-dns as Deployment)
- Caveat: 
	- kubeadm does not deploy kubelets - have to download binary, create systemd service. 
	- for kubeadm, kubelets are necessary in control plane nodes, since control plane components are scheduled as pods.
##### kubeadm: where to find manifests for kube system components?
- e.g. `/etc/kubernetes/manifests/kube-apiserver.yaml`
- e.g. `/etc/kubernetes/manifests/etcd.yaml`
##### kubeadm: where to find manifests for static pods?
- e.g. `/etc/kubernetes/manifests/static-app.yaml`
##### kubeadm: where to find tls certs for kube system components?
- e.g. `/etc/kubernetes/pki/ca.crt`
- e.g. `/etc/kubernetes/pki/etcd/ca.crt` (etcd has its own CA)
##### kube-manual: where to find systemd files and binaries for kube system components?
**binaries**
- e.g. `/usr/local/bin/kubelet`
**service files**
- e.g. `/etc/systemd/system/kubelet.service` --> `ExecStart=/usr/local/bin/kubelet --<args>`
