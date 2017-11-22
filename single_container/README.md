# Single Container

In order to reduce the complexity of running nginx in its own container I built this image that runs nginx in same container as the web app

## Installation
NOTE: I am using Docker version 17.09.0-ce

Download or clone repo and navigate to this directory (single_container).

```
# as root
docker build -f Dockerfile -t single_container .
```
![build](screenshots/docker_build.png?raw=true "docker_build")

start a terminal session in new container
```
# as root
docker run -ti single_container:latest
```
In new container start nginx
```
nginx
```
now start gunicorn
```
gunicorn -w 1 -b :8000 flask_gunicorn_embed:app
```
![start](screenshots/start_container.png?raw=true "start")

in a separate terminal (on host machine) find the IP address of the single_container container you are running
```
#as root
docker ps
# then do copy CONTAINER ID and inspect it
docker inspect [CONTIANER ID] | grep IPAddress
```

![find](screenshots/find_ip_single.png?raw=true "find")
