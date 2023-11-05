domain name system
### terminologies
- FQDN: Fully qualified domain name + protocol + path = url
- zone: domain namespace (a domain excluding its subdomains)
### resolving hostname
***how does your computer get the IP address of a particular hostname?***
1. browser dns cache
2.  os dns cache
3. local dns server
4. public dns server e.g. 8.8.8.8 OR 1.1.1.1 (librarian)
5. root nameserver "."(library catalogue)
6. TLD nameserver e.g. ".com" (library shelf)
7. authoritative nameserver - dns hosting provider (dictionary on shelf)

### dns record lifecycle
***how do you register a domain?***
- registrant to visit a domain registrar: e.g. godaddy
	- registrar reserves domain in domain registry: "global database of domain names"
	- registrar is accredited by ICANN - Internet Corporation of Assigned Names and Numbers
- registrant links domain to a list of authoritative nameservers (either nameservers provided by registrar, or third party e.g. route53)
	- nameserver hosts the dns records for that domain


***what are some common dns record types?***
- A: hostname --> IPv4
- AAAA: hostname --> IPv6
- CNAME: alias domain --> "canonical" domain
- NS: domain --> authoritative nameservers
- SOA (start of authority): standard information about domain (admin contact, TTL)

***why does it take awhile for changes to take effect after you add/update a record?***
- each record has a TTL
- each layer of dns servers in the hierarchy caches the record.
- changing record at the authoritative nameserver would require all the layers downstream to invalidate their cache/ wait for expiry

