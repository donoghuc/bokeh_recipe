# bokeh_recipe

## Objective

I have developed an application that uses embedded bokeh serve in a flask app to generate dynamic plots. It works great on my local machine but when I try to get it working on a server on our local network I am running in to problems.
I want to eliminate the specifics of my network and app details so I put together an example replicating the server using docker containers and an official bokeh server embedded example.

### Resources

1. Source for my example: https://github.com/donoghuc/bokeh_recipe
2. Official Bokeh App: https://github.com/bokeh/bokeh/blob/0.12.10/examples/howto/server_embed/flask_gunicorn_embed.py

## Example on local machine

### Relevant configuration:
* fresh install of conda (https://repo.continuum.io/archive/Anaconda3-5.0.1-Linux-x86_64.sh)
* python 3.6.3
* flask  0.12.2
* bokeh 0.12.10
* gunicorn 19.7.1
* tornado 4.5.2

### Installation:
1. clone or download my repo https://github.com/donoghuc/bokeh_recipe
2. navigate to bokeh_recipe/web/project
3. execute: gunicorn -w 1 flask_gunicorn_embed:app
4. open 127.0.0.1:8000 in web browser
  
__works great__


## Example replicating with docker network

### Relevant configuration:
* Docker version 17.09.0-ce
* docker-compose version 1.17.0

### Installation:
1. clone or download my repo https://github.com/donoghuc/bokeh_recipe
2. Build the docker images:
* [root@casdono-linux bokeh_recipe]# docker-compose build
3. Start some containers:
* [root@casdono-linux bokeh_recipe]# docker-compose -f docker-compose.yml up
4. Look up IP of bokehrecipe_nginx: I do this as follows:
* docker ps (copy CONTAINER ID for bokehrecipe_nginx)
* docker inspect [CONTAINER ID for bokehrecipe_nginx] | grep IPAddress
5. Type the IP address found in step 3 into browser

# PLEASE HELP
 
Somehow the template stuff is served but there is a problem with communication between the flask app and the embedded bokeh server

Notice the GET http://127.0.0.1:45853/bkapp/autoload.js in inspector. This just hangs for ever

I am guessing that this is an Nginx config issue. Specifically I need to add a "location" in "bokeh_recipe/nginx/bokeh_app.conf" for /bkapp and direct requests from flask to that.

Any help on this would be sooooooo greatly appreciated!!!!!

Thanks for your time. Please let me know if I can provide any other information,

-Cas Donoghue
