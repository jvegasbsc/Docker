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

RUN yum -y update && yum -y install \

    ##########################################################################################
    #
    # ALL THE SOFTWARE LISTED SO FAR IS REQUIRED TO BUILD TEMPO
    #
    ##########################################################################################

    # **************************************************
    #
    # OS tools
    #
    # **************************************************

    # VERSION: 6.18.01
    # LINK: https://pkgs.org/centos-7/centos-x86_64/tcsh-6.18.01-8.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # Tcsh is an enhanced but completely compatible version of csh, the C shell.
    tcsh \

    # VERSION: 6.0
    # LINK: https://pkgs.org/centos-7/centos-x86_64/unzip-6.0-15.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # The unzip utility is used to list, test, or extract files from a zip archive.
    unzip \

    # VERSION: 1.14
    # LINK: https://pkgs.org/centos-7/centos-x86_64/wget-1.14-10.el7_0.1.x86_64.rpm.html
    #
    # DESCRIPTION:
    # GNU Wget is a file retrieval utility which can use either the HTTP or FTP protocols.
    wget \

    # **************************************************
    #
    # Compilation / Dev tools
    #
    # **************************************************

    # VERSION: 2.8.11
    # LINK: https://pkgs.org/centos-7/centos-x86_64/cmake-2.8.11-5.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # CMake is used to control the software compilation process using simple platform
    # and compiler independent configuration files.
    make \

    # VERSION: 2.69
    # LINK: https://pkgs.org/centos-7/centos-x86_64/autoconf-2.69-11.el7.noarch.rpm.html
    #
    # DESCRIPTION:
    # GNU's Autoconf is a tool for configuring source code and Makefiles.
    autoconf \

    # VERSION: 1.13.4
    # LINK: https://pkgs.org/centos-7/centos-x86_64/automake-1.13.4-3.el7.noarch.rpm.html
    #
    # DESCRIPTION:
    # Automake is a tool for automatically generating `Makefile.in' files compliant with
    # the GNU Coding Standards.
    automake \

    # VERSION: 4.8.5
    # LINK: https://pkgs.org/centos-7/centos-x86_64/gcc-4.8.5-4.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # The gcc package contains the GNU Compiler Collection version 4.8. You'll need this
    # package in order to compile C code.
    gcc \

    # VERSION: 4.8.5
    # LINK: https://pkgs.org/centos-7/centos-x86_64/gcc-gfortran-4.8.5-4.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # The gcc-gfortran package provides support for compiling Fortran programs with the GNU
    # Compiler Collection.
    gcc-gfortran \

    ##########################################################################################
    #
    # ALL THE SOFTWARE LISTED SO FAR IS REQUIRED TO BUILD TEMPO 2
    #
    ##########################################################################################

    # VERSION: 2.4.2
    # LINK: https://pkgs.org/centos-7/centos-x86_64/libtool-ltdl-2.4.2-20.el7.i686.rpm.html
    #
    # DESCRIPTION:
    # The libtool-ltdl package contains the GNU Libtool Dynamic Module Loader, a library that
    # provides a consistent, portable interface which simplifies the process of using dynamic
    # modules.
    libtool \

    # VERSION: 4.8.5
    # LINK: https://pkgs.org/centos-7/centos-x86_64/gcc-c++-4.8.5-4.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # This package adds C++ support to the GNU Compiler Collection. It includes support for
    # most of the current C++ specification, including templates and exception handling.
    gcc-c++

##############################################################################################
# Install software repositories
##############################################################################################



##############################################################################################
# Setup environment variables
##############################################################################################

# Make the directory where software will be installed. Note the -p flag tells mkdir to
# also create parent directories as required.
RUN mkdir -p /home/psr/soft

# Define home, psrhome, OSTYPE
ENV HOME=/home
ENV PSRHOME=/home/psr/soft
ENV OSTYPE=linux

# Python packages
ENV PYTHONPATH=$HOME/ve/lib/python2.7/site-packages

# Tempo
ENV TEMPO=$PSRHOME/tempo
ENV PATH=$PATH:$PSRHOME/tempo/bin

# Tempo2
ENV TEMPO2=$PSRHOME/tempo2/T2runtime
ENV PATH=$PATH:$PSRHOME/tempo2/T2runtime/bin
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME/tempo2/T2runtime/include
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME/tempo2/T2runtime/lib

# SIGPROC
# These flags assist with the Sigproc compilation process, so do not remove them. If you take
# them out, then Sigproc will not build correctly.
ENV SIGPROC=$PSRHOME/sigproc
ENV PATH=$PATH:$SIGPROC/install/bin
ENV FC=gfortran
ENV F77=gfortran
ENV CC=gcc
ENV CXX=g++

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
RUN unzip Presto_SNAPSHOT_08_12_2016.zip -d /home/Presto_SNAPSHOT_08_12_2016
RUN unzip Sigproc_MJK_SNAPSHOT_08_12_2016.zip -d /home/Sigproc_MJK_SNAPSHOT_08_12_2016
RUN unzip Tempo_SNAPSHOT_08_12_2016.zip -d /home/Tempo_SNAPSHOT_08_12_2016
RUN unzip Tempo2_2016.11.3_SNAPSHOT_08_12_2016.zip -d /home/Tempo2_2016.11.3_SNAPSHOT_08_12_2016

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

RUN rm /home/psr/soft/sigproc/__MACOSX -R
RUN rm /home/psr/soft/presto/__MACOSX -R
RUN rm /home/psr/soft/tempo/__MACOSX -R
RUN rm /home/psr/soft/tempo2/__MACOSX -R

##############################################################################################
# TEMPO Installation
##############################################################################################
WORKDIR $PSRHOME/tempo
RUN ./prepare && \
    ./configure --prefix=$PSRHOME/tempo && \
    make && \
    make install && \
    mv obsys.dat obsys.dat_ORIGINAL && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/tempo/obsys.dat

##############################################################################################
# TEMPO2 Installation
##############################################################################################
# Ok here we install the latest version of TEMPO2.

WORKDIR $PSRHOME/tempo2
RUN ./bootstrap && \
    ./configure --x-libraries=/usr/lib/x86_64-linux-gnu --enable-shared --enable-static --with-pic F77=gfortran && \
    make && \
    make install && \
    make plugins-install
WORKDIR $PSRHOME/tempo2/T2runtime/observatory
RUN mv observatories.dat observatories.dat_ORIGINAL && \
    mv oldcodes.dat oldcodes.dat_ORIGINAL && \
    mv aliases aliases_ORIGINAL && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/tempo2/observatories.dat && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/tempo2/aliases



##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash