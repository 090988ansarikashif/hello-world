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

## Apache Configuration

**deactivate SSLv2 and SSLv3**
* https://en.wikipedia.org/wiki/POODLE
```
SSLProtocol All -SSLv2 -SSLv3
```

**deactivate ssl compression**
* https://en.wikipedia.org/wiki/CRIME
```
SSLCompression off
```

**chipper suite**

TODO: improve SSLCipherSuite
```
SSLHonorCipherOrder on
SSLCipherSuite ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4
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