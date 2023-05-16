FROM ubuntu:20.04

EXPOSE 26656
EXPOSE 26657
EXPOSE 9091
EXPOSE 9090
EXPOSE 6060
EXPOSE 1317

RUN apt-get update && \
      apt-get -y install sudo

RUN useradd -m docker && echo "docker:docker" | chpasswd && adduser docker sudo

USER docker
CMD /bin/bash