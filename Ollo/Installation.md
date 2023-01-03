Installation

Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.

![image](https://user-images.githubusercontent.com/91251550/210380789-e0b1f55e-b4cb-48ba-96d6-cd22829e19b9.png)

Chain ID: ollo-testnet-1 | Latest Version Tag: v0.0.1 | Custom Port: 32

Setup validator name

      Replace YOUR_MONIKER_GOES_HERE with your validator name

      MONIKER="YOUR_MONIKER_GOES_HERE"

Install dependencies

Update system and install build tools

      sudo apt update
      sudo apt install curl git jq lz4 build-essential -y
      
Install GO

      sudo rm -rf /usr/local/go
      sudo curl -Ls https://golang.org/dl/go1.19.4.linux-amd64.tar.gz | sudo tar -C /usr/local -xz
      tee -a $HOME/.profile > /dev/null << EOF
      export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
      EOF
      source $HOME/.profile
      Download and build binaries
      
# Clone project repository

      cd $HOME
      rm -rf ollo
      git clone https://github.com/OLLO-Station/ollo.git
      cd ollo
​
# Build binaries

      git checkout v0.0.1
      make build
      mkdir -p $HOME/.ollo/cosmovisor/genesis/bin
      mv bin/ollod $HOME/.ollo/cosmovisor/genesis/bin/
      rm -rf build
      Install Cosmovisor and create a service
      
# Download and install Cosmovisor

      go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
​

# Create service

      sudo tee /etc/systemd/system/ollod.service > /dev/null << EOF
      [Unit]
      Description=ollo-testnet node service
      After=network-online.target
      [Service]
      User=$USER
      ExecStart=$(which cosmovisor) run start
      Restart=on-failure
      RestartSec=10
      LimitNOFILE=65535
      Environment="DAEMON_HOME=$HOME/.ollo"
      Environment="DAEMON_NAME=ollod"
      Environment="UNSAFE_SKIP_BACKUP=true"
      [Install]
      WantedBy=multi-user.target
      EOF
      sudo systemctl daemon-reload
      sudo systemctl enable ollod
​
# Create application symlinks

      ln -s $HOME/.ollo/cosmovisor/genesis $HOME/.ollo/cosmovisor/current
      sudo ln -s $HOME/.ollo/cosmovisor/current/bin/ollod /usr/local/bin/ollod
      Initialize the node

# Set node configuration

      ollod config chain-id ollo-testnet-1
      ollod config keyring-backend test
      ollod config node tcp://localhost:32657
​
# Initialize the node

      ollod init $MONIKER --chain-id ollo-testnet-1
​
# Download genesis and addrbook

      curl -Ls https://snapshots.kjnodes.com/ollo-testnet/genesis.json > $HOME/.ollo/config/genesis.json
      curl -Ls https://snapshots.kjnodes.com/ollo-testnet/addrbook.json > $HOME/.ollo/config/addrbook.json
​
# Add seeds

      sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@ollo-testnet.rpc.kjnodes.com:32659\"|" $HOME/.ollo/config/config.toml
​
# Set minimum gas price

      sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0utollo\"|" $HOME/.ollo/config/app.toml
​
# Set pruning

      sed -i -e "s|^pruning *=.*|pruning = \"custom\"|" $HOME/.ollo/config/app.toml
      sed -i -e "s|^pruning-keep-recent *=.*|pruning-keep-recent = \"100\"|" $HOME/.ollo/config/app.toml
      sed -i -e "s|^pruning-keep-every *=.*|pruning-keep-every = \"0\"|" $HOME/.ollo/config/app.toml
      sed -i -e "s|^pruning-interval *=.*|pruning-interval = \"19\"|" $HOME/.ollo/config/app.toml
​
# Set custom ports

      sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:32658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:32657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:32060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:32656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":32660\"%" $HOME/.ollo/config/config.toml
      sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:32317\"%; s%^address = \":8080\"%address = \":32080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:32090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:32091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:32545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:32546\"%" $HOME/.ollo/config/app.toml
      
Download latest chain snapshot
 
      curl -L https://snapshots.kjnodes.com/ollo-testnet/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.ollo
       
Start service and check the logs

      sudo systemctl start ollod && journalctl -u ollod -f --no-hostname -o cat
