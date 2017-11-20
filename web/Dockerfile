# Build Docker image for data analysis pipeline

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
# Create the group and user to be used in this container
RUN groupadd flaskgroup && useradd -m -g flaskgroup -s /bin/bash flask

# Create the working directory (and set it as the working directory)
RUN mkdir -p /home/flask/app/web
WORKDIR /home/flask/app/web/project

# Copy the source code into the container
COPY . /home/flask/app/web
RUN cp -R $(bokeh info --static) /home/flask/app/web/project
# result of bokeh info --static

RUN chown -R flask:flaskgroup /home/flask

USER flask
