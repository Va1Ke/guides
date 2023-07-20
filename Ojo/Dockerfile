FROM ubuntu:20.04

EXPOSE 15057
EXPOSE 26658
EXPOSE 15058
EXPOSE 26657
EXPOSE 6060
EXPOSE 15060
EXPOSE 26656
EXPOSE 15056
EXPOSE 15066
EXPOSE 26660
EXPOSE 1317
EXPOSE 15017
EXPOSE 8080
EXPOSE 15080
EXPOSE 15090
EXPOSE 9091
EXPOSE 15091

RUN apt-get update && \
      apt-get -y install sudo

RUN useradd -r validator
USER validator

RUN apt-get update

# Installing development tools
RUN apt-get install -y git

RUN apt-get install -y curl

RUN apt install -y nano

RUN apt install -y tmux

RUN apt install wget

RUN sudo apt -q update

RUN sudo apt -qy install curl git jq lz4 build-essential

RUN sudo apt -qy upgrade
# Installing Node

RUN sudo apt update && sudo apt upgrade -y

RUN sudo apt install make build-essential gcc git



## Installing Go

RUN sudo rm -rf /usr/local/go

RUN curl -Ls https://go.dev/dl/go1.19.10.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local

RUN eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)

RUN eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)


## Clonning project repository and build

RUN cd $HOME
RUN rm -rf ojo
RUN git clone https://github.com/ojo-network/ojo.git
RUN cd ojo
RUN git checkout v0.1.2

RUN make build
## Prepare binaries for Cosmovisor

RUN mkdir -p $HOME/.ojo/cosmovisor/genesis/bin
RUN mv build/ojod $HOME/.ojo/cosmovisor/genesis/bin/
RUN rm -rf build


## Creating application symlinks
RUN sudo ln -s $HOME/.ojo/cosmovisor/genesis $HOME/.ojo/cosmovisor/current -f
RUN sudo ln -s $HOME/.ojo/cosmovisor/current/bin/ojod /usr/local/bin/ojod -f

## Installing Cosmovisor and create a service

RUN go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
RUN sudo tee /etc/systemd/system/ojod.service > /dev/null << EOF
[Unit]
Description=ojo-testnet node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.ojo"
Environment="DAEMON_NAME=ojod"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.ojo/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable ojod

## Initialize the node

RUN curl -Ls https://snapshots.kjnodes.com/ojo-testnet/genesis.json > $HOME/.ojo/config/genesis.json
RUN curl -Ls https://snapshots.kjnodes.com/ojo-testnet/addrbook.json > $HOME/.ojo/config/addrbook.json

### Add seeds
RUN sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@ojo-testnet.rpc.kjnodes.com:15059\"|" $HOME/.ojo/config/config.toml

### Set minimum gas price
RUN sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0uojo\"|" $HOME/.ojo/config/app.toml

### Set pruning
RUN sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.ojo/config/app.toml

### Set custom ports
RUN sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:15058\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:15057\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:15060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:15056\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":15066\"%" $HOME/.ojo/config/config.toml
RUN sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:15017\"%; s%^address = \":8080\"%address = \":15080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:15090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:15091\"%; s%:8545%:15045%; s%:8546%:15046%; s%:6065%:15065%" $HOME/.ojo/config/app.toml

## Download latest chain snapshot
RUN curl -L https://snapshots.kjnodes.com/ojo-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.ojo
RUN [[ -f $HOME/.ojo/data/upgrade-info.json ]] && cp $HOME/.ojo/data/upgrade-info.json $HOME/.ojo/cosmovisor/genesis/upgrade-info.json

## Start service and check the logs
RUN sudo systemctl start ojod && sudo journalctl -u ojod -f --no-hostname -o cat

# For Other Steps Read Readme.md


