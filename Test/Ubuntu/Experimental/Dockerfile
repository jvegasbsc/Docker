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
# fftw
# dev tools (gfortran etc.)
##############################################################################################

# Use well supported Ubuntu distribution. Note that a newer version of Ubunutu
# is available (16.04.1). See the docker site for more information.
FROM ubuntu:14.04.4

# Contact me for help!
MAINTAINER robert.lyon@manchester.ac.uk

# The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT,
# COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t
# exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.
WORKDIR /home
ENV PSRHOME=/home/psr/soft

# Make the directory where software will be installed. Note the -p flag tells mkdir to
# also create parent directories as required.
RUN mkdir -p /home/psr/soft

# As part of its operation, the Apt tool uses a file that lists the 'sources' from which
# sofotware packages can be obtained. This file can be found at: /etc/apt/sources.list.
# The call below simply updates the sources file, so that software not licensed under the
# GPL (or similar license) can be downloaded via Apt to the image.
RUN echo 'deb http://us.archive.ubuntu.com/ubuntu trusty main multiverse' >> /etc/apt/sources.list

# According to the docker reference documentation,  which can be found online at:
#   https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/
#
# You should avoid RUN apt-get upgrade or dist-upgrade, as many of the “essential” packages from
# the base images won’t upgrade inside an unprivileged container. If you know there’s a particular
# package, foo, that needs to be updated, use apt-get install -y foo to update automatically. The
# documentation suggests that you should always combine RUN apt-get update, with apt-get install
# in the same RUN statement. We do this below to install the main software packages we require.
# Note that the -y flag used below causes Apt to responde with an automatic yes to prompts. This
# allows the commands to run non-interactively.
RUN apt-get update -y && apt-get install -y \
    build-essential \
    autoconf \
    libtool \
    csh \
    git \
    cvs \
    fort77 \
    gcc \
    g++ \
    gfortran \
    wget

# These flags assist with the Sigproc comilation process, so do not remove them. If you take
# them out, then Sigproc will not build correctly.
ENV FC gfortran
ENV F77 gfortran
ENV CC gcc
ENV CXX g++

# Downloading all source codes
RUN git clone https://github.com/SixByNine/sigproc.git
RUN mv /home/sigproc /home/psr/soft/sigproc
RUN cvs -z3 -d:pserver:anonymous@tempo2.cvs.sourceforge.net:/cvsroot/tempo2 co tempo2
RUN mv /home/tempo2 /home/psr/soft/tempo2

WORKDIR $PSRHOME
RUN wget http://www.imcce.fr/fr/presentation/equipes/ASD/inpop/calceph/calceph-2.2.4.tar.gz && \
    tar -xvvf calceph-2.2.4.tar.gz -C $PSRHOME

##############################################################################################
# calceph Installation
##############################################################################################
# Ok here we install calceph.
#ENV PATH $PATH:$PSRHOME/calceph-2.2.4/install/bin
#ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$PSRHOME/calceph-2.2.4/install/lib
#ENV C_INCLUDE_PATH $C_INCLUDE_PATH:$PSRHOME/calceph-2.2.4/install/include
#WORKDIR $PSRHOME/calceph-2.2.4
#RUN ./configure --prefix=$PSRHOME/calceph-2.2.4/install --with-pic --enable-shared --enable-static --enable-fortran --enable-thread && \
#    make && \
#    make check && \
#    make install

##############################################################################################
# Sigproc Installation
##############################################################################################
# Ok here we install sigproc - This is Mike Keith's version of Sigproc, which comes with the
# fast_fake utility. First we set the environment variables for the install, then execute the
# building steps.
ENV SIGPROC $PSRHOME/sigproc
ENV PATH $PATH:$SIGPROC/install/bin
WORKDIR $SIGPROC
RUN ./bootstrap && \
    ./configure --prefix=$SIGPROC/install && \
    make && \
    make install

##############################################################################################
# TEMPO2 Installation
##############################################################################################
# Ok here we install the latest version of TEMPO2.
#ENV TEMPO $PSRHOME/tempo
#ENV PATH $PATH:$PSRHOME/tempo/bin
#WORKDIR $PSRHOME/tempo
#RUN ./prepare && \
#    ./configure --prefix=$PSRHOME/tempo && \
#    make && \
#    make install && \
#    mv obsys.dat obsys.dat_ORIGINAL && \
#    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/obsys.dat

ENV TEMPO2 $PSRHOME/tempo2/T2runtime
ENV PATH $PATH:$PSRHOME/tempo2/T2runtime/bin
ENV C_INCLUDE_PATH $C_INCLUDE_PATH:$PSRHOME/tempo2/T2runtime/include
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:$PSRHOME/tempo2/T2runtime/lib
WORKDIR $PSRHOME/tempo2
RUN sync && perl -pi -e 's/chmod \+x/#chmod +x/' bootstrap # Get rid of: returned a non-zero code: 126.
RUN ./bootstrap && \
    ./configure --x-libraries=/usr/lib/x86_64-linux-gnu --enable-shared --enable-static --with-pic F77=gfortran && \
    make && \
    make install && \
    make plugins-install
WORKDIR $PSRHOME/tempo2/T2runtime/observatory
RUN mv observatories.dat observatories.dat_ORIGINAL && \
    mv oldcodes.dat oldcodes.dat_ORIGINAL && \
    mv aliases aliases_ORIGINAL && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/observatories.dat && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/oldcodes.dat && \
    wget https://raw.githubusercontent.com/mserylak/pulsar_docker/master/aliases


##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash