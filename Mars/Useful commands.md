Useful commands

Useful set of commands for node operators. From key management to chain governance.
![image](https://user-images.githubusercontent.com/91251550/213097041-00876dea-78cf-4aae-82dd-6b6d3ac84ab4.png)

🔑 Key management

1 Add new key

      marsd keys add wallet

2 Recover existing key

      marsd keys add wallet --recover

3 List all keys

      marsd keys list

4 Delete key

      marsd keys delete wallet

5 Export key to the file

      marsd keys export wallet

6 Import key from the file

      marsd keys import wallet wallet.backup

7 Query wallet balance

      marsd q bank balances $(marsd keys show wallet -a)

👷 Validator management

Please make sure you have adjusted moniker, identity, details and website to match your values.

1 Create new validator

      marsd tx staking create-validator \
      --amount=1000000umars \
      --pubkey=$(marsd tendermint show-validator) \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL" \
      --chain-id=ares-1 \
      --commission-rate=0.05 \
      --commission-max-rate=0.20 \
      --commission-max-change-rate=0.01 \
      --min-self-delegation=1 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0umars \
      -y

2 Edit existing validator

      marsd tx staking edit-validator \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=ares-1 \
      --commission-rate=0.05 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0umars \
      -y

3 Unjail validator

      marsd tx slashing unjail --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

4 Jail reason

      marsd query slashing signing-info $(marsd tendermint show-validator)

5 List all active validators

      marsd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

6 List all inactive validators

      marsd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

7 View validator details

      marsd q staking validator $(marsd keys show wallet --bech val -a)

💲 Token management

1 Withdraw rewards from all validators

      marsd tx distribution withdraw-all-rewards --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

2 Withdraw commission and rewards from your validator

      marsd tx distribution withdraw-rewards $(marsd keys show wallet --bech val -a) --commission --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

3 Delegate tokens to yourself

      marsd tx staking delegate $(marsd keys show wallet --bech val -a) 1000000umars --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

4 Delegate tokens to validator

      marsd tx staking delegate <TO_VALOPER_ADDRESS> 1000000umars --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

5 Redelegate tokens to another validator

      marsd tx staking redelegate $(marsd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000umars --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

6 Unbond tokens from your validator

      marsd tx staking unbond $(marsd keys show wallet --bech val -a) 1000000umars --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

7 Send tokens to the wallet

      marsd tx bank send wallet <TO_WALLET_ADDRESS> 1000000umars --from wallet --chain-id ares-1

🗳 Governance

1 List all proposals

      marsd query gov proposals

2 View proposal by id

      marsd query gov proposal 1

3 Vote 'Yes'

      marsd tx gov vote 1 yes --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

4 Vote 'No'

      marsd tx gov vote 1 no --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

5 Vote 'Abstain'

      marsd tx gov vote 1 abstain --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

6 Vote 'NoWithVeto'

      marsd tx gov vote 1 nowithveto --from wallet --chain-id ares-1 --gas-adjustment 1.4 --gas auto --gas-prices 0umars -y

⚡️ Utility

1 Update ports

      CUSTOM_PORT=10
      sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.mars/config/config.toml
      sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.mars/config/app.toml

2 Update Indexer

2.1 Disable indexer

      sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.mars/config/config.toml

2.2 Enable indexer

      sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.mars/config/config.toml

2.3 Update pruning

      sed -i \
        -e 's|^pruning *=.*|pruning = "custom"|' \
        -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
        -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
        -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
        $HOME/.mars/config/app.toml

🚨 Maintenance

1 Get validator info

      marsd status 2>&1 | jq .ValidatorInfo

2 Get sync info

      marsd status 2>&1 | jq .SyncInfo

3 Get node peer

      echo $(marsd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.mars/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')

4 Check if validator key is correct

      [[ $(marsd q staking validator $(marsd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(marsd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"

5 Get live peers

      curl -sS http://localhost:45657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'

6 Set minimum gas price

      sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0umars\"/" $HOME/.mars/config/app.toml

7 Enable prometheus

      sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.mars/config/config.toml

8 Reset chain data

      marsd tendermint unsafe-reset-all --home $HOME/.mars --keep-addr-book

9 Remove node

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!
cd $HOME

      sudo systemctl stop marsd
      sudo systemctl disable marsd
      sudo rm /etc/systemd/system/marsd.service
      sudo systemctl daemon-reload
      rm -f $(which marsd)
      rm -rf $HOME/.mars
      rm -rf $HOME/hub

⚙️ Service Management

1 Reload service configuration

      sudo systemctl daemon-reload

2 Enable service

      sudo systemctl enable marsd

3 Disable service

      sudo systemctl disable marsd

4 Start service

      sudo systemctl start marsd

5 Stop service

      sudo systemctl stop marsd

6 Restart service

      sudo systemctl restart marsd

7 Check service status

      sudo systemctl status marsd

8 Check service logs

      sudo journalctl -u marsd -f
