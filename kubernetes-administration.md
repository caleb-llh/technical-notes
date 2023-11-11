##### how do I view container logs?
get container id using
`docker ps -a` or
`crictl ps -a`
then run
`docker logs {container-id}` or
`crictl logs {container-id}`


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
- generate manifest yaml
	- from current resource: `kubectl get pod {pod-name} -o yaml > pod.yaml`
	- from template (not all resources): `kubectl create configmap test-cm --dry-run=client -o yaml > test-cm.yaml`. 
		- use `kubectl create configmap -h` for template commands to copy
- view API versions, verbs, and other information about resources: `kubectl api-resources -o wide`

##### kubernetes cheatsheet
- kubectl cheatsheet - https://kubernetes.io/docs/reference/kubectl/cheatsheet/
- cka cheatsheet - https://medium.com/@mrJTY/kubernetes-cka-exam-cheat-sheet-6194ccf162bb