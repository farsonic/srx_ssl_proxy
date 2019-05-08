### SSL Proxy functional on SRX platforms - Quick Start

#### Operational mode commands
```
file copy https://curl.haxx.se/ca/cacert.pem /var/tmp/
request security pki generate-key-pair certificate-id ssl-fp-certificate size 2048 type rsa
request security pki local-certificate generate-self-signed certificate-id ssl-fp-certificate domain-name mydomain.com subject CN=<SERIALNUMBER_OF_SRX> email user@mydomain.com add-ca-constraint
request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/mydomain-root.pem
request security pki ca-certificate ca-profile-group load ca-group-name ssl-ca-group filename /var/tmp/cacert.pem | no-more 
```

#### Configuration changes for SSL Proxy
```
set services ssl proxy profile ssl-fp-profile root-ca ssl-fp-certificate
set services ssl proxy profile ssl-fp-profile trusted-ca ssl-ca-group    
commit and-quit
```
### Generate PEM format certificate
This file is what needs to be copied to your clients/users to ensure they trust the SRX 
```
request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/ssl-proxy.pem 
```

### Delete all SSL certificates, key-pairs and configuration

```
run clear security pki local-certificate all 
run clear security pki ca-certificate all | no-more       
run clear security pki crl all               
run clear security pki key-pair all 
delete security pki
delete services ssl proxy
commit and-quit
```
