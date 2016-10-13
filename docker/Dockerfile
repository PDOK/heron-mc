FROM python:2.7

MAINTAINER Frank Steggink <frank.steggink@kadaster.nl>

# Set Dutch timezone: it is assumed that most users of BAG-extract will be executed from the Netherlands
ENV TZ Europe/Amsterdam

# Enable ls colors/aliases
RUN sed -i '9 s/^# //' ~/.bashrc
RUN sed -i '11,13 s/^# //' ~/.bashrc

# Add keyserver for OpenJDK
RUN echo "deb http://ftp.de.debian.org/debian jessie-backports main" >> /etc/apt/sources.list
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3B4FE6ACC0B21F32

# Install utilities
RUN apt-get update

# Install OpenJDK 8 JDK, needed for Ant
RUN apt-get install -y openjdk-8-jdk

# Install ant
RUN apt-get install -y ant

# Install rsync
RUN apt-get install -y rsync

# Install zip
RUN apt-get install -y zip

# Add initialization files
ADD init/ /init/

# Install Python dependencies
RUN chmod +x /init/pip-install.sh
RUN /init/pip-install.sh

# Add a volume to exchange data
VOLUME /heron-mc

# Start a bash shell
CMD ["/bin/bash"]
