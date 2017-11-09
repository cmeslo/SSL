# Verify
## [How to Verify the CSR and Certificate](https://www.cisco.com/c/en/us/support/docs/unified-communications/unified-communications-manager-callmanager/200123-How-to-Verify-the-CSR-and-Certificate-Mi.html)

```
openssl x509 -noout -modulus -in ca/ca-cert.pem | openssl md5
openssl req -noout -modulus -in ca/ca-req.csr | openssl md5
```

## Verifying a cert against a chain
```
openssl verify -CAfile "example.chain.crt" "example.crt"
```

## Cross-validating among CSR, cert, and private key:
```
openssl req -noout -modulus -in example.csr | openssl md5
openssl x509 -noout -modulus -in example.crt | openssl md5
openssl rsa -noout -modulus -in example.key | openssl md5
```

# Display content
```
openssl x509 -in cert.pem -noout -text
openssl x509 -in cert.pem -noout -serial
openssl x509 -in cert.pem -noout -subject
openssl x509 -in cert.pem -noout -fingerprint
```
