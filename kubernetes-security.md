# authentication
## basic auth


## tls authentication
- mtls used for communication across kube system components
- upon start up (as a systemd service or pod), all kube system components requires specifying of 
	- ca cert, so as to validate each others' certs 
	- its server cert + key (if it's a server)
	- its client cert + key (if it's a client)

##### how to assign permissions to client certificate?
- include group when generating cert e.g. `/O=system:masters`
- [[pki#how to generate a cert?]]
- [[tls#openssl cheatsheet]]

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