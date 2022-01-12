# Practice

## Practice 1: sign with own CA (without passphrase)

### CA

1.  Generate private key:
    ```
    openssl genrsa -out ca/ca-key.pem 1024
    ```

2.  Generate csr
    ```
    openssl req -new -out ca/ca-req.csr -key ca/ca-key.pem
    ```

3.  Generate cert, sign by yourself
    ```
    openssl x509 -req -in ca/ca-req.csr -out ca/ca-cert.pem -signkey ca/ca-key.pem -days 3650
    ```

### Server side

1.  Generate private key
    ```
    openssl genrsa -out server/server-key.pem 1024
    ```

2.  Generate csr
    ```
    openssl req -new -out server/server-req.csr -key server/server-key.pem
    ```

3.  Generate cert, server's csr sign by CA
    ```
    openssl x509 -req -in server/server-req.csr -out server/server-cert.pem -CA ca/ca-cert.pem -CAkey ca/ca-key.pem -CAcreateserial -days 3650
    ```

Convert cert to p12 format
```
openssl pkcs12 -export -clcerts -in server/server-cert.pem -inkey server/server-key.pem -out server/server.p12
```

## Practice 2: Practice without CA

with -des3, you need to input a passphrase:

```
openssl genrsa -des3 -out key.pem 2048
openssl req -new -x509 -key key.pem -out cert.pem -days 3650
openssl pkcs12 -inkey key.pem -in cert.pem -export -out cert.pfx
```

## Practice 3: Generate key

### Without passphrase
1.  Generate private key 
    ```
    openssl genrsa -out rsa_private.key 2048
    ```

2.  Generate public key
    ```
    openssl rsa -in rsa_private.key -pubout -out rsa_public.key
    ```

### With aes256 encrypted
1.  Generate private key
    ```
    openssl genrsa -aes256 -passout pass:123456 -out rsa_aes_private.key 2048
    ```

2.  Generate public key
    ```
    openssl rsa -in rsa_aes_private.key -passin pass:123456 -pubout -out rsa_public.key
    ```

## Practice 4: Add / Remove Passphrase
-   Remove a passphrase from a private key
    ```
    openssl rsa -in rsa_aes_private.key -passin pass:123456 -out rsa_private.key
    ```

-   Add a passphrase to a private key
    ```
    openssl rsa -in rsa_private.key -aes256 -passout pass:123456 -out rsa_aes_private.key
    ```

## Practice 5: Generate a self-signed certificate

### Generate RSA private key and self-signed cert
```
openssl req -newkey rsa:2048 -nodes -keyout private_key.pem -x509 -days 365 -out certificate.pem
```

> ```req``` - request
> 
> ```-newkey rsa:2048 rsa_private.key``` - generate private key (pkcs8 format)
> 
> ```-nodes``` - private key without passphrase
> 
> ```-x509``` - output cert
> 
> ```-days 365``` - expired date

after run this command, you will need to input cert owner information:

- CN: CommonName
- OU: OrganizationalUnit
- O: Organization
- L: Locality
- S: StateOrProvinceName
- C: CountryName

or you can input as parameter:

#### Without passphrase
```
openssl req -newkey rsa:2048 -nodes -keyout private_key.pem -x509 -days 365 -out certificate.pem -subj "/C=XX/ST=XX/L=xxxxx/O=Org/OU=dev/CN=xxx.com/emailAddress=xx@xxx.com"
```

### Generate certificate with existed rsa private key
```
openssl req -new -x509 -days 365 -key private_key.pem -out certificate.pem
```
>   ```-new``` - generate cert request
>   
>   ```-x509``` - output cert directly

### Extract the public key from certificate
```
openssl x509 -pubkey -noout -in certificate.pem > public_key.pem
```

### Combine your key and certificate in a PKCS#12 (P12) bundle
```
openssl pkcs12 -inkey private_key.pem -in certificate.pem -export -out certificate.p12
```

### Validate your P2 file
```
openssl pkcs12 -in certificate.p12 -noout -info
```

## Practice 6: PFX

> (windows environment) Some commands works fine on powershell, but not git-bash

### Method 1:

1.  Extract the key-pair
    ```
    openssl pkcs12 -in sample.pfx -nocerts -nodes -out sample.key
    ```
    or
    ```
    openssl pkcs12 -in sample.pfx -nodes -out sample.key
    ```

2.  Get the Private Key from the key-pair
    ```
    openssl rsa -in sample.key -out sample_private.key
    ```

3.  Get the Public Key from key pair
    ```
    openssl rsa -in sample.key -pubout -out sample_public.key
    ```

4.  Need to do some modification to the private key -> to pkcs8 format
    ```
    openssl pkcs8 -topk8 -inform PEM -in sample_private.key -outform PEM -nocrypt
    ```
    > Copy the output and save it as sample_private_pkcs8.key

### Method 2:

-   Extract .key file from .pfx file
    ```
    openssl pkcs12 -in yourfile.pfx -nocerts -out private.key
    ```

-   Extract .crt file from .pfx file
    ```
    openssl pkcs12 -in yourfile.pfx -clcerts -nokeys -out cert.crt
    ```

-   Convert .pfx file to .pem format
    ```cpp
    openssl rsa -in keyfile-encrypted.key -outform PEM -out keyfile-encrypted-pem.key
    ```
