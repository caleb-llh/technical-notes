```table-of-contents
```
# cluster administration
##### how to view container logs?
get container id using
- `docker ps -a` or
- `crictl ps -a`
then run
- `docker logs {container-id}` or
- `crictl logs {container-id}`

##### how to view kubernetes components configuration?
- e.g. `ps -aux | grep kubelet` to see what is the `--network-plugin` configured to

##### how to run commands in the container using kubectl?
- run individual commands `kubectl exec {pod-name} -- {command}`
- run interactive terminal `kubectl exec -it {pod-name}`

##### how to generate resource yaml files?
- from current resource: `kubectl get pod {pod-name} -o yaml > pod.yaml`
- from template (not all resources): `kubectl create configmap test-cm --dry-run=client -o yaml > test-cm.yaml`. 
	- use `kubectl create configmap -h` for template commands to copy

##### some cheat code to remember
- count number of pods: `kubectl get pods --no-headers | wc -l`
- view API versions, verbs, and other information about resources: `kubectl api-resources -o wide`
- change version of etcdctl: 
	- `ETCD_API=3 ./etcdctl {command}` or
	- `export ETCD_API=3` then run your etcd command

##### kubernetes cheatsheet
- kubectl cheatsheet - https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- cka cheatsheet - https://medium.com/@mrJTY/kubernetes-cka-exam-cheat-sheet-6194ccf162bb

---
# cluster install

---
# cluster upgrade

##### how to upgrade a cluster?
- upgrade master nodes followed by worker nodes one by one. if single master node is down, control plane functionalities are down, but applications in worker nodes still continue to run.
	1. drain node: `kubectl drain {node-name} --ignore-daemonsets` - pods get evicted and rescheduled, node is cordoned.
	2. install kubeadm `apt-get update && apt-get install kubeadm=X.X.X`
		1. view kubeadm versions using `apt-cache madison kubeadm`
	3. find out latest kubernetes version: `kubeadm upgrade plan`
	4. upgrade control plane components (for master node): `kubeadm upgrade apply vX.X.X`
		1. upgrade apply only for the first master node, for additional master nodes, run `kubeadm upgrade node`
	5. upgrade kubelet:  
		1. `apt-get upgrade kubelet=X.X.X`
		2. `kubeadm upgrade node config --kubelet-version vX.X.X`
		3. `systemctl daemon-reload && systemctl restart kubelet`
	6. uncordon node: `kubectl uncordon {node-name}`
- `kubectl get nodes` tells you the kubernetes version running on each node's kubelet

---
# cluster backup/restore
