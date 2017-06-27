##############################################################################################
# Purpose   : Dockerize Components of a simple data science stack (GPU version).
# Python    : 2.7
# Version   : 0.1
# Author    : Rob Lyon (robert.lyon@manchester.ac.uk)
##############################################################################################
#
# This docker file will setup an environment with only a basic data science stack.
#
# See this Github repo for more information: https://github.com/floydhub/dl-docker
#
# https://github.com/ContinuumIO/docker-images/blob/master/anaconda/Dockerfile
##############################################################################################

# Use well supported Ubuntu distribution.
FROM floydhub/dl-docker:gpu_temp

RUN pip install --upgrade pip

RUN pip install http://download.pytorch.org/whl/cu80/torch-0.1.12.post2-cp27-none-linux_x86_64.whl
RUN pip install torchvision

# Define the command that will be exectuted when docker runs the container.
WORKDIR /home
ENTRYPOINT /bin/bash