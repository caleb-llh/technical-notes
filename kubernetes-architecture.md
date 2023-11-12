# kubernetes components
##### what are the control plane components?
- **kube-apiserver** (input): horizontally scalable API server to read/manipulate cluster state
- **etcd** (memory): stores cluster metadata and desired state 
- **kube-controller-manager**(brain): runs controllers (e.g. NodeController, ReplicationController). controllers monitor etcd for desired state and calls kube-apiserver to achieve the desired state.
- **kube-scheduler**(output): decides which node should run which pod
- **cloud-controller-manager**(output): runs controllers (NodeController, RouteController, ServiceController). calls the cloud API.
- **dns**: not strictly required. e.g. coredns
##### what are the node components?
- **kubelet**(input): listens to kube-apiserver, provides periodic status reports, communicates with container runtime (e.g. docker, cri-o, containerd) in a "CRI" way to manage container lifecycle
- **kube-proxy**: allow container in different nodes to communicate

# kubernetes system files

# kubeadm
##### kubeadm: where to find manifests for kube system components?
- e.g. `/etc/kubernetes/manifests/kube-apiserver.yaml`
- e.g. `/etc/kubernetes/manifests/etcd.yaml`
##### kubeadm: where to find manifests for static pods?
- e.g. `/etc/kubernetes/manifests/static-app.yaml`
##### kubeadm: where to find tls certs for kube system components?
- e.g. `/etc/kubernetes/pki/ca.crt`
##### kube-manual: where to find systemd files and binaries for kube system components?
**binaries**
- e.g. `/usr/local/bin/kubelet`
**service files**
- e.g. `/etc/systemd/kubelet.service` --> `ExecStart=/usr/local/bin/kubelet --<args>`

# cri
**container runtime interface**
- Kubernetes's runtime API that allow any container runtime implementation as long as they adhere to the OCI (Open Container Initiative) standard - imagespec and runtimespec
	- CLI: `crictl`
- just like dockerd daemon is the API for docker.
	- below dockerd/CRI: underlying container runtime e.g. containerd (CLI: `nerdctl`) - pulls images, manages networking, storage of containers.
##### how to link kubernetes to container runtime?
- specify `--container-runtime-endpoint=unix:///var/run/containerd/containerd.sock` when starting kubelet

# csi
**container storage interface**
- Kubernetes's storage API
# cni
**container network interface**
- Kubernetes's networking API that allocates routes, network interfaces and IP addresses to pods dynamically. 
- network plugins e.g. calico, flannel - software components that implement CNI.
##### how to link kubernetes to container runtime?
- specify `--network-plugin=cni` when starting kubelet
##### what are network policies?
- specify ingress/egress traffic that are allowed - src/dest pod or namespace(using selectors) or ipBlock (using cidr), and ports
- assign policy to a pod using selectors
- network policies are like AWS security groups: they are stateful. If the request traffic is allowed, the return traffic is allowed.
- Default behaviour: 
	- Implicit allow: If no policy is defined, kubernetes has an "allow all" policy. 
	- Implicit deny: If a policy is defined and assigned, but e.g. no egress rule is defined, egress is denied.
- only supported by some CNI solutions e.g. calico, and not on others (won't be enforced) e.g. Flannel.