# authentication
**options**
- files: username, pass
- files: username, token
- certificates
- external e.g. LDAP
- service accounts

## tls
- mtls used for communication across kube system components
- upon start up (as a systemd service or pod), all kube system components requires specifying of 
	- ca cert, so as to validate each others' certs 
	- its server cert + key (if it's a server)
	- its client cert + key (if it's a client)

##### how does tls authentication happen?
when you call kube-api you need to feed in the client-key, client-certificate, certificate-authority for authentication.
- These are CLI arguments if you are using CURL. 
- For kubectl, the current-context in KubeConfig will be used. `~/.kube/config` is the source of truth.
- Other info in KubeConfig: clusters<-->context(user@context)<-->users

##### how is kube api configured with certs?
- when generating kube-api server cert CSR, alternate dns and ip are specified along in  `openssl.cnf` 
- specify ca, server, client cert/key upon start up
- specify in `kube-config.yaml` the following info:
	- ca cert
	- kube-api endpoint
	- users (list) --> for client authentication
		- client-cert
		- client-key

##### how is kubelet configured with certs?
- when generating kubelet server cert CSR, common name uses the node name
	- include group when generating cert e.g. `/O=system:node:<node-name>`
- in each node, specify in `kubelet-config.yaml` the following info:
	- ca cert --> for client authentication
	- server cert identified by its own node name
	- server key

##### how do I set up kubernetes's certificates API client?
- kube-controller-manager has the ca server, for csr-approving, csr-signing. need to specify cluster signing cert and key file upon start up.
administrative steps:
1. generate key: `openssl genrsa -out myuser.key 2048`
2. generate csr using key: `openssl req -new -key myuser.key -out myuser.csr -subj "/CN=myuser"`.
	1. inspect csr using `openssl req -verify -in myuser.csr -text -noout`
3. base64 encode csr: `cat myuser.csr | base64 -w 0
4. create csr manifest. specs include, groups, usage and base64 csr (request). view csr using `kubectl get csr`. apply the csr manifest
5. approve csr: `kubectl certificate appprove myuser`
6. decode cert to plain text: `echo "<base64-cert>" | base64 --decode


##### how to assign identity to client certificate?
- include group in the subject option when generating csr 
	- groups e.g. `/O=system:masters` or `/O=system:node:`
	- users e.g. `/CN=myuser` 
- [[pki#how to generate a cert?]]
- [[tls#openssl cheatsheet]]

## service accounts

##### what are the responsibilities of TokenRequestAPI?
- for every namespace, a default service account is created (limited permissions) 
- every pod in that namespace assumes the default service account automatically:
	- a token is automatically issued by TokenRequestAPI and secret is mounted as a projected volume  `var/run/secrets/kubernetes.io/serviceaccount` 
	- projected volumes allow the pod to access multiple volumes (PVCs, Configmaps, Secrets) in a particular file directory.
- TokenRequestAPI ensures the token issued is time-bound (exp), audience-bound (aud), object-bound
- After creating your own service account, you need to call TokenRequestAPI `kubectl create token {sa-name}` to create a bearer token that is stored as a kubernetes secret
---
# authorization
- authorization-modes specified at kubeapi-server upon start up (sequentially, as long as one of them allow) e.g. Nodes, RBAC
**options**
- RBAC
- ABAC
- node authorization
- webhook mode

##### what are the kubernetes objects used for authorization?
- Role: specifies permissions as rules (apiGroups, resources, verbs). 
	- Verbs: get/watch/list/create/delete
- RoleBinding: specifies user/serviceaccount/group name (subjects) and role (roleRefs)
	- To test permissions: `kubectl auth can-i get pods --as test-user`
- ClusterRole and ClusterRoleBindings are similar but for cluster/non-namespace scoped objects (e.g. nodes, PVs,CSRs), or namespace scoped objects (e.g. pods, PVCs, secrets) across all namespace.
- The identity of the subject/principal is identified in the credential - TLS cert subject

---
# docker security
##### how do I run as a non root user with added capabilities?
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-4
spec:
  containers:
  - name: sec-ctx-4
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 1001
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```