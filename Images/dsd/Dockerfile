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
    numba \
    mkl \
    theano \
    pygpu

RUN conda update scikit-learn
RUN conda install -c anaconda javabridge -y
RUN conda update conda
RUN pip install xgboost
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