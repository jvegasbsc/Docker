# Docker
A docker test repository. This is a scratch space for my personal docker tests.

## Author: Rob Lyon
## Email : robert.lyon@manchester.ac.uk
## web   : www.scienceguyrob.com

### Overview
This repository consists of a whole bunch of docker files, written for a variety of purposes. So of these you may find
useful, others not so useful. Right now, there are three main types of image:

* **DSD**: Data science docker (DSD). This image has a basic data science stack that I use for everyday work. 
* **PulsarDSD**: This is an image that uses the Kern suite repository. It provides a small data science stack along with pulsar software.
* **music**: A data science docker which also contains music analysis software.

### DSD Docker File
This image has a basic data science stack that I use for everyday work. It is a CPU based image, i.e. does not use GPU resources.
 
#### OS Packages Installed

Image OS: Ubuntu 16.04
  
| Number |          Package            |
|--------|-----------------------------|
|1.      |  build-essential            |
|2.      |  wget                       |
|3.      |  unzip                      |
|4.      |  bzip2                      |
|5.      |  software-properties-common |
|6.      |  python-setuptools          |

#### Non-OS Software:
  
| Number |            Package            |
|--------|-------------------------------|
|1.      |  Java 8 (latest)              |
|2.      |  weka-3-9-1                   |
|3.      |  Anaconda (2-4.2.0)           |
|4.      |  Tensorflow (1.1.0, CPU only) |

#### Python Modules (Python 2.7):

| Number |        Package      |
|--------| --------------------|
|1.      | gcc                 |
|2.      | ephem               |
|3.      | astropy             |
|4.      | pip                 |
|5.      | pandas              |
|6.      | pillow              |
|7.      | numba               |
|8.      | mkl                 |
|9.      | theano              |
|10.     | pygpu               |
|11.     | javabridge          |
|12.     | xgboost             |
|13.     | python-weka-wrapper |

Also has a full scipy stack, numpy, matplotlib, jupyter notebooks etc (comes with Anaconda install).

### Pulsar DSD Docker File
This is an image that uses the Kern suite repository. It provides a small data science stack along with pulsar software.

#### OS Packages Installed

Image OS: Ubuntu 16.04, extends kernsuite/base:dev
  
| Number |          Package            |
|--------|-----------------------------|
|1.      |  wget                       |

#### Non-OS Software:
  
| Number |            Package            |
|--------|-------------------------------|
|1.      |  Anaconda (2-4.2.0)           |

#### Python Modules (Python 2.7):

Has a full scipy stack, numpy, matplotlib, jupyter notebooks etc (comes with Anaconda install).

#### Pulsar software

Latest versions of the pulsar software.

| Number |            Package            |
|--------|-------------------------------|
|1.      |  psrcat                       |
|2.      |  psrchive                     |
|3.      |  sigproc                      |
|4.      |  tempo                        |
|5.      |  tempo2                       |
|6.      |  dspsr                        |
|7.      |  presto                       |
|8.      |  python-presto                |

### Music Docker File
A data science docker which also contains music analysis software.
 
#### OS Packages Installed

Image OS: Ubuntu 16.04
  
| Number |          Package            |
|--------|-----------------------------|
|1.      |  build-essential            |
|2.      |  wget                       |
|3.      |  unzip                      |
|4.      |  bzip2                      |
|5.      |  software-properties-common |
|6.      |  python-setuptools          |
|7.      |  libasound-dev              |
|8.      |  portaudio19-dev            |
|9.      |  libportaudio2              |
|10.     |  libportaudiocpp0           |
|11.     |  ffmpeg                     |
|12.     |  libav-tools                |
|13.     |  libavcodec-extra           |

#### Non-OS Software:
  
| Number |            Package            |
|--------|-------------------------------|
|1.      |  Anaconda (2-4.2.0)           |
|2.      |  Tensorflow (1.1.0, CPU only) |

#### Python Modules (Python 2.7):

| Number |        Package      |
|--------| --------------------|
|1.      | gcc                 |
|2.      | ephem               |
|3.      | astropy             |
|4.      | pip                 |
|5.      | pandas              |
|6.      | pillow              |
|7.      | numba               |
|8.      | mkl                 |
|9.      | theano              |
|10.     | pygpu               |
|11.     | pydub               |
|12.     | xgboost             |
|13.     | ffmpeg              |
|14.     | librosa             |
|15.     | pyaudio             |

