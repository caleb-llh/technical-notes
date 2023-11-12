# cri
##### what is container runtime interface?
- Kubernetes's runtime API that allow any container runtime implementation as long as they adhere to the OCI (Open Container Initiative) standard - imagespec and runtimespec
	- CLI: `crictl`
- just like dockerd daemon is the API for docker.
	- below dockerd/CRI: underlying container runtime e.g. containerd (CLI: `nerdctl`) - pulls images, manages networking, storage of containers.

##### how to link kubernetes to container runtime?
- specify `--container-runtime-endpoint=unix:///var/run/containerd/containerd.sock` when starting kubelet


# csi
##### what is container storage interface?
- Kubernetes's storage API

# cni
##### what is container network interface?
- Kubernetes's networking API that allocates routes, network interfaces and IP addresses to pods dynamically. 
- network plugins e.g. calico, flannel - software components that implement CNI.

##### how to link kubernetes to network plugin?
- specify `--network-plugin=cni` when starting kubelet
