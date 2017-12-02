# Build Docker image to replicate server 
# Used to illustrate/investigate serving bokeh serve embedded in flask

# oracle approved base image 
FROM container-registry.oracle.com/os/oraclelinux:7

# communicate through oracle proxy 
# ENV http_proxy=secret
# ENV https_proxy=secret


# bzip2 required to decompress binary conda release
RUN yum -y install bzip2

# download, install and set env for conda release
RUN curl -LO https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN bash Miniconda3-latest-Linux-x86_64.sh -p /miniconda -b
RUN rm Miniconda3-latest-Linux-x86_64.sh
ENV PATH=/miniconda/bin:${PATH}

# pip install dependencies
RUN pip install gunicorn flask pandas bokeh

# download nginx
COPY ./nginx/nginx.repo /etc/yum.repos.d
RUN yum -y install nginx
# replace default config with update 
RUN rm /etc/nginx/nginx.conf
COPY ./nginx/nginx.conf /etc/nginx/
RUN rm /etc/nginx/conf.d/default.conf
COPY ./nginx/bokeh_app.conf /etc/nginx/conf.d/



# Create the group and user to be used in this container
# RUN groupadd flaskgroup && useradd -m -g flaskgroup -s /bin/bash flask

# Create the working directory (and set it as the working directory)
RUN mkdir /bokeh_serve_standalone


# Copy the source code into the container
COPY testapp.py /bokeh_serve_standalone
RUN cp -R $(bokeh info --static) /bokeh_serve_standalone

# RUN chown -R flask:flaskgroup /home/flask

# USER flask

######




