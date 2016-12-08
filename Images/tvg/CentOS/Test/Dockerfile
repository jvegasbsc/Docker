##############################################################################################
# Purpose   : Dockerize Components of Pulsar software stack.
# Python    : 2.7
# Version   : 0.1
# Author    : Rob Lyon (robert.lyon@manchester.ac.uk)
##############################################################################################
# Based upon original script written by Casey Law (caseyjlaw@gmail.com) and
# Maciej Serylak (mserylak@ska.ac.za).
#
# This docker file will setup an environment with only a basic pulsar stack. This
# is because the image is to be used for test vector generation, for tests of SKA
# SDP and CSP software. For a more complete pulsar image, look at the Dockerfile's
# written by Casey Law, or Maciej Serylak. I've documented this Dockerfile which I
# hope makes its content a lot easier to understand.
#
# SOFTWARE:
#
# psarchive
# tempo2
# fast_fake
# inject_pulsar
# pgplot
# python 2.6 (scipy, numpy stacks).
# fftw 2 and 3
# dev tools (gfortran etc.)
#
#
##############################################################################################

# Use well supported distribution.
FROM centos:7

# Contact me for help!
MAINTAINER robert.lyon@manchester.ac.uk

# The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT,
# COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t
# exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.
WORKDIR /home

##############################################################################################
# Install 'OS' software.
##############################################################################################

# Run due to CentOS bug.
RUN yum groups mark convert

# Now run repository setup and group software install.
RUN yum group list
RUN yum -y install epel-release
RUN yum repolist
RUN yum -y groupinstall "Development Tools"

RUN yum -y update && yum -y install \

    # **************************************************
    #
    # OS tools
    #
    # **************************************************

    # VERSION: 7.1.0
    # LINK: https://pkgs.org/centos-7/epel-x86_64/python-pip-7.1.0-1.el7.noarch.rpm.html
    #
    # DESCRIPTION:
    # Pip is a replacement for 'easy_install'.
    python-pip \
    python-devel \

    # VERSION: 6.18.01
    # LINK: https://pkgs.org/centos-7/centos-x86_64/tcsh-6.18.01-8.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # Tcsh is an enhanced but completely compatible version of csh, the C shell.
    tcsh \

    # VERSION: 1.14
    # LINK: https://pkgs.org/centos-7/centos-x86_64/wget-1.14-10.el7_0.1.x86_64.rpm.html
    #
    # DESCRIPTION:
    # GNU Wget is a file retrieval utility which can use either the HTTP or FTP protocols.
    wget

##############################################################################################
# Install software repositories
##############################################################################################

WORKDIR /home
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/CentOS/springdale-computational.repo
RUN mv springdale-computational.repo /etc/yum.repos.d/springdale-computational.repo

WORKDIR /home

RUN yum -y update
# springdale-computational

# Install python modules
RUN pip install --upgrade pip
RUN pip install pip -U
RUN pip install setuptools -U
RUN pip install numpy -U
RUN pip install scipy -U
RUN pip install fitsio -U
RUN pip install astropy -U
RUN pip install astroplan -U
RUN pip install pyfits -U
RUN pip install matplotlib -U
RUN pip install pyephem -U

##############################################################################################
# Setup environment variables
##############################################################################################

ENV PSRHOME=/home/psr/soft

# Make the directory where software will be installed. Note the -p flag tells mkdir to
# also create parent directories as required.
RUN mkdir -p /home/psr/soft

# Define home, psrhome, software, OSTYPE
ENV HOME=/home
ENV OSTYPE=linux

# Python packages
ENV PYTHONPATH=$HOME/ve/lib/python2.7/site-packages

##############################################################################################
# TEST VECTOR PIPELINE + README Setup
##############################################################################################
# Download test vector files
WORKDIR /home
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Images/tvg/CentOS/DockerImageReadme.txt

WORKDIR $PSRHOME
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/pulsar_injection_pipeline.zip
RUN unzip pulsar_injection_pipeline.zip -d $PSRHOME
RUN rm __MACOSX -R
RUN rm pulsar_injection_pipeline.zip

##############################################################################################
# PULSAR SOFTWARE PIPELINE
##############################################################################################
WORKDIR /home

# Downloading pulsar software source codes. Here I've tried to version the software
# used, so we know all about our container environment. However some of the pulsar tools
# are not versioned explcitly. So to make sure we always have the same version of the
# pulsar tools in each image, versions of the software have been packaged and stored,
# in my github repository. It is these versions of the software that will be installed
# each time.

# Download the software
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/Software/08_12_2016/Presto_SNAPSHOT_08_12_2016.zip
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/Software/08_12_2016/Sigproc_MJK_SNAPSHOT_08_12_2016.zip
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/Software/08_12_2016/Tempo_SNAPSHOT_08_12_2016.zip
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/Software/08_12_2016/Tempo2_2016.11.3_SNAPSHOT_08_12_2016.zip

# Unzip the software
RUN unzip Presto_SNAPSHOT_08_12_2016.zip -d /home
RUN unzip Sigproc_MJK_SNAPSHOT_08_12_2016.zip -d /home
RUN unzip Tempo_SNAPSHOT_08_12_2016.zip -d /home
RUN unzip Tempo2_2016.11.3_SNAPSHOT_08_12_2016.zip -d /home

# Remove any meta files caused by building zip files on OSX.
RUN rm __MACOSX -R

# Remove zip files
RUN rm Presto_SNAPSHOT_08_12_2016.zip
RUN rm Sigproc_MJK_SNAPSHOT_08_12_2016.zip
RUN rm Tempo_SNAPSHOT_08_12_2016.zip
RUN rm Tempo2_2016.11.3_SNAPSHOT_08_12_2016.zip

# Move the software to the correct folder location
RUN mv /home/Sigproc_MJK_SNAPSHOT_08_12_2016 /home/psr/soft/sigproc
RUN mv /home/Presto_SNAPSHOT_08_12_2016 /home/psr/soft/presto
RUN mv /home/Tempo_SNAPSHOT_08_12_2016 /home/psr/soft/tempo
RUN mv /home/Tempo2_2016.11.3_SNAPSHOT_08_12_2016 /home/psr/soft/tempo2

##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash