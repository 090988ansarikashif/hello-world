## Classic installation
See https://asciimoo.github.io/searx/dev/install/installation.html.

## With Docker
Make sure you have installed [Docker](https://docs.docker.com/engine/installation/). For instance, you can deploy searx like this : 
```
docker pull wonderfall/searx
docker run -d --name searx -p $PORT:8888 wonderfall/searx
```
Go to [http://localhost:$PORT](http://localhost:$PORT). 

See https://hub.docker.com/r/wonderfall/searx/ for more informations.

It's also possible to build searx from the embedded Dockerfile (could be lighter).
```
git clone https://github.com/asciimoo/searx.git
cd searx
docker build -t whatever/searx .
```