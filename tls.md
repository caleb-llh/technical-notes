transport layer security
- aka ssl: secure sockets layer
- uses [[pki]]

##### what does a digital cert contain?
- public key/"lock"
- CA name
- signature (by CA)
- expiry
- other info about domain 

##### how to verify cert?
- hash(cert) == ca_public_key(signature)
- ca_public_key is shipped with the browser

##### explain the tls handshake process
1. #todo

##### explain the mtls handshake process
1. #todo

##### how does https work?
- #todo 

##### how are dns hostname and certificate common name linked?
[[dns#how do you register a domain?]]
- #todo 


##### openssl cheatsheet
```
# generate private key
openssl genrsa -out ca.key 2048
openssl genrsa -out admin.key 2048

# generate csr
openssl req -new -key ca.key -subj "/CN=root-ca" -out ca.csr
openssl req -new -key admin.key -subj "/CN=admin" -out admin.csr

# generate cert
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt

# check cert details
openssl x509 -in ca.crt -text -noout
```