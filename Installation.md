step by step installation for Debian / Ubuntu with virtualenv.

source : https://about.okhin.fr/posts/Searx/ with some additions

# basic installation

For Ubuntu, be sure to have enable universe repository.

Install packages :
```sh
sudo apt-get install git build-essential libxslt-dev python-dev python-virtualenv
```
Install searx :
```sh
cd /usr/local
sudo git clone https://github.com/asciimoo/searx.git
sudo useradd searx -s /bin/false
sudo chown searx:searx -R /usr/local/searx
```

Install dependencies in a virtualenv :
```sh
su searx -s /bin/bash
cd /usr/local/searx
virtualenv searx-ve
. searx-ve/bin/activate
pip install -r requirements.txt
```

## configuration
```
cp engines.cfg_sample engines.cfg
sed -i -e "s/ultrasecretkey/`openssl rand -hex 16`/g" searx/settings.py
```

edit searx/settings.py or engines.cfg if necessary.

## check
start searx :
```
python searx/webapp.py
```

go to [http://localhost:8888](http://localhost:8888)

if everything works fine, disable debug option in searx/settings.py :
```
sed -i -e "s/debug = True/debug = False/g" searx/settings.py
```

At this point searx is not demonized, uwsgi allows this.

# uwsgi

Install packages :
```
sudo apt-get install uwsgi uwsgi-plugin-python
```

Create the configuration file /etc/uwsgi/apps-available/searx.ini with this content :
```
[uwsgi]
# Who will run the code
uid = searx
gid = searx

# Number of workers
workers = 4

# The right granted on the created socket
chmod-socket = 666

# Plugin to use and interpretor config
single-interpreter = true
master = true
plugin = python

# Application base folder
base = /usr/local/searx

# Module to import
module = searx.webapp

# Virtualenv and python path
virtualenv = /usr/local/searx/searx-ve/
pythonpath = /usr/local/searx/
chdir = /usr/local/searx/searx/

# The variable holding flask application
callable = app
```

Restart uwsgi :
```sh
cd /etc/uwsgi/apps-enabled
ln -s ../apps-available/searx.ini
/etc/init.d/uwsgi restart
```

# web server

## with nginx
If nginx is not installed (uwsgi will not work with the package nginx-light) :
```sh
sudo apt-get install nginx
```

Create the configuration file /etc/nginx/sites-available/searx with this content :
```Nginx
server {
    listen 80;
    server_name searx.example.com;
    root /usr/local/searx

    location / {
            include uwsgi_params;
            uwsgi_pass unix:/run/uwsgi/app/searx/socket;
    }
}
```

Restart nginx :
```sh
sudo /etc/init.d/nginx restart
```

## with apache 
**FIXME : not tested**

Add wsgi mod :
```sh
sudo apt-get install libapache2-mod-wsgi
sudo a2enmod mod-wsgi
```

Add this configuration :
```apache
<Location />
    Options FollowSymLinks Indexes
    SetHandler uwsgi-handler
    uWSGISocket /run/uwsgi/app/searx/socket
</Location>
```

Restart Apache :
```sh
sudo /etc/init.d/apache2 restart
```