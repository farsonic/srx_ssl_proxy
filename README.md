### SSL Proxy functional on SRX platforms - Quick Start
```
run file copy https://curl.haxx.se/ca/cacert.pem /var/tmp/
run request security pki generate-key-pair certificate-id ssl-fp-certificate size 2048 type rsa
run request security pki local-certificate generate-self-signed certificate-id ssl-fp-certificate domain-name mydomain.com subject CN=<SERIALNUMBER_OF_SRX> email user@mydomain.com add-ca-constraint
run request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/mydomain-root.pem
set services ssl proxy profile ssl-fp-profile root-ca ssl-fp-certificate     
run equest security pki ca-certificate ca-profile-group load ca-group-name ssl-ca-group filename /var/tmp/cacert.pem | no-more 
set services ssl proxy profile ssl-fp-profile trusted-ca ssl-ca-group    
commit and-quit
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

### Delete all SSL configuration
