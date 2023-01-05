Useful commands

Useful set of commands for node operators. From key management to chain governance.

![image](https://user-images.githubusercontent.com/91251550/210761482-814ce869-1d80-41ae-8b64-e24d35cf04ff.png)

🔑 Key management

1 Add new key

      quicksilverd keys add wallet

2 Recover existing key

      quicksilverd keys add wallet --recover

3 List all keys

      quicksilverd keys list

4 Delete key

      quicksilverd keys delete wallet

5 Export key to the file

      quicksilverd keys export wallet

6 Import key from the file

      quicksilverd keys import wallet wallet.backup

7 Query wallet balance

      quicksilverd q bank balances $(quicksilverd keys show wallet -a)

👷 Validator management

Please make sure you have adjusted moniker, identity, details and website to match your values.

1 Create new validator

      quicksilverd tx staking create-validator \
      --amount=1000000uqck \
      --pubkey=$(quicksilverd tendermint show-validator) \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=quicksilver-2 \
      --commission-rate=0.05 \
      --commission-max-rate=0.20 \
      --commission-max-change-rate=0.01 \
      --min-self-delegation=1 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0.0001uqck \
      -y

2 Edit existing validator

      quicksilverd tx staking edit-validator \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=quicksilver-2 \
      --commission-rate=0.05 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0.0001uqck \
      -y

3 Unjail validator

      quicksilverd tx slashing unjail --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

4 Jail reason

      quicksilverd query slashing signing-info $(quicksilverd tendermint show-validator)

5 List all active validators

      quicksilverd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

6 List all inactive validators

      quicksilverd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

7 View validator details

      quicksilverd q staking validator $(quicksilverd keys show wallet --bech val -a)

💲 Token management

1 Withdraw rewards from all validators

      quicksilverd tx distribution withdraw-all-rewards --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

2 Withdraw commission and rewards from your validator

      quicksilverd tx distribution withdraw-rewards $(quicksilverd keys show wallet --bech val -a) --commission --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

3 Delegate tokens to yourself

      quicksilverd tx staking delegate $(quicksilverd keys show wallet --bech val -a) 1000000uqck --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

4 Delegate tokens to validator

      quicksilverd tx staking delegate <TO_VALOPER_ADDRESS> 1000000uqck --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

5 Redelegate tokens to another validator

      quicksilverd tx staking redelegate $(quicksilverd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uqck --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

6 Unbond tokens from your validator

      quicksilverd tx staking unbond $(quicksilverd keys show wallet --bech val -a) 1000000uqck --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

7 Send tokens to the wallet

      quicksilverd tx bank send wallet <TO_WALLET_ADDRESS> 1000000uqck --from wallet --chain-id quicksilver-2

🗳 Governance

1 List all proposals

      quicksilverd query gov proposals

2 View proposal by id

      quicksilverd query gov proposal 1

3 Vote 'Yes'

      quicksilverd tx gov vote 1 yes --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

4 Vote 'No'

      quicksilverd tx gov vote 1 no --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

5 Vote 'Abstain'

      quicksilverd tx gov vote 1 abstain --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

6 Vote 'NoWithVeto'

      quicksilverd tx gov vote 1 nowithveto --from wallet --chain-id quicksilver-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y

⚡️ Utility

Update ports

      CUSTOM_PORT=10
      sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.quicksilverd/config/app.toml

Update Indexer

1 Disable indexer

      sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.quicksilverd/config/config.toml

2 Enable indexer

      sed -i 's|^indexer *=.*|indexer = "kv"|' $HOME/.quicksilverd/config/config.toml

3 Update pruning

      sed -i.bak -e 's|^pruning *=.*|pruning = "custom"|; s|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|; s|^pruning-keep-every *=.*|pruning-keep-every = "0"|; s|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.quicksilverd/config/app.toml
  
🚨 Maintenance

1 Get validator info

      quicksilverd status 2>&1 | jq .ValidatorInfo

2 Get sync info

      quicksilverd status 2>&1 | jq .SyncInfo

3 Get node peer

       echo $(quicksilverd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.quicksilverd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')

4 Check if validator key is correct

      [[ $(quicksilverd q staking validator $(quicksilverd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(quicksilverd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"

5 Get live peers

      curl -sS http://localhost:11657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'

6 Set minimum gas price

      sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uqck\"/" $HOME/.quicksilverd/config/app.toml

7 Enable prometheus

      sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.quicksilverd/config/config.toml

8 Reset chain data

       quicksilverd tendermint unsafe-reset-all --home $HOME/.quicksilverd --keep-addr-book

9 Remove node

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!

      cd $HOME
      sudo systemctl stop quicksilverd
      sudo systemctl disable quicksilverd
      sudo rm /etc/systemd/system/quicksilverd.service
      sudo systemctl daemon-reload
      rm -rf $(which quicksilverd) 
      rm -rf $HOME/.quicksilverd
      rm -rf $HOME/quicksilver

⚙️ Service Management

1 Reload service configuration

      sudo systemctl daemon-reload

2 Enable service

      sudo systemctl enable quicksilverd

3 Disable service

      sudo systemctl disable quicksilverd

4 Start service

      sudo systemctl start quicksilverd

5 Stop service

      sudo systemctl stop quicksilverd

6 Restart service

      sudo systemctl restart quicksilverd

7 Check service status

      sudo systemctl status quicksilverd

8 Check service logs

      sudo journalctl -u quicksilverd -f --no-hostname -o cat
