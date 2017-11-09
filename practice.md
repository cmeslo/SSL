# Practice with own CA

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
