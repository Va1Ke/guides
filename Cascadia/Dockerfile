FROM ubuntu:20.04

# Copying other directories in docker container

COPY . .

RUN apt-get update

# Installing development tools
RUN apt-get install -y git

RUN apt-get install -y curl

RUN apt install -y nano

RUN apt install -y tmux

# Installing Node

RUN sudo apt update && sudo apt upgrade -y

RUN sudo apt install make build-essential gcc git


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

## Build Cascadia from source
RUN curl -L https://github.com/CascadiaFoundation/cascadia/releases/download/v0.1.1/cascadiad-v0.1.1-linux-amd64 -o cascadiad

RUN sudo chmod u+x cascadiad

RUN sudo cp cascadiad /usr/local/bin/cascadiad

# For Other Steps Read Readme.md

