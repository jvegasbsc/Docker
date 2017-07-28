##############################################################################################
# Purpose   : Kern build
# Python    : 2.7
# Version   : 0.1
# Author    : Rob Lyon (robert.lyon@manchester.ac.uk)
##############################################################################################
#
# This docker file will setup an environment with only a basic pulsar / data science stack.
# See http://kernsuite.info for more information on the Kern packages.
#
# Update: 27/07/2017
#
# After finding an error in the Kern install of DSPSR, we removed this from the file. We then
# manually installed these tools.
#
##############################################################################################

# Use the kern distribution...
#FROM kernsuite/base:dev
FROM ubuntu:16.04


RUN apt-get update -y && apt-get install -y \
    software-properties-common \
    python-software-properties  \
    python-pip \
    wget

RUN add-apt-repository -y ppa:kernsuite/kern-dev
RUN add-apt-repository -y -y multiverse

# upgrade the pip package to the latest version
RUN pip install --upgrade pip


# The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT,
# COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t
# exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.
WORKDIR /home

# Now create some diectories to store files and scripts
RUN mkdir soft

# Change to the soft (aka software directory).
WORKDIR /home/soft

# Now In install Anaconda to manage the python environment. This installs numpy, scipy, matplotlib,
# jupyter etc. Type conda list inside the built docker container, to view the python modules already
# installed.
RUN wget https://repo.continuum.io/archive/Anaconda2-4.2.0-Linux-x86_64.sh
RUN bash Anaconda2-4.2.0-Linux-x86_64.sh -b -p /home/soft/anaconda
RUN rm Anaconda2-4.2.0-Linux-x86_64.sh
ENV PATH /home/soft/anaconda/bin:$PATH

RUN conda update conda

# Update cahce
RUN apt-get -qq update

WORKDIR /home
RUN mkdir workspace

# Borrowed this List from Ewan Barr's docker file (https://hub.docker.com/r/mpifrpsr/dspsr/~/dockerfile/),
# however we did already have the list of prequisites, after previously building an Ubuntu based docker
# image for pulsar search tools :)
RUN apt-get --no-install-recommends -y install \
    build-essential \
    autoconf \
    autotools-dev \
    automake \
    autogen \
    libtool \
    pkg-config \
    cmake \
    csh \
    gcc \
    gfortran \
    git \
    cvs \
    libcfitsio-dev \
    pgplot5 \
    swig2.0 \
    hwloc \
    python-dev \
    libfftw3-3 \
    libfftw3-bin \
    libfftw3-dev \
    libfftw3-single3 \
    libx11-dev \
    libpng12-dev \
    libpng3 \
    libpnglite-dev \
    libhdf5-10 \
    libhdf5-cpp-11 \
    libhdf5-dev \
    libhdf5-serial-dev \
    libxml2 \
    libxml2-dev \
    libltdl-dev \
    gsl-bin \
    libgsl-dev \
    libgsl2

# Installs the Kern suite components individually. Up to here, the Kern repository is only
# enbaled, not installed. So you have to specify the packages you want. There is a list here:
# https://launchpad.net/~kernsuite/+archive/ubuntu/kern-2
# Note that some packages need to be installed in a specfic order to work. Also, it doesn't seem
# that Kern doesn't set up environment variables for some tools.
RUN apt-get install -y psrcat
RUN apt-get install -y wget
RUN apt-get install -y tempo
RUN apt-get install -y tempo2
RUN apt-get install -y sigproc
RUN apt-get install -y presto python-presto

# These two appear broken in Kern.
#RUN docker-apt-install psrchive
#RUN docker-apt-install dspsr


WORKDIR /home/soft

ENV PSRHOME=/home/soft

# Obtain software...
RUN git clone git://git.code.sf.net/p/dspsr/code dspsr && \
    git clone https://github.com/SixByNine/psrxml.git && \
    git clone git://git.code.sf.net/p/psrchive/code psrchive


# PSRCHIVE
ENV PSRCHIVE $PSRHOME/psrchive
ENV PATH $PATH:$PSRCHIVE/install/bin
ENV C_INCLUDE_PATH $C_INCLUDE_PATH:$PSRCHIVE/install/include
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$PSRCHIVE/install/lib
ENV PYTHONPATH $PSRCHIVE/install/lib/python2.7/site-packages

WORKDIR $PSRCHIVE
RUN ./bootstrap
RUN ./configure --prefix=$PSRCHIVE/install
RUN make && \
    make install

# PSRXML
ENV PSRXML $PSRHOME/psrxml
ENV PATH $PATH:$PSRXML/install/bin
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$PSRXML/install/lib
ENV C_INCLUDE_PATH $C_INCLUDE_PATH:$PSRXML/install/include
WORKDIR $PSRXML

RUN autoreconf --install --warnings=none
RUN ./configure --prefix=$PSRXML/install && \
    make && \
    make install

# DSPSR
ENV DSPSR $PSRHOME/dspsr
ENV PATH $PATH:$DSPSR/install/bin
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$DSPSR/install/lib
ENV C_INCLUDE_PATH $C_INCLUDE_PATH:$DSPSR/install/include
WORKDIR $DSPSR
RUN ./bootstrap && \
    echo "bpsr dummy fits puma2 sigproc ska1" > backends.list


RUN ./configure --prefix=$DSPSR/install
RUN make
RUN make install

##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash

# Install git, autoconf, libtool
#   git clone https://git.code.sf.net/p/dspsr/code dspsr
# ./bootstrap