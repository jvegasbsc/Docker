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
RUN yum groups mark convert
RUN yum group list
RUN yum -y update && yum -y install \

    # **************************************************
    #
    # Install main tools:
    #
    # **************************************************
    groupinstall "Development Tools" \
    groupinstall "X11" \

    # **************************************************
    #
    # OS tools
    #
    # **************************************************

    # VERSION: bc-1.06.95-13.el7.x86_64.rpm
    # LINK: https://pkgs.org/download/bc
    #
    # DESCRIPTION:
    # The bc package includes bc and dc. Bc is an arbitrary precision numeric processing
    # arithmetic language. Dc is an interactive arbitrary precision stack based calculator,
    # which can be used as a text mode calculator.
    bc \

    # VERSION: 2.8.11
    # LINK: https://pkgs.org/centos-7/centos-x86_64/cmake-2.8.11-5.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # CMake is used to control the software compilation process using simple platform and
    # compiler independent configuration files. CMake generates native makefiles and workspaces
    # that can be used in the compiler environment of your choice.
    cmake \                

    # VERSION: 2.2.0.3
    # LINK: https://pkgs.org/centos-7/epel-x86_64/dkms-2.2.0.3-34.git.9e0394d.el7.noarch.rpm.html
    #
    # DESCRIPTION:
    # This package contains the framework for the Dynamic Kernel Module Support (DKMS) method for
    # installing module RPMS as originally developed by Dell.
    dkms \

    # VERSION: 2.0.2
    # LINK: https://pkgs.org/centos-7/epel-x86_64/htop-2.0.2-1.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # htop is an interactive text-mode process viewer for Linux, similar to top.
    htop \

    # VERSION: 1.7
    # LINK: https://pkgs.org/centos-7/centos-x86_64/hwloc-1.7-5.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # The Portable Hardware Locality (hwloc) software package provides a portable
    # abstraction (across OS, versions, architectures, ...) of the hierarchical
    # topology of modern architectures, including NUMA memory nodes, shared caches,
    # processor sockets, processor cores and processing units (logical processors or
    # "threads"). It also gathers various system attributes such as cache and memory
    # information. It primarily aims at helping applications with gathering information
    # about modern computing hardware so as to exploit it accordingly and efficiently.
    hwloc \

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
    wget \

    # **************************************************
    #
    # Fortran
    #
    # **************************************************

    # VERSION: 4.8.5
    # LINK: https://pkgs.org/centos-7/centos-x86_64/gcc-gfortran-4.8.5-4.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # The gcc-gfortran package provides support for compiling Fortran programs with the
    # GNU Compiler Collection.
    gcc-gfortran \

    # VERSION: 3.4.6
    # LINK: https://pkgs.org/centos-7/centos-x86_64/compat-libf2c-34-3.4.6-32.el7.i686.rpm.html
    #
    # DESCRIPTION:
    # This package contains Fortran 77 shared library which is needed to run Fortran 77
    # dynamically linked programs built by g77 3.4.x.
    compat-libf2c-34 \     

    # **************************************************
    #
    # C / C++
    #
    # **************************************************

    # VERSION: 4.8.5
    # LINK: https://pkgs.org/centos-7/centos-x86_64/cpp-4.8.5-4.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # Cpp is the GNU C-Compatible Compiler Preprocessor. Cpp is a macro processor
    # which is used automatically by the C compiler to transform your program before
    # actual compilation.
    cpp \

    # VERSION: 0.19
    # LINK: https://pkgs.org/centos-7/centos-x86_64/Cython-0.19-3.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # This is a development version of Pyrex, a language for writing Python extension modules.
    Cython \

    # **************************************************
    #
    # Pulsar software dependencies
    #
    # **************************************************

    # VERSION: 2.1.5
    # LINK: https://pkgs.org/centos-7/epel-x86_64/fftw2-2.1.5-26.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # FFTW is a C subroutine library for computing the Discrete Fourier Transform (DFT)
    # in one or more dimensions, of both real and complex data, and of arbitrary input size.
    fftw2 \

    # VERSION: 2.1.5
    # LINK: https://pkgs.org/centos-7/epel-x86_64/fftw2-devel-2.1.5-26.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # This package contains header files and development libraries needed to develop programs
    # using the FFTW fast Fourier transform library version 2.
    fftw2-devel \

    # VERSION: 4.6.2
    # LINK: https://pkgs.org/centos-7/centos-x86_64/gnuplot-4.6.2-3.el7.x86_64.rpm.html
    #
    # DESCRIPTION:
    # Gnuplot is a command-line driven, interactive function plotting program especially suited
    # for scientific data representation.
    gnuplot\

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

    # VERSION:
    # LINK:
    #
    # DESCRIPTION:

##############################################################################################
# Install software repositories
##############################################################################################

WORKDIR /home
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/CentOS/springdale-computational.repo
RUN mv springdale-computational.repo /etc/yum.repos.d/springdale-computational.repo
RUN rm __MACOSX -R

