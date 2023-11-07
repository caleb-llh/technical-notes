public key infrastructure
- uses x509 certificate format, structured in a chain of trust 
- infrastructure refers to the CAs, servers, processes to distribute and maintain digital certs

##### what is asymmetric encryption?
- public key/"lock" (.pem/ .crt) 
- private key (-key.pem/ .key )

##### what does a digital signature contain?
- signature = private_key(hash(data))

##### how to verify signature?
- hash(data) == public_key(signature)

##### how to generate a cert?
- using tools like `openssl`, `cfssl`, `easyrsa`
- create a private key
- create a CSR (certificate signing request) containing public key
	- private key --> csr (containing pub key)
- CA returns a certificate signed with CA's private key. 
	- for root cert: 
		- root csr + ca_private_key --> root cert
	- for non root cert: 
		- csr + ca_private_key + root cert --> cert