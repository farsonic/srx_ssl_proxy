# Command to quickly get SSL Proxy functional on SRX platforms

1) request security pki generate-key-pair certificate-id ssl-fp-certificate size 2048 type rsa

2) request security pki local-certificate generate-self-signed certificate-id ssl-fp-certificate domain-name prowsehouse.com subject CN=<SERIALNUMBER_OF_SRX> email fp@prowsehouse.com add-ca-constraint

3) set services ssl proxy profile ssl-fp-profile root-ca ssl-fp-certificate

4) request security pki local-certificate export certificate-id ssl-fp-certificate type pem filename /var/tmp/prowsehouse-root.pem

5) request security pki ca-certificate ca-profile-group load ca-group-name ssl-ca-group filename default

6) set services ssl proxy profile ssl-fp-profile trusted-ca ssl-ca-group
