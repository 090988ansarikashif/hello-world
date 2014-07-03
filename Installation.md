step by step installation for Debian / Ubuntu with virtualenv.

source : https://about.okhin.fr/posts/Searx/ with some additions

# basic installation

For Ubuntu, be sure to have enable universe repository.

Install packages :
```sh
sudo apt-get install git build-essential libxslt-dev python-dev python-virtualenv python-pybabel zlib1g-dev
```
Install searx :
```sh
cd /usr/local
sudo git clone https://github.com/asciimoo/searx.git
sudo useradd searx -d /usr/local/searx
sudo chown searx:searx -R /usr/local/searx
```

Install dependencies in a virtualenv :
```sh
sudo -u searx -i
cd /usr/local/searx
virtualenv searx-ve
. ./searx-ve/bin/activate
pip install -r requirements.txt
python setup.py install
```

## configuration
```
sed -i -e "s/ultrasecretkey/`openssl rand -hex 16`/g" searx/settings.yml
```

Edit searx/settings.yml if necessary.

## check
Start searx :
```
python searx/webapp.py
```

Go to [http://localhost:8888](http://localhost:8888)

If everything works fine, disable the debug option in settings.yml :
```
sed -i -e "s/debug : True/debug : False/g" searx/settings.yml
```

At this point searx is not demonized ; uwsgi allows this.

You can exit the virtualenv and the searx user bash (enter exit command twice).

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

# Number of workers (usually CPU count)
workers = 4

# The right granted on the created socket
chmod-socket = 666

# Plugin to use and interpretor config
single-interpreter = true
master = true
plugin = python

# Module to import
module = searx.webapp

# Virtualenv and python path
virtualenv = /usr/local/searx/searx-ve/
```

Activate the uwsgi application and restart :
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
### Hosted at /
```
server {
    listen 80;
    server_name searx.example.com;
    root /usr/local/searx;

    location / {
            include uwsgi_params;
            uwsgi_pass unix:/run/uwsgi/app/searx/socket;
    }
}
```
Restart service :
```sh
sudo service nginx restart
sudo service uwsgi restart
```

### from subdirectory URL (/searx)
```
location = /searx { rewrite ^ /searx/; }
location /searx {
        try_files $uri @searx; }
location @searx {
        uwsgi_param SCRIPT_NAME /searx;
        include uwsgi_params;
        uwsgi_modifier1 30;
        uwsgi_pass unix:/run/uwsgi/app/searx/socket;
}
```

Enable base_url in searx/settings.yml
```
base_url : http://your.domain.tld/searx/
```

Restart service :
```sh
sudo service nginx restart
sudo service uwsgi restart
```

## with apache 

Add wsgi mod :
```sh
sudo apt-get install libapache2-mod-uwsgi
sudo a2enmod mod-uwsgi
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