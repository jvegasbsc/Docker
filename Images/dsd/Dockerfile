##############################################################################################
# Purpose   : Dockerize Components of a simple data science stack (CPU version).
# Python    : 2.7
# Version   : 0.1
# Author    : Rob Lyon (robert.lyon@manchester.ac.uk)
##############################################################################################
#
# This docker file will setup an environment with only a basic data science stack.
# See the container maintained by Kamil Kwiek <kamil.kwiek@continuum.io>, from which I
# created this dockerfile:
#
# https://github.com/ContinuumIO/docker-images/blob/master/anaconda/Dockerfile
##############################################################################################

# Use well supported Ubuntu distribution.
FROM ubuntu:16.04

# Contact me for help!
MAINTAINER robert.lyon@manchester.ac.uk


# The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT,
# COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t
# exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.
WORKDIR /home

#####################################################################
# Make required directories
#####################################################################

# Now create some diectories to store files and scripts
RUN mkdir scripts
RUN mkdir soft
RUN mkdir soft/weka
RUN mkdir workspace

#####################################################################
# Install System tools needed
#####################################################################

RUN apt-get update \
  && apt-get install -y build-essential \
  wget \
  unzip \
  bzip2 \
  software-properties-common \
  python-setuptools

#####################################################################
# Install WEKA data mining tool
#####################################################################

# Change to the WEKA software directory.
WORKDIR /home/soft/weka

# Get WEKA data mining tool. Must be version 3.9.1, see:
# for python compatibility http://pythonhosted.org/python-weka-wrapper/index.html
RUN wget http://prdownloads.sourceforge.net/weka/weka-3-9-1.zip
RUN unzip /home/soft/weka/weka-3-9-1.zip

#####################################################################
# Install Java for WEKA to run.
#####################################################################

# Install Java.
RUN \
  echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
  add-apt-repository -y ppa:webupd8team/java && \
  apt-get update && \
  apt-get install -y oracle-java8-installer && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/oracle-jdk8-installer

# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

#####################################################################
# Install Anaconda (python 2.7 version)
#####################################################################

# Change to the soft (aka software directory).
WORKDIR /home/soft

# Now install Anaconda to manage the python environment. This installs numpy, scipy, matplotlib,
# jupyter etc. Type conda list inside the built docker container, to view the python modules already
# installed.
RUN wget https://repo.continuum.io/archive/Anaconda2-4.2.0-Linux-x86_64.sh
RUN bash Anaconda2-4.2.0-Linux-x86_64.sh -b -p /home/soft/anaconda
RUN rm Anaconda2-4.2.0-Linux-x86_64.sh
ENV PATH /home/soft/anaconda/bin:$PATH

#####################################################################
# Update Anaconda (extra data science tools)
#####################################################################

# Python packages from conda. Here libgcc is needed to run XGBoost,
# whilst GCC is needed for neural network based tools.
RUN conda install -y \
    gcc \
    libgcc \
    ephem \
    astropy \
    pip \
    pandas \
    pillow \
    numba

RUN conda update scikit-learn
RUN conda install -c anaconda javabridge -y
RUN conda update conda

#RUN conda install -c akode xgboost=0.3 -y
RUN pip install xgboost
#RUN pip install python-weka-wrapper
RUN pip install -i https://pypi.anaconda.org/pypi/simple python-weka-wrapper

#####################################################################
# Install CPU version of Tensor Flow
#####################################################################

# Install TensorFlow CPU version from central repo. Python 2.7 version
RUN pip --no-cache-dir install \
    https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.1.0-cp27-none-linux_x86_64.whl

# TensorBoard
EXPOSE 6006
# IPython
EXPOSE 8888

#####################################################################
# Finally...
#####################################################################

# Ensure the OS is updated.
RUN apt-get update

# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash

