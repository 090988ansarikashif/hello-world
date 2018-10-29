In case this doesn't fit in here feel free to edit or delete it. Thanks for searx.
Because it took me some time to make things work with Apache, I thought I might document it here for others...


First of all you need to enable the Apache headers module.

**a2enmod headers**

**service apache2 restart**


To harden Apache I added these values to my searx virtual host.
I managed to get an A+ rating with https://observatory.mozilla.org/ afterwards.


**`Header set Content-Security-Policy "default-src  www.mydomain.com mydomain.com; frame-ancestors 'self';"`**

**`Header always append X-Frame-Options SAMEORIGIN`**

**`Header always append X-Content-Type-Options nosniff`**

**`Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains;"`**

**`Header always append X-XSS-Protection 1`**
