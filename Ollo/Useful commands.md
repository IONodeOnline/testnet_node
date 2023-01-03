Useful commands

Useful set of commands for node operators. From key management to chain governance.

![image](https://user-images.githubusercontent.com/91251550/210383569-152b8e00-d853-46d1-81fa-4050092f0d27.png)

🔑 Key management

1 Add new key

      ollod keys add wallet
      
2 Recover existing key

      ollod keys add wallet --recover
      
3 List all keys

      ollod keys list
      
4 Delete key

      ollod keys delete wallet
      
5 Export key to the file

      ollod keys export wallet
      
6 Import key from the file

      ollod keys import wallet wallet.backup
      
7 Query wallet balance

      ollod q bank balances $(ollod keys show wallet -a)
      
👷 Validator management

Please make sure you have adjusted moniker, identity, details and website to match your values.

1 Create new validator

      ollod tx staking create-validator \
      --amount=1000000utollo \
      --pubkey=$(ollod tendermint show-validator) \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=ollo-testnet-1 \
      --commission-rate=0.05 \
      --commission-max-rate=0.20 \
      --commission-max-change-rate=0.01 \
      --min-self-delegation=1 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0utollo \
      -y
      
2 Edit existing validator

      ollod tx staking edit-validator \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=ollo-testnet-1 \
      --commission-rate=0.05 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0utollo \
      -y
      
3 Unjail validator

      ollod tx slashing unjail --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y
      
4 Jail reason

      ollod query slashing signing-info $(ollod tendermint show-validator)
      
5 List all active validators

      ollod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

6 List all inactive validators
      ollod q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

7 View validator details

      ollod q staking validator $(ollod keys show wallet --bech val -a)
      
💲 Token management

1 Withdraw rewards from all validators

      ollod tx distribution withdraw-all-rewards --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

2 Withdraw commission and rewards from your validator

      ollod tx distribution withdraw-rewards $(ollod keys show wallet --bech val -a) --commission --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

3 Delegate tokens to yourself

      ollod tx staking delegate $(ollod keys show wallet --bech val -a) 1000000utollo --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

4 Delegate tokens to validator

      ollod tx staking delegate <TO_VALOPER_ADDRESS> 1000000utollo --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

5 Redelegate tokens to another validator

      ollod tx staking redelegate $(ollod keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000utollo --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

6 Unbond tokens from your validator

      ollod tx staking unbond $(ollod keys show wallet --bech val -a) 1000000utollo --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

7 Send tokens to the wallet

      ollod tx bank send wallet <TO_WALLET_ADDRESS> 1000000utollo --from wallet --chain-id ollo-testnet-1

🗳 Governance

1 List all proposals

      ollod query gov proposals

2 View proposal by id

      ollod query gov proposal 1

3 Vote 'Yes'

      ollod tx gov vote 1 yes --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

4 Vote 'No'

      ollod tx gov vote 1 no --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

5 Vote 'Abstain'

      ollod tx gov vote 1 abstain --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

6 Vote 'NoWithVeto'

      ollod tx gov vote 1 nowithveto --from wallet --chain-id ollo-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0utollo -y

⚡️ Utility

1 Update ports


      CUSTOM_PORT=10
      sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.ollo/config/config.toml
      sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.ollo/config/app.toml

2 Update Indexer

Disable indexer

      sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.ollo/config/config.toml

Enable indexer

      sed -i 's|^indexer *=.*|indexer = "kv"|' $HOME/.ollo/config/config.toml

Update pruning

      sed -i.bak -e 's|^pruning *=.*|pruning = "custom"|; s|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|; s|^pruning-keep-every *=.*|pruning-keep-every = "0"|; s|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.ollo/config/app.toml

🚨 Maintenance

1 Get validator info

      ollod status 2>&1 | jq .ValidatorInfo

2 Get sync info

      ollod status 2>&1 | jq .SyncInfo

3 Get node peer

      echo $(ollod tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.ollo/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')

4 Check if validator key is correct

      [[ $(ollod q staking validator $(ollod keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(ollod status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"

5 Get live peers

      curl -sS http://localhost:32657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'

6 Set minimum gas price

      sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0utollo\"/" $HOME/.ollo/config/app.toml

7 Enable prometheus

      sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.ollo/config/config.toml

8 Reset chain data

      ollod tendermint unsafe-reset-all --home $HOME/.ollo --keep-addr-book

9 Remove node

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!

      cd $HOME
      sudo systemctl stop ollod
      sudo systemctl disable ollod
      sudo rm /etc/systemd/system/ollod.service
      sudo systemctl daemon-reload
      rm -rf $(which ollod) 
      rm -rf $HOME/.ollo
      rm -rf $HOME/ollo

⚙️ Service Management

1 Reload service configuration

      sudo systemctl daemon-reload

2 Enable service

      sudo systemctl enable ollod

3 Disable service

      sudo systemctl disable ollod

4 Start service

      sudo systemctl start ollod

5 Stop service

      sudo systemctl stop ollod

6 Restart service

      sudo systemctl restart ollod

7 Check service status

      sudo systemctl status ollod

8 Check service logs

      sudo journalctl -u ollod -f --no-hostname -o cat
