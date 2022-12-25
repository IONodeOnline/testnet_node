Useful commands

Useful set of commands for node operators. From key management to chain governance.

![image](https://user-images.githubusercontent.com/91251550/209468509-554d5dcd-1d10-4956-b1e8-c548658cd87c.png)

🔑 Key management

1 Add new key

           haqqd keys add wallet

2 Recover existing key

           haqqd keys add wallet --recover

3 List all keys

           haqqd keys list

4 Delete key

           haqqd keys delete wallet

5 Export key to the file

           haqqd keys export wallet

6 Import key from the file

           haqqd keys import wallet wallet.backup
           
7 Query wallet balance

           haqqd q bank balances $(haqqd keys show wallet -a)

👷 Validator management

Please make sure you have adjusted moniker, identity, details and website to match your values.

1 Create new validator

            haqqd tx staking create-validator \
            --amount=1000000aISLM \
            --pubkey=$(haqqd tendermint show-validator) \
            --moniker="YOUR_MONIKER_NAME" \
            --identity="YOUR_KEYBASE_ID" \
            --details="YOUR_DETAILS" \
            --website="YOUR_WEBSITE_URL"
            --chain-id=haqq_54211-3 \
            --commission-rate=0.05 \
            --commission-max-rate=0.20 \
            --commission-max-change-rate=0.01 \
            --min-self-delegation=1 \
            --from=wallet \
            --gas-adjustment=1.4 \
            --gas=auto \
            -y

2 Edit existing validator

            haqqd tx staking edit-validator \
            --moniker="YOUR_MONIKER_NAME" \
            --identity="YOUR_KEYBASE_ID" \
            --details="YOUR_DETAILS" \
            --website="YOUR_WEBSITE_URL"
            --chain-id=haqq_54211-3 \
            --commission-rate=0.05 \
            --from=wallet \
            --gas-adjustment=1.4 \
            --gas=auto \
            -y

3 Unjail validator

            haqqd tx slashing unjail --from wallet --chain-id haqq_54211-3 --gas auto --gas-adjustment 1.4 -y

4 Jail reason

            haqqd query slashing signing-info $(haqqd tendermint show-validator)

5 List all active validators

            haqqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

6 List all inactive validators

            haqqd q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl

7 View validator details

            haqqd q staking validator $(haqqd keys show wallet --bech val -a)

💲 Token management

1 Withdraw rewards from all validators

            haqqd tx distribution withdraw-all-rewards --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

2 Withdraw commission and rewards from your validator

            haqqd tx distribution withdraw-rewards $(haqqd keys show wallet --bech val -a) --commission --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

3 Delegate tokens to yourself

            haqqd tx staking delegate $(haqqd keys show wallet --bech val -a) 1000000aISLM --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

4 Delegate tokens to validator

            haqqd tx staking delegate <TO_VALOPER_ADDRESS> 1000000aISLM --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

5 Redelegate tokens to another validator

            haqqd tx staking redelegate $(haqqd keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000aISLM --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

6 Unbond tokens from your validator

            haqqd tx staking unbond $(haqqd keys show wallet --bech val -a) 1000000aISLM --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

7 Send tokens to the wallet

            haqqd tx bank send wallet <TO_WALLET_ADDRESS> 1000000aISLM --from wallet --chain-id haqq_54211-3

🗳 Governance
1 List all proposals

            haqqd query gov proposals

2 View proposal by id

            haqqd query gov proposal 1

3 Vote 'Yes'

            haqqd tx gov vote 1 yes --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

4 Vote 'No'

            haqqd tx gov vote 1 no --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

5 Vote 'Abstain'

            haqqd tx gov vote 1 abstain --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

6 Vote 'NoWithVeto'

            haqqd tx gov vote 1 nowithveto --from wallet --chain-id haqq_54211-3 --gas-adjustment 1.4 --gas auto -y

⚡️ Utility

1 Update ports

            CUSTOM_PORT=10
            sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${CUSTOM_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${CUSTOM_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${CUSTOM_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${CUSTOM_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${CUSTOM_PORT}660\"%" $HOME/.haqqd/config/config.toml
            sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${CUSTOM_PORT}317\"%; s%^address = \":8080\"%address = \":${CUSTOM_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${CUSTOM_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${CUSTOM_PORT}091\"%" $HOME/.haqqd/config/app.toml
            2 Update Indexer

            #Disable indexer
            sed -i 's|^indexer *=.*|indexer = "null"|' $HOME/.haqqd/config/config.toml
            Enable indexer
            sed -i 's|^indexer *=.*|indexer = "kv"|' $HOME/.haqqd/config/config.toml
            #Update pruning
            sed -i.bak -e 's|^pruning *=.*|pruning = "custom"|; s|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|; s|^pruning-keep-every *=.*|pruning-keep-every = "0"|; s|^pruning-interval *=.*|pruning-interval = "10"|' $HOME/.haqqd/config/app.toml
🚨 Maintenance

1 Get validator info

            haqqd status 2>&1 | jq .ValidatorInfo

2 Get sync info

            haqqd status 2>&1 | jq .SyncInfo

3 Get node peer

            echo $(haqqd tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.haqqd/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
            Check if validator key is correct
            [[ $(haqqd q staking validator $(haqqd keys show wallet --bech val -a) -oj | jq -r .consensus_pubkey.key) = $(haqqd status | jq -r .ValidatorInfo.PubKey.value) ]] && echo -e "\n\e[1m\e[32mTrue\e[0m\n" || echo -e "\n\e[1m\e[31mFalse\e[0m\n"

4 Get live peers

            curl -sS http://localhost:35657/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr)"' | awk -F ':' '{print $1":"$(NF)}'

5 Set minimum gas price

            sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0aISLM\"/" $HOME/.haqqd/config/app.toml

6 Enable prometheus

            sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.haqqd/config/config.toml

7 Reset chain data

            haqqd tendermint unsafe-reset-all --home $HOME/.haqqd --keep-addr-book
            
8 Remove node

Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your priv_validator_key.json!
cd $HOME

            sudo systemctl stop haqqd
            sudo systemctl disable haqqd
            sudo rm /etc/systemd/system/haqqd.service
            sudo systemctl daemon-reload
            rm -rf $(which haqqd) 
            rm -rf $HOME/.haqqd
            rm -rf $HOME/haqq

⚙️ Service Management

1 Reload service configuration

            sudo systemctl daemon-reload

2 Enable service

            sudo systemctl enable haqqd

3 Disable service

            sudo systemctl disable haqqd

4 Start service

            sudo systemctl start haqqd

5 Stop service

            sudo systemctl stop haqqd

6 Restart service

            sudo systemctl restart haqqd

7 Check service status

            sudo systemctl status haqqd

8 Check service logs

            sudo journalctl -u haqqd -f --no-hostname -o cat
