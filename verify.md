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

## [Openssl command line to verify the signature](https://stackoverflow.com/questions/5140425/openssl-command-line-to-verify-the-signature)

### method 1

```
$ openssl rsautl -sign -inkey my.key -out in.txt.rsa -in in.txt
Enter pass phrase for my.key:
$ openssl rsautl -verify -inkey my-pub.pem -in in.txt.rsa -pubin
Bonjour
```
With this method, all the document is included within the signature file and is outputted by the final command.

### method 2

1. Create digest of document to sign (sender)
2. Sign digest with private key (sender)
3. Create digest of document to verify (recipient)
4. Verify signature with public key (recipient)

```
$ openssl dgst -sha256 -sign my.key -out in.txt.sha256 in.txt 
Enter pass phrase for my.key:
$ openssl dgst -sha256 -verify my-pub.pem -signature in.txt.sha256 in.txt  
Verified OK
```

## Encrypt/Decrypt with base64

### Encrypt

```bash
$ echo "passwordHAHA" | openssl rsautl -pubin -inkey public_key.pem -encrypt | base64

JjVxxZGgst8iiJFTj7GvK/s43eB2GFgxIWT2jdizkJxMFGOvrDyBQHe8ueyNzHnSRUixQPrrLB+Z
al0iC3ZLfC0ev1ndWxWZlyeAM/nHniJHXJ8dPjKVBNyCvuBrUnW3gHeguqBJot/OYZiz8O15c/bF
9XWPgWfkuzZYVf6t+GbrKwzndd9DCxHQMRGcNH8VxbZSYzQz/LC1L5T1iUlXYGajnShSsBpfyL1e
sXMv+4w/xdSCX4u54hxgPeWWc0uou1fkG1Ijl/R0JkCPfmHGFvy0LOCV599Df6V5NUxv8Rq5XFTZ
G/suuHBpnVIF0T++H3kcgDkF5Rhyy0YAeWoplA==
```

### Decrypt

```bash
$ echo "JjVxxZGgst8iiJFTj7GvK/s43eB2GFgxIWT2jdizkJxMFGOvrDyBQHe8ueyNzHnSRUixQPrrLB+Zal0iC3ZLfC0ev1ndWxWZlyeAM/nHniJHXJ8dPjKVBNyCvuBrUnW3gHeguqBJot/OYZiz8O15c/bF9XWPgWfkuzZYVf6t+GbrKwzndd9DCxHQMRGcNH8VxbZSYzQz/LC1L5T1iUlXYGajnShSsBpfyL1esXMv+4w/xdSCX4u54hxgPeWWc0uou1fkG1Ijl/R0JkCPfmHGFvy0LOCV599Df6V5NUxv8Rq5XFTZG/suuHBpnVIF0T++H3kcgDkF5Rhyy0YAeWoplA==" | base64 -d | openssl rsautl -decrypt -inkey rsa_private.key

passwordHAHA
```

# Display content
```
openssl x509 -in cert.pem -noout -text
openssl x509 -in cert.pem -noout -serial
openssl x509 -in cert.pem -noout -subject
openssl x509 -in cert.pem -noout -fingerprint
openssl x509 -in cert.pem -noout -startdate -enddate
```
