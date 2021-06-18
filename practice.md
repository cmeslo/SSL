# Practice with own CA (without pass pharse)

## CA

### 1. gen private key
```
openssl genrsa -out ca/ca-key.pem 1024
```

### 2. gen csr
```
openssl req -new -out ca/ca-req.csr -key ca/ca-key.pem
```

### 3. gen cert, sign by yourself
```
openssl x509 -req -in ca/ca-req.csr -out ca/ca-cert.pem -signkey ca/ca-key.pem -days 3650
```

## Server side

### 1. gen private key
```
openssl genrsa -out server/server-key.pem 1024
```

### 2. gen csr
```
openssl req -new -out server/server-req.csr -key server/server-key.pem
```

### 3. gen cert, server's csr sign by CA
```
openssl x509 -req -in server/server-req.csr -out server/server-cert.pem -CA ca/ca-cert.pem -CAkey ca/ca-key.pem -CAcreateserial -days 3650
```

### convert cert to p12 format
openssl pkcs12 -export -clcerts -in server/server-cert.pem -inkey server/server-key.pem -out server/server.p12

# Practice without CA
with -des3, you need to input a pass phrase.
```
openssl genrsa -des3 -out key.pem 2048
openssl req -new -x509 -key key.pem -out cert.pem -days 3650
openssl pkcs12 -inkey key.pem -in cert.pem -export -out cert.pfx
```

# Gen key
[source](https://www.cnblogs.com/littleatp/p/5878763.html)

## without pass pharse
### 1. gen private key 
```
openssl genrsa -out rsa_private.key 2048
```

### 2. gen public key
```
openssl rsa -in rsa_private.key -pubout -out rsa_public.key
```

## with aes256 encrypted
### 1. gen private key
```
openssl genrsa -aes256 -passout pass:123456 -out rsa_aes_private.key 2048
```

### 2. gen public key
```
openssl rsa -in rsa_aes_private.key -passin pass:123456 -pubout -out rsa_public.key
```

# Convert
## remove a passphrase from a private key
```
openssl rsa -in rsa_aes_private.key -passin pass:123456 -out rsa_private.key
```

## add a passphrase to a private key
```
openssl rsa -in rsa_private.key -aes256 -passout pass:123456 -out rsa_aes_private.key
```

# Generate a self-signed certificate

## gen RSA private key and self-signed cert
```
openssl req -newkey rsa:2048 -nodes -keyout rsa_private.key -x509 -days 365 -out cert.crt
```
```req``` is request,
```-newkey rsa:2048 rsa_private.key``` is gen private key (pkcs8 format),
```-nodes``` means private key without pass pharse,
```-x509``` is output cert,
```-days 365``` is expired date

after run this command, you will need to input cert owner information, or you can input as parameter:
```
openssl req -newkey rsa:2048 -nodes -keyout rsa_private.key -x509 -days 365 -out cert.crt -subj "/C=XX/ST=XX/L=Xxxxx/O=Org/OU=dev/CN=xxx.com/emailAddress=xx@xxx.com"
```
- CN: CommonName
- OU: OrganizationalUnit
- O: Organization
- L: Locality
- S: StateOrProvinceName
- C: CountryName

## gen cert with existed rsa private key
```
openssl req -new -x509 -days 365 -key rsa_private.key -out cert.crt
```
```-new``` is gen cert request, with ```-x509``` is output cert directly

## extract the public key from cert
```
openssl x509 -pubkey -noout -in cert.crt > public_key.pem
```
