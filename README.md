# SSL #
Knowledge about ssl.


### [How to Verify the CSR and Certificate](https://www.cisco.com/c/en/us/support/docs/unified-communications/unified-communications-manager-callmanager/200123-How-to-Verify-the-CSR-and-Certificate-Mi.html) ###

```openssl x509 -noout -modulus -in ca/ca-cert.pem | openssl md5```

```openssl req -noout -modulus -in ca/ca-req.csr | openssl md5```

### Convert a DER file (.crt .cer .der) to PEM(.pem) ###
```openssl x509 -inform der -in certificate.cer -out certificate.pem```
