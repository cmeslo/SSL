# SSL #
Knowledge about ssl.

## Verify ##
### [How to Verify the CSR and Certificate](https://www.cisco.com/c/en/us/support/docs/unified-communications/unified-communications-manager-callmanager/200123-How-to-Verify-the-CSR-and-Certificate-Mi.html) ###

```
openssl x509 -noout -modulus -in ca/ca-cert.pem | openssl md5
openssl req -noout -modulus -in ca/ca-req.csr | openssl md5
```

### Convert a DER file (.crt .cer .der) to PEM(.pem) ###
```
openssl x509 -inform der -in certificate.cer -out certificate.pem
```

### Verifying a cert against a chain ###
```
openssl verify -CAfile "example.chain.crt" "example.crt"
```

### Cross-validating among CSR, cert, and private key: ###
```
openssl req -noout -modulus -in example.csr | openssl md5
openssl x509 -noout -modulus -in example.crt | openssl md5
openssl rsa -noout -modulus -in example.key | openssl md5
```

## Display content ##
```
openssl x509 -in cert.pem -noout -text
openssl x509 -in cert.pem -noout -serial
openssl x509 -in cert.pem -noout -subject
openssl x509 -in cert.pem -noout -fingerprint
```

## Test ##
```
openssl s_client -connect google.com:443
openssl s_client -connect google.com:443 -tls1
openssl s_client -connect google.com:443 -tls1_1
openssl s_client -connect google.com:443 -tls1_2
```

Reference:

* [The Most Common OpenSSL Commands](https://www.sslshopper.com/article-most-common-openssl-commands.html)
* [SSL Basic](http://csc.ocean-pioneer.com/docum/ssl_basic.html)
* [How can I verify if TLS 1.2 is supported](https://serverfault.com/questions/638691/how-can-i-verify-if-tls-1-2-is-supported-on-a-remote-web-server-from-the-rhel-ce)
* [Principle](http://blog.csdn.net/oldmtn/article/details/52208747)
