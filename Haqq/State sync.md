State sync

![image](https://user-images.githubusercontent.com/91251550/209468693-86c78528-fa81-4142-96c1-5043d2e31fa9.png)

With our state sync services you will be able to catch up latest chain block in matter of minutes

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

Instructions

1 Stop the service and reset the data

                     sudo systemctl stop haqqd
                     cp $HOME/.haqqd/data/priv_validator_state.json $HOME/.haqqd/priv_validator_state.json.backup
                     haqqd tendermint unsafe-reset-all --home $HOME/.haqqd
2 Get and configure the state sync information

                     STATE_SYNC_RPC=https://haqq-testnet.rpc.kjnodes.com:443
                     STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@haqq-testnet.rpc.kjnodes.com:35656
                     LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
                     SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
                     SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)
                     ​
                    sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.haqqd/config/config.toml
                    sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
                       $HOME/.haqqd/config/config.toml
                    sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
                       $HOME/.haqqd/config/config.toml
                    sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
                       $HOME/.haqqd/config/config.toml
                    sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
                       $HOME/.haqqd/config/config.toml
                    mv $HOME/.haqqd/priv_validator_state.json.backup $HOME/.haqqd/data/priv_validator_state.json
3 Restart the service and check the log

                    sudo systemctl start haqqd && journalctl -u haqqd -f --no-hostname -o cat
