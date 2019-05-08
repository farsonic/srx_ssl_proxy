# SSL Proxy functional on SRX platforms - Quick Start

* request security pki generate-key-pair certificate-id ssl-fp-certificate size 2048 type rsa

* request security pki local-certificate generate-self-signed certificate-id ssl-fp-certificate domain-name mydomain.com subject CN=<SERIALNUMBER_OF_SRX> email user@mydomain.com add-ca-constraint

* set services ssl proxy profile ssl-fp-profile root-ca ssl-fp-certificate <- Config change

* request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/mydomain-root.pem

* request security pki ca-certificate ca-profile-group load ca-group-name ssl-ca-group filename default

* set services ssl proxy profile ssl-fp-profile trusted-ca ssl-ca-group . <- Config change
