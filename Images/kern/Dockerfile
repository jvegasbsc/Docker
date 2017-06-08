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
##############################################################################################

# Use the kern distribution...
FROM kernsuite/base:dev

# The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT,
# COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t
# exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.
WORKDIR /home

# Installs the Kern suite components individually. Up to here, the Kern repository is only
# enbaled, not installed. So you have to specify the packages you want. There is a list here:
# https://launchpad.net/~kernsuite/+archive/ubuntu/kern-2
# Note that some packages need to be installed in a specfic order to work. Also, it doesn't seem
# that Kern doesn't set up environment variables for some tools.
RUN docker-apt-install wget
RUN docker-apt-install psrcat
RUN docker-apt-install psrchive
RUN docker-apt-install sigproc
RUN docker-apt-install tempo
RUN docker-apt-install tempo2
RUN docker-apt-install dspsr
RUN docker-apt-install presto python-presto

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

WORKDIR /home
RUN mkdir workspace

##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash