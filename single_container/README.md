# Single Container

In order to reduce the complexity of running nginx in its own container I built this image that runs nginx in same container as the web app

## Installation
NOTE: I am using Docker version 17.09.0-ce

Download or clone repo and navigate to this directory (single_container).

```
# as root
docker build -f Dockerfile -t single_container .
```
![build](../screenshots/docker_build.png?raw=true "docker_build")

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
![start](../screenshots/start_container.png?raw=true "start")

in a separate terminal (on host machine) find the IP address of the single_container container you are running
```
#as root
docker ps
# then do copy CONTAINER ID and inspect it
docker inspect [CONTIANER ID] | grep IPAddress
```

![find](../screenshots/find_ip_single.png?raw=true "find")

# PROBLEM
Using IP found above (with container running) check out in firefox with inspector.
As you can in screenshot above (see screenshots folder "single_container_broken.png" for raw the get request just hangs
![broke_1](../screenshots/single_container_broken.png?raw=true "broke_1")

I can verify that nginx is serving the static files though by navigating to /bkapp/static/ (see bokeh_recipe/single_container/nginx/bokeh_app.conf for config)
![static](../screenshots/can_serve_static.png?raw=true "static")

Another oddidy is that I try to hit the embedded bokeh server directly (with /bkapp/) but i end up with a 400 (denied?)
![bkapp](../screenshots/hit_bkapp.png?raw=true "bkapp")

# Note about app
- to reduce complexity of dynamically assigning available ports to tornado workers I hard coded in 46518 for port to talk to bokeh serve

# nginx config
I know you could just look at okeh_recipe/single_container/nginx/bokeh_app.conf but I want to show it here. 
I think I need to config nginx to make explicit that the "request" to bkapp to the 127.0.0.1:46518 is originating FROM the server not the client. 

```
## Define the parameters for a specific virtual host/server
server {

    # define what port to listen on
    listen 80;

    # Define the specified charset to the “Content-Type” response header field
    charset utf-8;

    # Configure NGINX to deliver static content from the specified folder
    # NOTE this should be a docker volume shared from the bokehrecipe_web container (css, js for bokeh serve)
    location /bkapp/static/ {
        alias /home/flask/app/web/static/;
        autoindex on;
    }

    # Configure NGINX to reverse proxy HTTP requests to the upstream server (Gunicorn (WSGI server))
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host:$server_port;
        proxy_buffering off;
    }
    # deal with the http://127.0.0.1/bkapp/autoload.js (note hard coded port for now)
    location /bkapp/ {
        proxy_pass http://127.0.0.1:46518;
    }

}
```