Also has a full scipy stack, numpy, matplotlib, jupyter notebooks etc (comes with Anaconda install).

### Docker Quick Start

Docker is basically a system that runs a virtualised software environment. You can in simple terms run an OS
within in OS (e.g. run a full linux kernel on your Mac). Docker is cool, and below I share the
Docker commands I use most often. These will help you get started. For more detailed help please see,

[Docker guide](https://docs.docker.com/engine/getstarted/)

[Docker site](https://www.docker.com)

[Docker User guide](https://docs.docker.com/engine/userguide/)

[Docker command line reference](https://docs.docker.com/engine/reference/commandline/cli/)


#### Docker Commands
To execute docker commands you usually use sudo. Below are the main docker commands available to you.

**LOGIN COMMAND** - This command logs you into the docker hub, which is useful if you want to
upload your own docker images. The dockerhub is an online system which hosts pre-built docker images.

  `sudo docker login`


**IMAGES COMMAND** - This command lists the docker images downloaded onto your host machine, available to run locally.

  `sudo docker images`


**BUILD COMMAND** - This builds the specified docker file on your host machine. Note the full
stop at the end - this is required and isn’t a typo.

  `sudo docker build -t <docker file> .`


**PUSH COMMAND** - This uploads the docker file built via the build command, to the dockerhub
(needs a dockerhub account and you must be logged in).

  `sudo docker push <docker file>`


**RMI COMMAND** - This deletes a local docker image. Useful if an image is faulty,
or disk space is low. To find the docker image id’s, just run the `sudo docker images command`.

  `sudo docker rmi -f <image id>`


**PULL COMMAND** - This pulls the specified docker image from the docker hub.

  `sudo docker pull <docker file>`


**RUN COMMAND** - This runs a docker image locally on your host machine. The -v flag tells
docker to mount a host file directory, inside the docker container. This allows you to copy files
between the host, and the virtualised docker container. Here are the main parameters:

* **-i** is the interactive flag
* **-t** tells docker to open a terminal
* **-v** mounts a volume
* **-p** route ports between the host and container


`sudo docker run -it -v <local dir path>:<docker image dir path> <docker file>`

The ordering of flags and parameters matters. Put all the flags and parameters before the docker image is specified.

**Caveats:**
When using the `-v` flag, if you specify a path that already exists in the docker image via `<path to create in image>`,
    then any files there will be overwritten, IF they exist in `<path to local folder>`.


**Examples**

Here I list the available images:

```[lyon@zeus ~]$ sudo docker images
[sudo] password for lyon:
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
my-docker-image        latest              19e0164251da        8 days ago          84.91 MB
debian                 wheezy              34a0b91d4fe9        13 days ago         84.91 MB
scienceguyrob/docker   latest              44ddec452b70        2 weeks ago         3.521 GB
hello-world            latest              c54a2cc56cbb        4 months ago        1.848 kB
```

Here I run my docker image:

```[lyon@zeus ~]$ sudo docker run -it scienceguyrob/docker
root@3c6064b40e3b:~ls
DockerImageReadme.txt  psr
root@3c6064b40e3b:~
```

### Running Jupyter (Pulsar DSD, DSD, Music)

To run jupyter in either the Kern, DSD or music containers, you have to pipe the notebook server output to your host machine.

You can use the following steps to achieve this.

1. Run the docker container with the command,

      `sudo docker run -i -t -p 9999:9999 scienceguyrob/kern`

2. Next you need to run your notebook (within the container) on the IP 0.0.0.0. to do this run the command,

      `jupyter notebook --notebook-dir=workspace --ip=0.0.0.0 --port=9999 --no-browser`

3. Now open `http://localhost:9999` in your browser to view your jupyter notebook.

You can load a workspace directory from your host machine into your container. For example I have
a path `/Volumes/data/Dropbox/Projects/Jupyter` which has my notebooks. If I run,

   `sudo docker run -i -t -p 9999:9999 scienceguyrob/kern -v /Volumes/data/Dropbox/Projects/Jupyter:/home/workspace`

I should see my notebooks when running:

`jupyter notebook --notebook-dir=workspace --ip=0.0.0.0 --port=9999 --no-browser`

Please be careful mounting volumes, as anything altered in your host folder will be changed.

### License

The code and the contents of this notebook are released under the GNU GENERAL PUBLIC LICENSE, Version 3, 29 June 2007. We kindly request that if you make use of the docker files here, you cite the work appropriately.