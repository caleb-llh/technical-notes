##### how do I view container logs?
get container id using
- `docker ps -a` or
- `crictl ps -a`
then run
- `docker logs {container-id}` or
- `crictl logs {container-id}`

##### how do I view kubernetes components configuration?
- e.g. `ps -aux | grep kubelet` to see what is the `--network-plugin` configured to

##### how do I run commands in the container using kubectl?
- run individual commands `kubectl exec {pod-name} -- {command}`
- run interactive terminal `kubectl exec -it {pod-name}`

##### how do I generate resource yaml files?
- from current resource: `kubectl get pod {pod-name} -o yaml > pod.yaml`
- from template (not all resources): `kubectl create configmap test-cm --dry-run=client -o yaml > test-cm.yaml`. 
	- use `kubectl create configmap -h` for template commands to copy

##### what are the TCP ports that are distinct to kubernetes
- 2379,2380 - etcd
- 6443 - kube-api
- 10250 - kubelet
- 10257 - kube-controller-manager
- 10259 - kube-scheduler
- 30000- 32767 - NodePort services
https://kubernetes.io/docs/reference/networking/ports-and-protocols/


##### some cheat code to remember
- count number of pods: `kubectl get pods --no-headers | wc -l`
- view API versions, verbs, and other information about resources: `kubectl api-resources -o wide`
- change version of etcdctl: 
	- `ETCD_API=3 ./etcdctl {command}` or
	- `export ETCD_API=3` then run your etcd command

##### kubernetes cheatsheet
- kubectl cheatsheet - https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- cka cheatsheet - https://medium.com/@mrJTY/kubernetes-cka-exam-cheat-sheet-6194ccf162bb