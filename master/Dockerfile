# Docker image for OE-lite.org buildbot master
FROM ubuntu:14.04
MAINTAINER Esben Haabendal <esben@haabendal.dk>

# Install buildbot package dependencies
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get install -y \
	python-pip python-dev git sudo \
	libffi-dev libssl-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install buildbot and required dependencies
RUN git clone http://github.com/esben/buildbot -b github-pr-base && \
    cd buildbot/master && ./setup.py install

# Install optional buildbot dependencies
RUN pip install txgithub service_identity

# Install setup and start scripts and buildbot configuration
COPY assets /srv/setup/

# Setup buildbot user
RUN groupadd -g 666 -r buildbot \
 && useradd -u 666 -g buildbot -r -m -d /srv/buildbot -s /bin/bash buildbot \
 && chown -R buildbot:buildbot /srv/setup \
 && install -o buildbot -g buildbot -d /srv/buildbot/master
USER buildbot
WORKDIR /srv/buildbot

# Buildbot master dir in volume.  Either mount it from a data volume
# container, from host directory, or just let it stay in the buildbot-master
# container.
VOLUME /srv/buildbot/master

# Setup whatever is possible to do at image build time (not much...)
RUN /srv/setup/setup.sh

CMD ["./start.sh"]

# Exposed ports
EXPOSE 8010 9989
