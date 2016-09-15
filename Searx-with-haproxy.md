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