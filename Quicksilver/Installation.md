Installation

Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.

![image](https://user-images.githubusercontent.com/91251550/210759562-70a2089a-2df2-4b67-b2b1-650eb06df4b2.png)

Chain ID: quicksilver-2 | Latest Version Tag: v1.2.0 | Custom Port: 11

Setup validator name

Replace YOUR_MONIKER_GOES_HERE with your validator name

      MONIKER="YOUR_MONIKER_GOES_HERE"

Install dependencies

1 Update system and install build tools

      sudo apt update
      sudo apt install curl git jq lz4 build-essential -y
      
2 Install GO

      sudo rm -rf /usr/local/go
      sudo curl -Ls https://golang.org/dl/go1.19.4.linux-amd64.tar.gz | sudo tar -C /usr/local -xz
      tee -a $HOME/.profile > /dev/null << EOF
      export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
      EOF
      source $HOME/.profile
      
3 Download and build binaries

      # Clone project repository
      cd $HOME
      rm -rf quicksilver
      git clone https://github.com/ingenuity-build/quicksilver.git
      cd quicksilver
      ​
      # Build binaries
      git checkout v1.2.0
      make build
      mkdir -p $HOME/.quicksilverd/cosmovisor/genesis/bin
      mv build/quicksilverd $HOME/.quicksilverd/cosmovisor/genesis/bin/
      rm -rf build
      
4 Install Cosmovisor and create a service

      # Download and install Cosmovisor
      go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.4.0
      ​
      # Create service
      sudo tee /etc/systemd/system/quicksilverd.service > /dev/null << EOF
      [Unit]
      Description=quicksilver node service
      After=network-online.target
      [Service]
      User=$USER
      ExecStart=$(which cosmovisor) run start
      Restart=on-failure
      RestartSec=10
      LimitNOFILE=65535
      Environment="DAEMON_HOME=$HOME/.quicksilverd"
      Environment="DAEMON_NAME=quicksilverd"
      Environment="UNSAFE_SKIP_BACKUP=true"
      [Install]
      WantedBy=multi-user.target
      EOF
      sudo systemctl daemon-reload
      sudo systemctl enable quicksilverd
      ​
      # Create application symlinks
      ln -s $HOME/.quicksilverd/cosmovisor/genesis $HOME/.quicksilverd/cosmovisor/current
      sudo ln -s $HOME/.quicksilverd/cosmovisor/current/bin/quicksilverd /usr/local/bin/quicksilverd
      
5 Initialize the node

      # Set node configuration
      quicksilverd config chain-id quicksilver-2
      quicksilverd config keyring-backend file
      quicksilverd config node tcp://localhost:11657
      ​
      # Initialize the node
      quicksilverd init $MONIKER --chain-id quicksilver-2
      ​
      # Download genesis and addrbook
      curl -Ls https://snapshots.kjnodes.com/quicksilver/genesis.json > $HOME/.quicksilverd/config/genesis.json
      curl -Ls https://snapshots.kjnodes.com/quicksilver/addrbook.json > $HOME/.quicksilverd/config/addrbook.json
      ​
      # Add seeds
      sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@quicksilver.rpc.kjnodes.com:11659\"|" $HOME/.quicksilverd/config/config.toml
      ​
      # Set minimum gas price
      sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0001uqck\"|" $HOME/.quicksilverd/config/app.toml
      ​
      # Set pruning
      sed -i -e "s|^pruning *=.*|pruning = \"custom\"|" $HOME/.quicksilverd/config/app.toml
      sed -i -e "s|^pruning-keep-recent *=.*|pruning-keep-recent = \"100\"|" $HOME/.quicksilverd/config/app.toml
      sed -i -e "s|^pruning-keep-every *=.*|pruning-keep-every = \"0\"|" $HOME/.quicksilverd/config/app.toml
      sed -i -e "s|^pruning-interval *=.*|pruning-interval = \"19\"|" $HOME/.quicksilverd/config/app.toml
      ​
      # Set custom ports
      sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:11658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:11657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:11060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:11656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":11660\"%" $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:11317\"%; s%^address = \":8080\"%address = \":11080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:11090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:11091\"%; s%^address = \"0.0.0.0:8545\"%address = \"0.0.0.0:11545\"%; s%^ws-address = \"0.0.0.0:8546\"%ws-address = \"0.0.0.0:11546\"%" $HOME/.quicksilverd/config/app.toml

6 Download latest chain snapshot

      curl -L https://snapshots.kjnodes.com/quicksilver/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.quicksilverd
      
7 Start service and check the logs

      sudo systemctl start quicksilverd && journalctl -u quicksilverd -f --no-hostname -o cat
