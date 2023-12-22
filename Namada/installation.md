## Update packages and Install dependencies
```bash
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y make git-core libssl-dev pkg-config libclang-12-dev build-essential protobuf-compiler
```
## Install GO
```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.20.12.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
## Install Rust
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
```
## Protocol Buffers
```bash
cd $HOME
curl -L -o protobuf.zip https://github.com/protocolbuffers/protobuf/releases/download/v24.4/protoc-24.4-linux-x86_64.zip
mkdir protobuf_temp && unzip protobuf.zip -d protobuf_temp/
sudo cp protobuf_temp/bin/protoc /usr/local/bin/
sudo cp -r protobuf_temp/include/* /usr/local/include/
rm -rf protobuf_temp protobuf.zip
```
## Install CometBFT:
```bash
cd $HOME
rm -rf cometbft
git clone https://github.com/cometbft/cometbft.git
cd cometbft
git checkout v0.37.2
make build
sudo cp $HOME/cometbft/build/cometbft /usr/local/bin/
cometbft version
```
## Change default port if needed
```bash
NAMADA_PORT=23
echo "export NAMADA_PORT="$NAMADA_PORT"" >> $HOME/.bash_profile
echo "export ALIAS="CHOOSE_A_NAME_FOR_YOUR_VALIDATOR"" >> $HOME/.bash_profile
echo "export WALLET="CHOOSE_A_WALLET_NAME"" >> $HOME/.bash_profile
echo "export PUBLIC_IP=$(wget -qO- eth0.me)" >> $HOME/.bash_profile
echo "export TM_HASH="v0.1.4-abciplus"" >> $HOME/.bash_profile
echo "export CHAIN_ID="public-testnet-15.0dacadb8d663"" >> $HOME/.bash_profile
echo "export BASE_DIR="$HOME/.local/share/namada"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## Download prebuilt Namada
```bash
cd $HOME/namada
wget https://github.com/anoma/namada/releases/download/v0.28.1/namada-v0.28.1-Linux-x86_64.tar.gz
tar -xvf namada-v0.28.1-Linux-x86_64.tar.gz
rm namada-v0.28.1-Linux-x86_64.tar.gz
cd namada-v0.28.1-Linux-x86_64
sudo mv namada namadan namadac namadaw /usr/local/bin/
if [ ! -d "$HOME/.local/share/namada" ]; then
    mkdir -p "$HOME/.local/share/namada"
fi
```
## Check version
```bash
namada --version
cometbft version
```
## Join-network as Pre-Genesis Validator: Move your pre-genesis folder to $BASE_DIR and join the network
```bash
cd $HOME
cp -r ~/.namada/pre-genesis $BASE_DIR/
namada client utils join-network --chain-id $CHAIN_ID --genesis-validator $ALIAS
```
##  Join-network as Full Nodes or Post-Genesis Validator
```bash
namada client utils join-network --chain-id $CHAIN_ID
```
## Create Service file
```bash
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=namada
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$BASE_DIR
Environment=TM_LOG_LEVEL=p2p:none,pex:error
Environment=NAMADA_CMT_STDOUT=true
Environment=NAMADA_LOG=info
ExecStart=$(which namada) node ledger run
StandardOutput=syslog
StandardError=syslog
Restart=always
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## Set custom ports in config.toml
```bash
sed -i.bak -e "s%:26658%:${NAMADA_PORT}658%g;
s%:26657%:${NAMADA_PORT}657%g;
s%:26656%:${NAMADA_PORT}656%g;
s%:26545%:${NAMADA_PORT}545%g;
s%:8545%:${NAMADA_PORT}545%g;
s%:26660%:${NAMADA_PORT}660%g" $HOME/.local/share/namada/public-testnet-15.0dacadb8d663/config.toml
```
## Enable and start service
```bash
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl start namadad && sudo journalctl -u namadad -f
```
## Create Post-Genesis Validator (skip this if you are a pre-genesis validator)
### Create wallet
```bash
namada wallet address gen --alias $WALLET
```
### Or restore wallet:
```bash
namada wallet key derive --alias $WALLET --hd-path default
```
### Check wallet list
```bash
namada wallet key list
```
### Fund your wallet from faucet by Transparent Address: tnam1... 
### Transparent Address show by: import key to Namada extension (chrome, ...)
```bash
https://faucet.heliax.click/
```
### Create validator:
### (!) before creating a validator, you need to check the balance and make sure that the node is synched
### Check Sync status, once your node is fully synced, the output from above will say: FALSE
```bash
curl http://127.0.0.1:${NAMADA_PORT}657/status | jq .result.sync_info.catching_up
```
### Check your balance
```bash
namada client balance --owner $WALLET --node tcp://127.0.0.1:${NAMADA_PORT}657
```
### Init validator
```bash
namada client init-validator \
 --alias $ALIAS \
 --account-keys $WALLET \
 --signing-keys $WALLET \
 --commission-rate 0.1 \
 --max-commission-rate-change 0.1 \
 --node tcp://127.0.0.1:${NAMADA_PORT}657 \
 --email $EMAIL
```
### Stake your funds
```bash
namada client bond \
 --source $WALLET  --validator $ALIAS  --amount 1000
```
### Waiting more than 2 epoch and check your status:
```bash
namada client bonds --owner $ALIAS
```
## Delete node
```bash
sudo systemctl stop namadad
sudo systemctl disable namadad
sudo rm -rf /etc/systemd/system/namadad.service
sudo systemctl daemon-reload
sudo rm $(which namada)
sudo rm -rf $HOME/.local/share/namada/$CHAIN_ID
```
