### SSL Proxy functional on SRX platforms - Quick Start

#### Operational mode commands
Pull down the latest list of trusted certificate authorities from haxx.se and load into the SRX.
Part of the functionality below will populate the root CA's into the SRX configuration. 
If you re-run this you will need to delete the trusted CA's and re-run this process
```
file copy https://curl.haxx.se/ca/cacert.pem /var/tmp/
request security pki generate-key-pair certificate-id ssl-fp-certificate size 2048 type rsa
request security pki local-certificate generate-self-signed certificate-id ssl-fp-certificate domain-name mydomain.com subject CN=MY_SSL_PROXY email user@mydomain.com add-ca-constraint
request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/mydomain-root.pem
request security pki ca-certificate ca-profile-group load ca-group-name ssl-ca-group filename /var/tmp/cacert.pem | no-more 
```

#### Configuration changes for SSL Proxy
Example configuration for basic SSL proxy. 
The profile is referenced in policy configuration
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

### Example policy
The following policy will terminate SSL sessions and act as a man-in-the-middle of the TLS Transaction. With this policy you are now free to use application inspection, IPS, URL filtering etc on the payload as it is unencrypted. 
```
set security policies from-zone lab to-zone untrust policy ssl-test match source-address windows-test
set security policies from-zone lab to-zone untrust policy ssl-test match destination-address any
set security policies from-zone lab to-zone untrust policy ssl-test match application junos-https
set security policies from-zone lab to-zone untrust policy ssl-test then permit application-services ssl-proxy profile-name ssl-fp-profile
```



### Delete all SSL certificates, key-pairs and configuration
This will delete all PKI configuration from the SRX. 
```
run clear security pki local-certificate all 
run clear security pki ca-certificate all | no-more       
run clear security pki crl all               
run clear security pki key-pair all 
delete security pki
delete services ssl proxy
commit and-quit
```
