```table-of-contents
```
# cluster administration
##### how to switch context to another cluster?
- `kubectl config use-context {cluster-name}`
- to run a command on another cluster without switching: `kubectl get pods --context {cluster-name}`

##### how to view container logs?
get container id using
- `docker ps -a` or
- `crictl ps -a`
then run
- `docker logs {container-id}` or
- `crictl logs {container-id}` or
for logs of previous terminated container:
- `kubectl logs {pod-name} --previous`

##### how to view kubernetes components configuration?
- e.g. `ps -aux | grep kubelet` to see what is the `--network-plugin` configured to

##### how to run commands in the container using kubectl?
- run individual commands `kubectl exec {pod-name} -- {command}`
- run interactive terminal `kubectl exec -it {pod-name} -- /bin/bash` 

##### how to generate resource yaml files?
- from current resource: `kubectl get pod {pod-name} -o yaml > pod.yaml`
- from template (not all resources): `kubectl create configmap test-cm --dry-run=client -o yaml > test-cm.yaml`. 
	- use `kubectl create configmap -h` for template commands to copy

##### some cheat code to remember
- count number of pods: `kubectl get pods --no-headers | wc -l`
- view API versions, verbs, and other information about resources: `kubectl api-resources -o wide`
- change version of etcdctl: 
	- `ETCDCTL_API=3 ./etcdctl {command}` or
	- `export ETCDCTL_API=3` then run your etcd command
- see which OS you're on: `cat /etc/*-release`
- for temp files created after editing an uneditable field: `kubectl replace --force -f  /tmp/....yml`
- set your current context to a particular namespace: `kubectl config set-context --current  --namespace=kube-system`
- use another kubeconfig: `kubectl get pods --kubeconfig {path-to-config}`
- download publicly hosted yaml: `curl -LO https://raw.githubusercontent.com/... 

##### kubernetes cheatsheet
- kubectl cheatsheet - https://kubernetes.io/docs/reference/kubectl/cheatsheet/
	- useful exam setup: autocomplete, kubectl alias, export ETCDCTL_API=3
- cka cheatsheet - https://medium.com/@mrJTY/kubernetes-cka-exam-cheat-sheet-6194ccf162bb

---
# cluster install

##### what are the steps to install a cluster?
1. setup networking [prerequisites](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#install-and-configure-prerequisites) - setup ip forwarding for overlay network
3. install [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd) on all nodes, e.g. containerd
4. configure cgroups (linux control groups, which the container runtime depends on, to control resources allocated to processes): `ps -p 1` to determine which cgroup to choose
5. install kubeadm, kubectl, kubelet using apt. `apt-cache madison kubeadm` to see available versions
6. initialize control plane node: `kubeadm init {..args}`
	1. choose pod networking add-on, specify `--pod-network-cidr`, `--apiserver-advertise-address` (use eth0 ip)
7. copy admin kubeconfig to `~/.kube`
8. addons: setup pod network using the public hosted yaml in docs e.g. weave-net
	1. e.g. weave: specify `IP_ALLOC_RANGE` env var based on `pod-network-cidr` --> modify the daemonset of network plugin
	2. e.g. flannel: add `--iface=eth0` to container args
9. join worker nodes to cluster: copy and paste the `kubeadm join ...` command

---
# cluster upgrade

##### how to upgrade a cluster?
- upgrade master nodes followed by worker nodes one by one. if single master node is down, control plane functionalities are down, but applications in worker nodes still continue to run.
	1. drain node: `kubectl drain {node-name} --ignore-daemonsets` - pods get evicted and rescheduled, node is cordoned.
	2. upgrade kubeadm if necessary `apt-get update && apt-get upgrade{or install} kubeadm=X.X.X-XX`
		1. view kubeadm versions using `apt-cache madison kubeadm`
	3. find out latest kubernetes version: `kubeadm upgrade plan`
	4. upgrade control plane components (for master node): `kubeadm upgrade apply vX.X.X`
		1. `upgrade apply` only for the first master node, for additional master nodes or worker nodes, run `kubeadm upgrade node`
	5. upgrade kubelet and kubectl:  
		1. `apt-get upgrade{or install} kubelet=X.X.X-XX kubectl=X.X.X-XX`
		3. `systemctl daemon-reload && systemctl restart kubelet`
	6. uncordon node: `kubectl uncordon {node-name}`
- `kubectl get nodes` tells you the kubernetes version running on each node's kubelet

---
# cluster backup/restore

##### how.to backup etcd?
`export ETCDCTL_ADPI=3`
- save etcd snapshot:  `etcdctl snapshot save snapshot.db`
- inspect snapshot: `etcdctl snapshot status snapshot.db`
for TLS-enabled etcd, run `etcdctl {command}` with
- `--cacert`
- `--cert`
- `--key`
- `--endpoints=[127.0.0.1:2379]`
find cert path from inspecting etcd or looking at etcd startup command options

##### how ro restore etcd backup?
`export ETCDCTL_ADPI=3`
- stop kube-apiserver since it depends on etcd: `systemctl stop kube-apiserver.service`
- restore snapshot: `etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup`
	- this configures a new etcd cluster and configures members as new members. use new data directory (in host filesystem) for the new cluster
	- thus you don't need to pass in the cert authentication options (unlike in backup)
- if kubeadm
	- in `/etc/kubernetes/manifests/etcd.yaml`: update volume host path to new data dir (for kubeadm, i.e. etcd is a staticpod)
- else
	- update `--data-dir /var/lib/etcd-from-backup` in etcd startup options in etcd.service (not for kubeadm because it references container filesystem instead of host filesystem)
	- make sure the new data directory is owned by etcd user (recursively): `chown -R etcd:etcd /var/lib/etcd-from-backup`
	- `systemctl daemon-reload && systemctl restart etcd.service`
- restart control plane components to make sure state is refreshed (kube-apiserver, scheduler, controller-manager, kubelet)