# Create the python environment

In that how to we use the last python 3 version with alpine distribution but you can use another docker image based on python.

```
docker run --rm -it --name searx -v $(pwd):/ws -w /ws python:3-alpine sh                                               
apk add build-base libffi-dev libxml2-dev libxslt-dev openssl-dev
sed -i 's/127.0.0.1/0.0.0.0/g' searx/settings.yml                                                      
python searx/webapp.py 
```

Then go to http://searx.docker:8888 to load the searx website. Where **searx.docker** is the IP of the container, you can run the following command to get it `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' searx`.