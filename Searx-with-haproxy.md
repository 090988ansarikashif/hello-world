[HAProxy](http://www.haproxy.org/) is a free, very fast and reliable solution offering high availability, load balancing, and proxying for TCP and HTTP-based applications.

## Sample configuration
    frontend http
      mode http
      bind <public_ip>:80
      bind <public_ip>:443

      # acl to catch the domain name where searx is served.
      acl host_searx  hdr(host) -i searx.example.com

      # force HTTPS
      redirect scheme https code 301 if !{ ssl_fc }

      # use the searx backend if it matches the correct domain name
      use_backend  searx if host_searx

      # use another backend for other subdomains.
      default_backend other_site

    backend searx
      mode http
      server python2_searx 127.0.0.1:8888 maxconn 50 check inter 5m rise 1 fastinter 1s downinter 1s

    backend other_site
       server nginx [...]

## Static asset compression
Modify the searx backend declared in haproxy by adding the following

    backend searx
       [...]
       compression algo gzip
       compression type text/html text/plain text/css application/javascript

## Security

    backend searx
       [...]
       http-response set-header Strict-Transport-Security max-age=15768000
       http-response set-header X-Frame-Options SAMEORIGIN
       http-response set-header X-Content-Type-Options nosniff
       http-response set-header X-XSS-Protection "1; mode=block"
       http-response set-header Content-Security-Policy "default-src 'none';script-src 'self' 'unsafe-eval' 'unsafe-inline';style-src 'self' 'unsafe-inline';img-src 'self' data: blob:;font-src 'self';connect-src 'self';media-src 'self'"

## Change the server header response.
By default the 'Server' header of Searx is: "Server: Werkzeug/0.11.11-dev Python/2.7.12".

You may change this to anything else with

    backend searx
       http-response set-header Server Apache-Nginx