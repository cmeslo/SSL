# Test as server
[source](https://blog.jorisvisscher.com/2015/07/22/create-a-simple-https-server-with-openssl-s_server/)
```
openssl s_server -key key.pem -cert cert.pem -accept 44330 -www
openssl s_server -key key.pem -cert cert.pem -accept 44330 -www -tls1_2
```
We can test our openssl s_server by accessing the following URL via your web browser:

https://localhost:44330

or s_client

# Test as client
```
openssl s_client -connect google.com:443
openssl s_client -connect google.com:443 -tls1
openssl s_client -connect google.com:443 -tls1_1
openssl s_client -connect google.com:443 -tls1_2
```
