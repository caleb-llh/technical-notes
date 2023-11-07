public key infrastructure
- uses x509 certificate format, structured in a chain of trust 
- infrastructure refers to the CAs, servers, processes to distribute and maintain digital certs

### TLS
transport layer security. aka ssl: secure sockets layer

***what is asymmetric encryption?***
- public key/"lock" (.pem/ .crt) 
- private key (-key.pem/ .key )

***what does a digital signature contain?***
- signature = private_key(hash(data))

***how to verify signature?***
- hash(data) == public_key(signature)

***what does a digital cert contain?***
- public key/"lock"
- CA name
- signature (by CA)
- expiry
- other info about domain 

***how to verify cert?***
- hash(cert) == ca_public_key(signature)
- ca_public_key is shipped with the browser

***explain the tls handshake process***
1. #todo

**explain the mtls handshake process**
1. #todo

***how does https work?***
- #todo 

***how are dns hostname and certificate common name linked?***
- #todo 


### SSH
secure shell transfer

**how does key-based authentication work using asymmetric encryption?**
- use public "lock" to lock an access door on server
- use private key on client machine as authentication 


### IPSec VPN
internet protocol security
- Uses Internet Key Exchange (IKE): x509 certs for authentication and diffie-hellman key exchange for session key

