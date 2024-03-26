
# SIDE PROTOCOL
|   Chain ID	 | Latest Version Tag| Custom Port|
|--------------|-------------------|------------|
| side-testnet-3|      0.7.0      |     130    |

https://explorer.side.exchange/testnet

```bash
  chain_id="side-testnet-3"
  CUSTOM_PORT=130
  name_all=sided
```
## Install dependencies
```bash
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```
## INSTALL GO
```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.1.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
## Download and build binaries
```bash
# Clone project repository
cd $HOME
rm -rf sidechain
git clone https://github.com/sideprotocol/side.git
cd side
git checkout v0.7.0

# Build binaries
make build

# Prepare binaries for Cosmovisor
mkdir -p $HOME/.side/cosmovisor/genesis/bin
mv build/sided $HOME/.side/cosmovisor/genesis/bin/
rm -rf build

# Create application symlinks
sudo ln -s $HOME/.side/cosmovisor/genesis $HOME/.side/cosmovisor/current -f
sudo ln -s $HOME/.side/cosmovisor/current/bin/sided /usr/local/bin/sided -f
```
## Install Cosmovisor and create a service
```bash
# Download and install Cosmovisor
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0

# Create service
sudo tee /etc/systemd/system/sided.service > /dev/null << EOF
[Unit]
Description=$name_all node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.side"
Environment="DAEMON_NAME=sided"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.side/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
## Initialize the node 
```bash
# Set node configuration
$name_all config chain-id $chain_id
$name_all config keyring-backend test
$name_all config node tcp://localhost:${CUSTOM_PORT}57

# Initialize the node
MONIKER="YOUR_MONIKER_GOES_HERE"
$name_all init $MONIKER --chain-id $chain_id

# Download genesis and addrbook
wget https://github.com/sideprotocol/testnet/raw/main/side-testnet-3/genesis.json -O ~/.side/config/genesis.json
wget https://rpc-side-testnet.trusted-point.com/addrbook.json -O $HOME/.side/config/addrbook.json
# Set minimum gas price
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.005uside"|g' $HOME/.side/config/app.toml

# Add seeds & peers (option)
SEEDS="00170c0c23c3e97c740680a7f881511faf68289a@202.182.119.24:26656"
PEERS="bbbf623474e377664673bde3256fc35a36ba0df1@side-testnet-peer.itrocket.net:45656,2ca1a2f1170df5ecb55dcae5e976d6dbb85e3b6b@65.109.92.148:61456,ad731aefa7582d59b4de7c9063e87dc90d98fa8f@78.46.45.174:12656,5e0b5f26e4c069fbcaac1ae4b22aba151e463a52@65.108.79.241:60856,cb0c64e85255d127d5e9c16f7db9c995b3600c37@152.228.208.164:26656,fad59a438051161be332714505e86a5da4920bc4@109.123.242.217:21306,09ea56a225a4c3e525ae4cfc1311157dbcbe33c9@141.95.65.26:24966,00e0d0a39d56bb945de4d4179ae0bd49679cac65@65.108.206.118:46656,08f006100a637b2fea09eab6c124949fe437af3e@37.27.69.161:36656,db165a039236165c8c1225c1c793320593ce2b35@65.109.68.87:21656,91b6c3d622e28752c428091ca47eb463b63d14de@162.55.4.42:11356"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.side/config/config.toml

# Set pruning
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "10"|' \
  $HOME/.side/config/app.toml

# Disable indexer
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.side/config/config.toml

# Set custom ports
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}58\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}57\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}60\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}56\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}66\"%" $HOME/.side/config/config.toml
sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}17\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}80\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}90\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}91\"%" $HOME/.side/config/app.toml

```
## Start service and check the logs 
```bash
sudo systemctl daemon-reload
sudo systemctl enable $name_all
sudo systemctl start $name_all && sudo journalctl -u $name_all -f --no-hostname -o cat
# GET SYNC INFO
$name_all status 2>&1 | jq .SyncInfo
curl -s localhost:${CUSTOM_PORT}/status | jq .result.sync_info
```
## Key management
```bash
# ADD NEW KEY
$name_all keys add wallet --keyring-backend test
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
--amount 1000000uside \
--pubkey $(sided tendermint show-validator) \
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
--gas-adjustment 1.4 \
--gas auto \
--fees 1300uside \
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
--gas-adjustment 1.4 \
--gas auto \
--fees 1300uside \
-y
```
## RESET CHAIN DATA
```bash
$name_all tendermint unsafe-reset-all --keep-addr-book --home $HOME/.side --keep-addr-book
```
## REMOVE NODE: Make sure you have backed up your priv_validator_key.json
```bash
cd $HOME
sudo systemctl stop $name_all
sudo systemctl disable $name_all
sudo rm /etc/systemd/system/$name_all
sudo systemctl daemon-reload
rm -f $(which sided)
rm -rf $HOME/.side
rm -rf $HOME/side
```
## Token management
```bash
# Delegate
$name tx staking delegate sidevaloper1e6603g3hukd9lad90vc3tgxetg949d4q2nhguf 98000000uside --from wallet --chain-id side-testnet-1 --gas auto --fees 1300uside -y
# WITHDRAW REWARDS FROM ALL VALIDATORS
$name_all tx distribution withdraw-all-rewards --from wallet --chain-id $chain_id --gas-adjustment 1.4 --gas auto --fees 1300uside -y
# WITHDRAW COMMISSION AND REWARDS FROM YOUR VALIDATOR
$name_all tx distribution withdraw-rewards $(quicksilverd keys show wallet --bech val -a) --commission --from wallet --chain-id $chain_id --gas-adjustment 1.4 --gas auto --fees 1300uside -y
# Unjail Validator
$name_all tx slashing unjail --from wallet --chain-id $chain_id --gas-adjustment 1.4 --gas auto --fees 1300uside -y
```