WORKDIR /home

RUN yum -y update && yum -y install \
    springdale-computational















# Install python modules
RUN pip install pip -U && \
    pip install setuptools -U && \
    pip install numpy -U && \
    pip install scipy -U && \
    pip install fitsio -U && \
    pip install astropy -U && \
    pip install astroplan -U && \
    pip install pyfits -U && \
    pip install matplotlib -U && \
    pip install pyephem -U

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

# PGPLOT
ENV PGPLOT_DIR=/usr/lib/pgplot5
ENV PGPLOT_FONT=/usr/lib/pgplot5/grfont.dat
ENV PGPLOT_INCLUDES=/usr/include
ENV PGPLOT_BACKGROUND=white
ENV PGPLOT_FOREGROUND=black
ENV PGPLOT_DEV=/xs

# psrcat
ENV PSRCAT_FILE=$PSRHOME/psrcat_tar/psrcat.db
ENV PATH=$PATH:$PSRHOME/psrcat_tar

# tempo
ENV TEMPO=$PSRHOME/tempo
ENV PATH=$PATH:$PSRHOME/tempo/bin

# tempo2
ENV TEMPO2=$PSRHOME/tempo2/T2runtime
ENV PATH=$PATH:$PSRHOME/tempo2/T2runtime/bin
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRHOME/tempo2/T2runtime/include
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRHOME/tempo2/T2runtime/lib

# PSRCHIVE
ENV PSRCHIVE=$PSRHOME/psrchive
ENV PATH=$PATH:$PSRCHIVE/install/bin
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$PSRCHIVE/install/include
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PSRCHIVE/install/lib
ENV PYTHONPATH=$PYTHONPATH:$PSRCHIVE/install/lib/python2.7/site-packages

# SIGPROC
# These flags assist with the Sigproc comilation process, so do not remove them. If you take
# them out, then Sigproc will not build correctly.
ENV SIGPROC=$PSRHOME/sigproc
ENV PATH=$PATH:$SIGPROC/install/bin
ENV FC=gfortran
ENV F77=gfortran
ENV CC=gcc
ENV CXX=g++

# sigpyproc
ENV SIGPYPROC=$PSRHOME/sigpyproc
ENV PYTHONPATH=$PYTHONPATH:$SIGPYPROC/lib/python
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$SIGPYPROC/lib/c

# DSPSR
ENV DSPSR=$PSRHOME/dspsr
ENV PATH=$PATH:$DSPSR/install/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DSPSR/install/lib
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$DSPSR/install/include

# clig
ENV CLIG=$PSRHOME/clig
ENV PATH=$PATH:$CLIG/instal/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CLIG/instal/lib

# CLooG
ENV CLOOG=$PSRHOME/cloog-0.18.4
ENV PATH=$PATH:$CLOOG/install/bin
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$CLOOG/install/include
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$CLOOG/install/lib

# Ctags
ENV CTAGS=$PSRHOME/ctags-5.8
ENV PATH=$PATH:$CTAGS/install/bin

# FFTW2
ENV FFTW2=$PSRHOME/fftw-2.1.5
ENV C_INCLUDE_PATH=$C_INCLUDE_PATH:$FFTW2/install/include
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$FFTW2/install/lib

# PRESTO
ENV PRESTO=$PSRHOME/presto
ENV PATH=$PATH:$PRESTO/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$PRESTO/lib
ENV PYTHONPATH=$PYTHONPATH:$PRESTO/lib/python

# Downloading all source codes
RUN git clone https://github.com/SixByNine/sigproc.git
RUN git clone https://github.com/scottransom/presto.git
RUN git clone git://git.code.sf.net/p/tempo/tempo
RUN mv /home/sigproc /home/psr/soft/sigproc
RUN mv /home/presto /home/psr/soft/presto
RUN mv /home/tempo /home/psr/soft/tempo
RUN cvs -z3 -d:pserver:anonymous@tempo2.cvs.sourceforge.net:/cvsroot/tempo2 co tempo2
RUN mv /home/tempo2 /home/psr/soft/tempo2


##############################################################################################
# CUDA Drivers
#
# This part of the docker file was taken from the Nvidia Github respository, credit goes
# to the NVIDIA CORPORATION <digits@nvidia.com> for this. Please see the following link
# for more information: https://github.com/NVIDIA/nvidia-docker
##############################################################################################



##############################################################################################
# TEST VECTOR PIPELINE + README Setup
##############################################################################################
# Download test vector files
WORKDIR /home
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/DockerImageReadme.txt

WORKDIR $PSRHOME
RUN wget https://github.com/scienceguyrob/Docker/raw/master/Resources/Deploy/pulsar_injection_pipeline.zip
RUN unzip pulsar_injection_pipeline.zip -d $PSRHOME
RUN rm __MACOSX -R
RUN rm pulsar_injection_pipeline.zip

##############################################################################################
# Finally...
##############################################################################################
# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash