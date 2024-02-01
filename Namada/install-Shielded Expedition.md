# chain ID is shielded-expedition.b40d8e9055
## install namada
```bash
mkdir namada
cd namada
wget https://github.com/anoma/namada/releases/download/v0.31.0/namada-v0.31.0-Linux-x86_64.tar.gz
wget https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz
tar -xvf namada-v0.31.0-Linux-x86_64.tar.gz
tar -xvf cometbft_0.37.2_linux_amd64.tar.gz
sudo cp cometbft /usr/local/bin/
sudo cp cometbft /usr/bin/
sudo cp namada-v0.31.0-Linux-x86_64/namada* /usr/local/bin/
sudo cp namada-v0.31.0-Linux-x86_64/namada* /usr/bin/
sudo cp namada-v0.31.0-Linux-x86_64/namada* /usr/local/sbin/
```
## Create service
```bash
sudo tee /etc/systemd/system/namadad.service > /dev/null <<EOF
[Unit]
Description=Namada Service
After=network-online.target
[Service] 
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Environment=NAMADA_LOG=debug
Environment=NAMADA_TM_STDOUT=true
Environment=NAMADA_CMT_STDOUT=true
Environment=CMT_LOG_LEVEL=p2p:none,pex:error
ExecStart=/usr/local/bin/namada --base-dir=$HOME/.local/share/namada ledger run
StandardOutput=syslog
StandardError=syslog
CPUWeight=30
IOWeight=30
Restart=always
RestartSec=10
LimitNOFILE=102400
[Install]
WantedBy=multi-user.target
EOF
```
### enable service
```bash
sudo systemctl daemon-reload
sudo systemctl enable namadad
```
## Join chain
### 1.POST-GENESIS VALIDATOR
```bash
export CHAIN_ID="shielded-expedition.b40d8e9055"
namada client utils join-network --chain-id $CHAIN_ID
```
### 2.PRE-GENESIS Validators
```bash
export CHAIN_ID="shielded-expedition.b40d8e9055"
export ALIAS="IONode Online"
namada client utils join-network --chain-id $CHAIN_ID --genesis-validator "${ALIAS}"
```
## Start/stop/check logs
```bash
sudo systemctl stop namadad
sudo systemctl start namadad
sudo journalctl -u namadad -f -o cat
curl -s localhost:26657/status | jq
```
## SETUP VALIDATOR: (Node must by synced)
```bash
export KEY_NAME="ionode"
```
### generate new key
```bash
namadaw gen --alias "${KEY_NAME}"
```
#### or recover key from mnemonic
```bash
namadaw derive --alias "${KEY_NAME}"
```
### check key address
```bash
namadaw find --alias "${KEY_NAME}"
```
### list all the keys
```bash
namadaw  list
```
### check balance
```bash
namadac balance --token NAM --owner "${KEY_NAME}"
```
### initialize validator: (Node must by synced)
```bash
export VAL_NAME="IONode Online"
export EMAIL="ionodeonline@gmail.com"
```
```bash
namadac  init-validator \
 --alias "${VAL_NAME}" \
 --account-keys "${KEY_NAME}" \
 --signing-keys "${KEY_NAME}" \
 --commission-rate 0.1 \
 --max-commission-rate-change 0.1 \
 --email "${EMAIL}"
```
### bond tokens to your validator
```bash
namadac  bond \
 --source "${KEY_NAME}" \
 --validator "${VAL_NAME}" \
 --amount 1000
```
## USERFULL COMMAND
### query TX
```bash
namadac tx-result --tx-hash <TX HASH>
```
### find validator key
```bash
namadac find-validator --tm-address=$(curl -s localhost:26657/status | jq -r .result.validator_info.address)
```
### send funds
```bash
namada  transfer --target <RECEIVER-KEY(tnam1..)> --source <SENDER-KEY(NAME)> --amount 100 --token NAM 
```
### check validator consensus status
```bash
namadac validator-state --validator "<Validator address tham1...>"
```
### unjail validator
```bash
namadac unjail-validator --validator  "<Validator address tham1...>"
```
### claim rewards
```bash
namadac claim-rewards --validator "<Validator address tnam1...>"
```