# RUNNING JUPYTER
#
# To run jupyter you have to pipe the server output to your host machine. You use the following
# steps to do this.
#
# 1. Run the docker container with the command,
#
#       sudo docker run -i -t -p 9999:9999 scienceguyrob/kern
#
# 2. Next you need to run your notebook (within the container) on the IP 0.0.0.0. to do this run the comamnd,
#
#       jupyter notebook --notebook-dir=workspace --ip=0.0.0.0 --port=9999 --no-browser
#
# 3. Now open http://localhost:9999 in your browser to view you jupyter notebook.
#
# You can load a workspace directory from your host machine into your container. For example I have
# a path /Volumes/data/Dropbox/Projects/Jupyter which has my notebooks. If I run,
#
#    sudo docker run -i -t -p 9999:9999 scienceguyrob/kern -v /Volumes/data/Dropbox/Projects/Jupyter:/home/workspace
#
# I should see my notebooks when running:
#
# jupyter notebook --notebook-dir=workspace --ip=0.0.0.0 --port=9999 --no-browser
#
# Please be careful mounting volumes, as anything alter in your host folder will be changed.
#
# DOCKER DETAILS
#
# Docker is basically a system that runs a virtualised software environment - so you can run an OS
# within in OS (e.g. run a full linux kernel on your Mac). Docker is cool, and below I share the
# Docker commands I use most often. These will help you get started.
#
# Docker guide: https://docs.docker.com/engine/getstarted/
# Docker site: https://www.docker.com
#
# To execute docker commands you have to use sudo. Below are all the docker commands available to you.
#
# LOGIN COMMAND - This login command logs you into the docker hub, which is useful if you want to
# upload your own docker images. This is an online system which hosts pre-built docker images.
#
#   sudo docker login
#
#
# IMAGES COMMAND - This command prints out the docker images downloaded onto your host machine,
# which are available to run locally.
#
#   sudo docker images
#
#
# BUILD COMMAND - This command builds the specified docker file on your host machine. Note the full
# stop at the end. This is required and isn’t a typo.
#
#   sudo docker build -t <docker file> .
#
#
# PUSH COMMAND - This command uploads the docker file built via the build command, to the docker hub
# (needs a dockerhub account and you must be logged in).
#
#   sudo docker push <docker file>
#
#
# RMI COMMAND - This command deletes a local docker image. Useful if an image is faulty,
# or disk space is low. To find the docker image id’s, just run the "sudo docker images command".
#
#   sudo docker rmi -f <image id>
#
#
# PULL COMMAND - This command pulls a docker image from the docker hub.
#
#   sudo docker pull <docker file>
#
#
# RUN COMMAND - This command runs a docker image locally on your host machine. The -v flag tells
# docker to mount a host file directory, inside the docker container. This allows you to copy files
# between the host, and the virtualised docker container. Here are the main parameters:
#
#                	-i is the interactive flag
#                	-t tells docker to open a terminal
#                	-v mounts a volume
#                	-p route ports between the host and container
#
#
#   sudo docker run -it -v <local dir path>:<docker image dir path> <docker file>
#
# The ordering of flags and parameters matters. Put all the flags and parameters before the docker image is specified.
#
# Caveats:
#	* When using the -v flag, if you specify a path that already exists in the docker image via <path to create in image>,
#     then any files there will be overwritten, IF they exist in <path to local folder>.
#
#
# Examples
#
# Here I list the available images:
# [lyon@zeus ~]$ sudo docker images
# [sudo] password for lyon:
# REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
# my-docker-image        latest              19e0164251da        8 days ago          84.91 MB
# debian                 wheezy              34a0b91d4fe9        13 days ago         84.91 MB
# scienceguyrob/docker   latest              44ddec452b70        2 weeks ago         3.521 GB
# hello-world            latest              c54a2cc56cbb        4 months ago        1.848 kB
#
#
# Here I run my docker image:
# [lyon@zeus ~]$ sudo docker run -it scienceguyrob/docker
# root@3c6064b40e3b:~# ls
# DockerImageReadme.txt  psr
# root@3c6064b40e3b:~#
#
# You can see the image contains a text file and the folder psr in home.
#
# Documentation
# Here is the docker user guid: https://docs.docker.com/engine/userguide/
#
# And the docker command line reference: https://docs.docker.com/engine/reference/commandline/cli/