|   Chain ID	 | Latest Version Tag| Custom Port|
|--------------|-------------------|------------|
| initiation-1 |   v0.2.12         |     135    |
# Official documentation:
  1. https://docs.initia.xyz/run-initia-node/running-initia-node/becoming-a-validator
  2. https://faucet.testnet.initia.xyz/
  3. https://docs.initia.xyz/run-initia-node/running-initia-node/oracle
```bash
  chain_id="initiation-1"
  CUSTOM_PORT=135
  name_all=initiad
  version="v0.2.12"
```
## Install dependencies
```bash
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential unzip
sudo apt -qy upgrade
```
## INSTALL GO
```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.10.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
## Download and build binaries
```bash
# Download binary
cd $HOME
rm -rf initia
git clone https://github.com/initia-labs/initia.git
cd initia
git checkout $version
make build
# Prepare binaries for Cosmovisor
mkdir -p $HOME/.initia/cosmovisor/genesis/bin
mv build/initiad $HOME/.initia/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f
```
## Install Cosmovisor and create a service
```bash
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=$name_all node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.initia"
Environment="DAEMON_NAME=initiad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.initia/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
## Initialize the node 
```bash
# Set node configuration
initiad config set client chain-id initiation-1
initiad config set client keyring-backend test
initiad config set client node tcp://localhost:17957


# Initialize the node
MONIKER="YOUR_MONIKER_GOES_HERE"
$name_all init $MONIKER --chain-id $chain_id

# Download genesis and addrbook
curl -Ls https://snapshots.kjnodes.com/initia-testnet/genesis.json > $HOME/.initia/config/genesis.json
curl -Ls https://snapshots.kjnodes.com/initia-testnet/addrbook.json > $HOME/.initia/config/addrbook.json

# Set minimum gas price
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
  $HOME/.mineplex-chain/config/app.toml
# Disable indexer
sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.mineplex-chain/config/config.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.mineplex-chain/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.mineplex-chain/config/app.toml

```
# Download Latest Snapshot
```bash
curl -L https://snapshots.kjnodes.com/initia-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.initia
[[ -f $HOME/.initia/data/upgrade-info.json ]] && cp $HOME/.initia/data/upgrade-info.json $HOME/.initia/cosmovisor/genesis/upgrade-info.json
```
## Start service and check the logs 
```bash
sudo systemctl daemon-reload
sudo systemctl enable $name_all
sudo systemctl start $name_all && sudo journalctl -u $name_all -f --no-hostname -o cat
# GET SYNC INFO
crossfid status 2>&1 | jq .SyncInfo
curl -s localhost:${CUSTOM_PORT}57/status | jq .result.sync_info
```
## Key management
```bash
# ADD NEW KEY
$name_all keys add wallet
# RECOVER EXISTING KEY
$name_all keys add wallet --recover
# LIST ALL KEYS
$name_all keys list
# EXPORT KEY TO A FILE
$name_all keys export wallet
```
## CREATE NEW VALIDATOR
```bash
$name_all tx staking create-validator \
--amount 1000000000000000000mpx \
--pubkey $(crossfid tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id $chain_id \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--fees 5000uinit \
-y
```
## EDIT EXISTING VALIDATOR
```bash
$name_all tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id $chain_id \
--commission-rate 0.05 \
--from wallet \
--fees 5000uinit \
-y
```
## RESET CHAIN DATA
```bash
$name_all tendermint unsafe-reset-all --keep-addr-book --home $HOME/.initia --keep-addr-book
```
## REMOVE NODE: Make sure you have backed up your priv_validator_key.json
```bash
cd $HOME
sudo systemctl stop $name_all
sudo systemctl disable $name_all
sudo rm /etc/systemd/system/$name_all
sudo systemctl daemon-reload
rm -f $(which initiad)
rm -rf $HOME/.initia
```
## Token management
```bash
# WITHDRAW REWARDS FROM ALL VALIDATORS
$name_all tx distribution withdraw-all-rewards --from wallet --chain-id $chain_id --fees 5000uinit -y
# WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
$name_all tx distribution withdraw-rewards $(crossfid keys show wallet --bech val -a) --commission --from wallet --chain-id $chain_id --fees 5000uinit -y
# Unjail Validator
$name_all tx slashing unjail --from wallet --chain-id $chain_id --gas-adjustment 1.5 --fees 5000uinit -y
# Delegate to other validator
$name_all tx staking delegate <validator address 1000000000000000000000000uinit --from wallet --chain-id $chain_id --fees 5000uinit -y 
```
# Set up Oracle
## Clone the Repository and build binaries
```
cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v0.4.3
make build
mv build/slinky /usr/local/bin/
rm -rf build
```
## Run oracle
```
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 127.0.0.1:$CUSTOM_PORT
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

```
## Start service and check the logs 
```
sudo systemctl daemon-reload
sudo systemctl enable slinkyd
sudo systemctl start slinkyd && sudo journalctl -u slinkyd -f --no-hostname -o cat
```
## Validating Prices
```
make run-oracle-client
```
## Enable Oracle Vote Extension: the Oracle setting should be enabled in the config/app.toml file.
```
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "127.0.0.1:8089"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "500ms"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "false"
```

