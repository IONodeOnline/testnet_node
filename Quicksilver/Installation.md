Installation

Setting up your validator node has never been so easy. Get your validator running in minutes by following step by step instructions.

![image](https://user-images.githubusercontent.com/91251550/210759562-70a2089a-2df2-4b67-b2b1-650eb06df4b2.png)

|   Chain ID	 | Latest Version Tag| Custom Port|
|--------------|-------------------|------------|
| rhye-1   |      v1.4.4-rc.3      |     110    |


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
      git checkout v1.4.4-rc.3
      make build
      ​
      # Build binaries
      sudo rm -rf /usr/local/go
      curl -Ls https://go.dev/dl/go1.20.12.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
      eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
      eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
      
4 Install Cosmovisor and create a service

      # Download and install Cosmovisor
      go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
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
      quicksilverd config chain-id rhye-1
      quicksilverd config keyring-backend test
      quicksilverd config node tcp://localhost:11057
      ​
      # Initialize the node
      MONIKER="YOUR_MONIKER_GOES_HERE"
      quicksilverd init $MONIKER --chain-id rhye-1
      ​
      # Download genesis and addrbook
      curl -Ls https://snapshots.kjnodes.com/quicksilver-testnet/genesis.json > $HOME/.quicksilverd/config/genesis.json
      curl -Ls https://snapshots.kjnodes.com/quicksilver-testnet/addrbook.json > $HOME/.quicksilverd/config/addrbook.json
      ​
      # Set minimum gas price
      sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.0001uqck\"|" $HOME/.quicksilverd/config/app.toml
      ​# Disable indexer
      sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.quicksilverd/config/config.toml
      # Set pruning
      sed -i \
        -e 's|^pruning *=.*|pruning = "custom"|' \
        -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
        -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
        -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
      $HOME/.quicksilverd/config/app.toml
      ​
      # Set custom ports
     CUSTOM_PORT=110
      sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr =                        \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr =         \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.quicksilverd/config/config.toml
      sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address =       \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.quicksilverd/config/app.toml
      
6 Start service and check the logs

      sudo systemctl daemon-reload
      sudo systemctl enable quicksilverd
      sudo systemctl start quicksilverd && sudo journalctl -u quicksilverde -f --no-hostname -o cat
      # GET SYNC INFO
      quicksilverd status 2>&1 | jq .SyncInfo
      curl -s localhost:${CUSTOM_PORT}/status | jq .result.sync_info
      
7 CREATE NEW VALIDATOR

      quicksilverd tx staking create-validator \
      --amount 1000000uqck \
      --pubkey $(quicksilverd tendermint show-validator) \
      --moniker "YOUR_MONIKER_NAME" \
      --identity "YOUR_KEYBASE_ID" \
      --details "YOUR_DETAILS" \
      --website "YOUR_WEBSITE_URL" \
      --chain-id rhye-1 \
      --commission-rate 0.05 \
      --commission-max-rate 0.20 \
      --commission-max-change-rate 0.01 \
      --min-self-delegation 1 \
      --from wallet \
      --gas-adjustment 1.4 \
      --gas auto \
      --gas-prices 0.0001uqck \
      -y

8 EDIT EXISTING VALIDATOR

      quicksilverd tx staking edit-validator \
      --new-moniker "YOUR_MONIKER_NAME" \
      --identity "YOUR_KEYBASE_ID" \
      --details "YOUR_DETAILS" \
      --website "YOUR_WEBSITE_URL" \
      --chain-id rhye-1 \
      --commission-rate 0.05 \
      --from wallet \
      --gas-adjustment 1.4 \
      --gas auto \
      --gas-prices 0.0001uqck \
      -y

9. RESET CHAIN DATA
```bash
quicksilverd tendermint unsafe-reset-all --keep-addr-book --home $HOME/.quicksilverd --keep-addr-book
```
10. REMOVE NODE: Make sure you have backed up your priv_validator_key.json
```bash
cd $HOME
sudo systemctl stop quicksilverd
sudo systemctl disable quicksilverd
sudo rm /etc/systemd/system/quicksilverd
sudo systemctl daemon-reload
rm -f $(which quicksilverd)
rm -rf $HOME/.quicksilverd
rm -rf $HOME/quicksilver
```
11. Token management
```bash
# WITHDRAW REWARDS FROM ALL VALIDATORS
quicksilverd tx distribution withdraw-all-rewards --from wallet --chain-id rhye-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y
# WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
quicksilverd tx distribution withdraw-rewards $(quicksilverd keys show wallet --bech val -a) --commission --from wallet --chain-id rhye-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y
# Unjail Validator
quicksilverd tx slashing unjail --from qcknode2 --chain-id rhye-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y
```
