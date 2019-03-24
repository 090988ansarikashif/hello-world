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
openssl req -new -x509 -key searx.key -out searx.crt -sha256
```

**SHA512**
```
openssl req -new -x509 -key searx.key -out searx.crt -sha512
```

## Apache Configuration
https://mozilla.github.io/server-side-tls/ssl-config-generator

https://wiki.mozilla.org/Security/Server_Side_TLS

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
```
SSLHonorCipherOrder on
SSLCipherSuite ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
```

**HSTS - HTTP Strict Transport Security**

**OCSP Stapling**

### Apache Reverse Proxy with SSL

You can use Apache as a reverse proxy to handle the SSL, and communicate to your searx instance via http on a local port. Example configuration for apache with letsencrypt might then be like:
```
<IfModule mod_ssl.c>
	<VirtualHost *:443>
		ServerName searx.yourdomain.tld
		ServerAdmin webmaster@yourdomain.tld
		SSLProxyEngine On
		ProxyRequests Off
                ProxyPreserveHost On
                AddDefaultCharset utf-8
                HostnameLookups off
                UseCanonicalName off
                ProxyBadHeader Ignore
                KeepAlive off
    		ProxyPass	/	http://localhost:10099/
    		ProxyPassReverse	/	http://localhost:10099/
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined
		SSLEngine on
		SSLCertificateFile	/etc/letsencrypt/live/www.yourdomain.tld/cert.pem
		SSLCertificateKeyFile /etc/letsencrypt/live/www.yourdomain.tld/privkey.pem
		<FilesMatch "\.(cgi|shtml|phtml|php)$">
				SSLOptions +StdEnvVars
		</FilesMatch>
		<Directory /usr/lib/cgi-bin>
				SSLOptions +StdEnvVars
		</Directory>
		BrowserMatch "MSIE [2-6]" \
				nokeepalive ssl-unclean-shutdown \
				downgrade-1.0 force-response-1.0
		BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown
SSLCertificateChainFile /etc/letsencrypt/live/www.yourdomain.tld/chain.pem
	</VirtualHost>
</IfModule>
```

You need to activate `proxy` and `proxy_http` modules in apache (e.g. with `a2enmod`), as well as `ssl`.

## nginx Configuration
https://cipherli.st/

**recommended ssl params (strict)**
```
ssl_protocols TLSv1.2;
ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256";
ssl_prefer_server_ciphers on;
ssl_ecdh_curve secp384r1;

ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
ssl_session_tickets off;

add_header Strict-Transport-Security "max-age=31536000";
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
```

This provide strong security, but you should take a look. Use `ECDHE-RSA-CHACHA20-POLY1305` cipher if you're using nginx linked against LibreSSL.

## SSL Configuration test

* https://www.ssllabs.com/ssltest
* https://sslcheck.globalsign.com
* https://tls.imirhil.fr/

## Free SSL-Certificate Authorities

### Let's Encrypt

Get SSL certificates for your server for free with https://letsencrypt.org Let's Encrypt campaign. 
You might want to use certbot, then follow the instructions on the https://certbot.eff.org/ page.

### GlobalSign

https://www.globalsign.com/en/ssl/ssl-open-source/

### Other

- general information/guide on running your own CA:  
https://web.archive.org/web/20150620120849/https://jamielinux.com/docs/openssl-certificate-authority/