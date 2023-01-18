Useful commands

Useful set of commands for node operators. From key management to chain governance.

![image](https://user-images.githubusercontent.com/91251550/213081400-93aadbf1-6b97-425a-bb84-b963576eb210.png)

🔑 Key management

1 Add new key

      defundd keys add wallet

2 Recover existing key

      defundd keys add wallet --recover

3 List all keys

      defundd keys list

4 Delete key

      defundd keys delete wallet

5 Export key to the file

      defundd keys export wallet

6 Import key from the file

      defundd keys import wallet wallet.backup

7 Query wallet balance

      defundd q bank balances $(defundd keys show wallet -a)

👷 Validator management

Please make sure you have adjusted moniker, identity, details and website to match your values.
 
1 Create new validator

      defundd tx staking create-validator \
      --amount=1000000ufetf \
      --pubkey=$(defundd tendermint show-validator) \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL" \
      --chain-id=defund-private-4 \
      --commission-rate=0.05 \
      --commission-max-rate=0.20 \
      --commission-max-change-rate=0.01 \
      --min-self-delegation=1 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0ufetf \
      -y
2 Edit existing validator

      defundd tx staking edit-validator \
      --moniker="YOUR_MONIKER_NAME" \
      --identity="YOUR_KEYBASE_ID" \
      --details="YOUR_DETAILS" \
      --website="YOUR_WEBSITE_URL"
      --chain-id=defund-private-4 \
      --commission-rate=0.05 \
      --from=wallet \
      --gas-adjustment=1.4 \
      --gas=auto \
      --gas-prices=0ufetf \
      -y
      
3 Unjail validator

      defundd tx slashing unjail --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y
4 Jail reason

      defundd query slashing signing-info $(defundd tendermint show-validator)
      
5 List all active validators

      defundd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

6 List all inactive validators
      
      defundd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

7 View validator details

      defundd q staking validator $(defundd keys show wallet --bech val -a)

💲 Token management

1 Withdraw rewards from all validators

      defundd tx distribution withdraw-all-rewards --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

2 Withdraw commission and rewards from your validator

      defundd tx distribution withdraw-rewards $(defundd keys show wallet --bech val -a) --commission --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

3 Delegate tokens to yourself

      defundd tx staking delegate $(defundd keys show wallet --bech val -a) 1000000ufetf --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

4 Delegate tokens to validator

      defundd tx staking delegate <TO_VALOPER_ADDRESS> 1000000ufetf --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

5 Redelegate tokens to another validator

      defundd tx staking redelegate $(defundd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000ufetf --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

6 Unbond tokens from your validator

      defundd tx staking unbond $(defundd keys show wallet --bech val -a) 1000000ufetf --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

7 Send tokens to the wallet

      defundd tx bank send wallet <TO_WALLET_ADDRESS> 1000000ufetf --from wallet --chain-id defund-private-4

🗳 Governance

1 List all proposals

      defundd query gov proposals

2 View proposal by id

      defundd query gov proposal 1

3 Vote 'Yes'

      defundd tx gov vote 1 yes --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

4 Vote 'No'

      defundd tx gov vote 1 no --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

5 Vote 'Abstain'
  
      defundd tx gov vote 1 abstain --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

6 Vote 'NoWithVeto'

      defundd tx gov vote 1 nowithveto --from wallet --chain-id defund-private-4 --gas-adjustment 1.4 --gas auto --gas-prices 0ufetf -y

⚡️ Utility

1 Update ports

      CUSTOM_PORT=10
      sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.defund/config/config.toml
      sed -i -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.defund/config/app.toml
2 Update Indexer

2.1 Disable indexer

      sed -i -e 's|^indexer *=.*|indexer = "null"|' $HOME/.defund/config/config.toml
      
2.2 Enable indexer

      sed -i -e 's|^indexer *=.*|indexer = "kv"|' $HOME/.defund/config/config.toml

2.3 Update pruning

      sed -i \
        -e 's|^pruning *=.*|pruning = "custom"|' \
        -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
        -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
        -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
        $HOME/.defund/config/app.toml
        
🚨 Maintenance

1 Get validator info

      defundd status 2>&1 | jq .ValidatorInfo

2 Get sync info

      defundd status 2>&1 | jq .SyncInfo

3 Get node peer

      echo $(defundd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.defund/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')

4 Check if validator key is correct

      [[ $(defundd q staking validator $(defundd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(defundd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"

5 Get live peers

      curl -sS http://localhost:40657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'

6 Set minimum gas price

      sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ufetf\"/" $HOME/.defund/config/app.toml

7 Enable prometheus

      sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.defund/config/config.toml

8 Reset chain data

      defundd tendermint unsafe-reset-all --home $HOME/.defund --keep-addr-book

9 Remove node

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!

      cd $HOME
      sudo systemctl stop defundd
      sudo systemctl disable defundd
      sudo rm /etc/systemd/system/defundd.service
      sudo systemctl daemon-reload
      rm -f $(which defundd)
      rm -rf $HOME/.defund
      rm -rf $HOME/defund

⚙️ Service Management

1 Reload service configuration

      sudo systemctl daemon-reload

2 Enable service

      sudo systemctl enable defundd

3 Disable service

      sudo systemctl disable defundd

4 Start service

      sudo systemctl start defundd

5 Stop service

      sudo systemctl stop defundd

6 Restart service

      sudo systemctl restart defundd

7 Check service status

      sudo systemctl status defundd

8 Check service logs

      sudo journalctl -u defundd -f --no-hostname -o cat
