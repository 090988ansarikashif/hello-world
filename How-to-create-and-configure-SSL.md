## Create Keypair

**RSA 2048 Bit** 
```
openssl genrsa -out searx.key 2048
```

**RSA 4096 Bit**
```
openssl genrsa -out searx.key 4096
```

## Create Certificate Request (CSR)

**SHA256**
```
openssl req -new -key searx.key -out searx.csr -new -sha256
```

## Free SSL-Certificate Authorities

### StartSSL

https://www.startssl.com

1. Register and authenticate your server
2. create keypair and csr
3. create certificate (with own key)
4. ...

**You have to add a ChainFile on your server, so the client can work without problems**

```
wget http://www.startssl.com/certs/sub.class1.server.ca.pem
```

Add the folllowing command into your apache configuration:
```
SSLCertificateChainFile /etc/ssl/certs/sub.class1.server.ca.pem
```

TODO: Ngnix

### Let's Encrypt

https://letsencrypt.org

comming soon

### GlobalSign

https://www.globalsign.com/en/ssl/ssl-open-source/