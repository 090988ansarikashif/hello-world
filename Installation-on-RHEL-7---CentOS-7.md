Step by step installation for RHEL7 / CentOS 7, based on the official installation instructions on this wiki.

# Basic installation

You need EPEL repo for zlib-devel mod_proxy_uwsgi uwsgi-plugin-python

Install EPEL :
```sh
yum install epel-release && yum update
```

Install packages :
```sh
yum install gcc zlib-devel git openssl-devel libffi-devel python-babel python-virtualenv libxslt-devel
```

Install searx :
```sh
cd /usr/local
git clone https://github.com/asciimoo/searx.git
useradd searx -d /usr/local/searx
chown searx:searx -R /usr/local/searx
```

Install dependencies in a virtualenv :
```sh
su - searx
cd /usr/local/searx
virtualenv searx-ve
. ./searx-ve/bin/activate
pip install --upgrade pip
pip install --upgrade setuptools
./manage.sh update_packages
```

## Configuration
```sh
sed -i -e "s/ultrasecretkey/`openssl rand -hex 16`/g" searx/settings.yml
```

Edit searx/settings.yml if necessary.

## Check
Start searx :
```sh
python searx/webapp.py
```
Go to [http://localhost:8888](http://localhost:8888)

If everything works fine, disable the debug option in settings.yml:
```sh
sed -i -e "s/debug : True/debug : False/g" searx/settings.yml
```

At this point searx is not demonized ; uwsgi allows this.

You can exit the virtualenv and the searx user bash (enter exit).

# uwsgi

Install packages :
```sh
yum install uwsgi uwsgi-plugin-python
```

Create the configuration file /etc/uwsgi.d/searx.ini with this content :
```
[uwsgi]
# Who will run the code
uid = searx
gid = searx

# disable logging for privacy
disable-logging = true

# Number of workers (usually CPU count)
workers = 4
socket = 127.0.0.1:8888

# The right granted on the created socket
chmod-socket = 666

# Plugin to use and interpretor config
single-interpreter = true
master = true
plugin = python
lazy-apps = true
enable-threads = true

# Module to import
module = searx.webapp

# Virtualenv and python path
virtualenv = /usr/local/searx/searx-ve/
pythonpath = /usr/local/searx/
chdir = /usr/local/searx/searx/
```

Change ownership of the new config file :
```sh
chown uwsgi:uwsgi /etc/uwsgi.d/searx.ini
```

Create PID directory :
```
mkdir /run/uwsgi
```

Activate the uwsgi application and restart :
```sh
systemctl enable uwsgi
systemctl start uwsgi
```

# Web server

## with nginx

TODO ...

## with apache 

Add and enable wsgi mod :
```sh
yum install mod_proxy_uwsgi
echo "LoadModule proxy_uwsgi_module modules/mod_proxy_uwsgi.so" > /etc/httpd/conf.modules.d/00-uwsgi.conf
```

Create a proxy configuration file :
```sh
echo "ProxyPass / uwsgi://127.0.0.1:8888/" > /etc/httpd/conf.d/searx.conf
```
Note that if your instance of searx is not at the root, you should change `ProxyPass /` by the location of your instance, like `ProxyPass /searx`.

Restart Apache :
```sh
systemctl restart httpd
```

If you have SELinux enabled, you must allow httpd to allow network connect :
```sh
setsebool -P httpd_can_network_connect 1
```

# How to update (no tested)

```sh
cd /usr/local/searx
su - searx
. ./searx-ve/bin/activate
git stash
git pull origin master
git stash apply
./manage.sh update_packages
exit
systemctl restart uwsgi
```