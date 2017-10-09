## Classic installation
See https://asciimoo.github.io/searx/dev/install/installation.html.

## With Docker
First, make sure you have installed [Docker](https://docs.docker.com/engine/installation/). **It is recommended to know how Docker containers work.**

#### Build
You should build searx from the embedded Dockerfile. Clone the repo first then build the image :
```
git clone https://github.com/asciimoo/searx.git
cd searx
docker build -t searx .
```

#### Configure
You can custom your searx instance with two environment variables : 
- **IMAGE_PROXY** (default : False), if set to *True* searx will proxy all images.
- **BASE_URL** (default : False) should be set to your domain name (with *http/https*).

#### Run
Run a searx container behind a reverse proxy : 
```
docker run -d --name searx -e IMAGE_PROXY=True -e BASE_URL=https://domain.tld searx
```

Using nginx, create a vhost with the following content : 
```
...
  location / {
      proxy_pass http://searx:8888;
      proxy_set_header        Host                 $host;
      proxy_set_header        X-Real-IP            $remote_addr;
      proxy_set_header        X-Forwarded-For      $proxy_add_x_forwarded_for;
      proxy_set_header        X-Remote-Port        $remote_port;
      proxy_set_header        X-Forwarded-Proto    $scheme;
      proxy_redirect          off;
  }
...
```

If you don't want to use searx behind a reverse proxy, you can map ports, for instance : 
```
docker run -d --name searx -p 8888:8888 -e IMAGE_PROXY=True searx
```
So you can go to [http://localhost:$PORT](http://localhost:$PORT) and enjoy searx.

#### Docker-compose
Using docker-compose and starting the v2 file format, you can use this : 

##### If you're reverse proxying from Nginx that isn't inside a container
```
version: '2'
services:
  searx:
    #network_mode: "bridge"
    build:
      context: /path/to/searx
      dockerfile: Dockerfile
    # Only expose Searx port to localhost 
    ports:
      - "127.0.0.1:8888:8888"
    image: searx
    container_name: searx
    environment:
      - BASE_URL="https://domain.tld"
      - IMAGE_PROXY=True
```
##### If you're utilizing Nginx inside a container.

```
version: '2'
services:
  searx:
    #network_mode: "bridge"
    build:
      context: /path/to/searx
      dockerfile: Dockerfile
    image: searx
    container_name: searx
    environment:
      - BASE_URL="https://domain.tld"
      - IMAGE_PROXY=True
```
### Optional
#### Add to browser search engine options.

This differs from browser to browser but the proper format for search is as follows

```
https://domain.tld/search?q=%s
```

The format for auto complete is as follows

```
https://domain.tld/autocompleter?q=%s
```