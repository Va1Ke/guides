FROM ubuntu:20.04

EXPOSE 26656
EXPOSE 26657
EXPOSE 9091
EXPOSE 9090
EXPOSE 6060
EXPOSE 1317

# Copying other directories in docker container

RUN useradd -r validator
USER validator

RUN apt-get update

# Installing development tools
RUN apt-get install -y git

RUN apt-get install -y curl

RUN apt install -y nano

RUN apt install -y tmux



## Installing Go

RUN wget https://golang.org/dl/go1.19.2.linux-amd64.tar.gz

RUN sudo tar -C /usr/local -xzf go1.19.2.linux-amd64.tar.gz

RUN npm install -g npm@6

## Export

RUN GOROOT=/usr/local/go

RUN GOPATH=$HOME/go

RUN GO111MODULE=on

RUN PATH=$PATH:/usr/local/go/bin:$HOME/go/bin

## Update Your Profile

RUN source ~/.profile

# For Other Steps Read Readme.md


