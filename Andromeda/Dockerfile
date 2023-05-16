FROM ubuntu:20.04

EXPOSE 26656
EXPOSE 26657
EXPOSE 9091
EXPOSE 9090
EXPOSE 6060
EXPOSE 1317

RUN useradd docker && echo "docker:docker" | chpasswd
RUN mkdir -p /home/docker && chown -R docker:docker /home/docker

USER docker
CMD /bin/bash


