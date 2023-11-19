```table-of-contents
```
# runtime
##### what is CRI - container runtime interface?
- Kubernetes's runtime API that allow any container runtime implementation as long as they adhere to the OCI (Open Container Initiative) standard - imagespec and runtimespec
	- CLI: `crictl`
- just like dockerd daemon is the API for docker.
	- below dockerd/CRI: underlying container runtime e.g. containerd (CLI: `nerdctl`) - pulls images, manages networking, storage of containers.

##### how to link kubernetes to container runtime?
- specify `--container-runtime-endpoint=unix:///var/run/containerd/containerd.sock` when starting kubelet


# storage
##### what is CSI - container storage interface?
- Kubernetes's storage API

##### how to link kubernetes to CSI drivers?
- when the CSI drivers registers themselves via the kubelet plugin registration mechanism on each supported node, kubelet discovers the drivers and and the Unix Domain Socket to use to interact with a CSI driver

# network
##### what is CNI - container network interface?
- Kubernetes's networking API that allocates routes, network interfaces and IP addresses to pods dynamically. 
- network plugins e.g. calico, flannel - software components that implement CNI.

##### how to link kubernetes to network plugin?
- specify `--network-plugin=cni` command line option when starting kubelet
- kubelet reads the config file `--cni-conf-dir` (default `/etc/cni/net.d`) to set up each pod's network.
- Kubernetes first checks the `/etc/cni/net.d` directory and the first configuration that is in there alphabetically, it will choose.
