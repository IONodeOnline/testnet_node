State sync

With our state sync services you will be able to catch up latest chain block in matter of minutes

![image](https://user-images.githubusercontent.com/91251550/210761127-3dbdb206-12a9-44b2-80ba-ca7d1a4acde7.png)

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

Instructions

1 Stop the service and reset the data

      sudo systemctl stop quicksilverd
      cp $HOME/.quicksilverd/data/priv_validator_state.json $HOME/.quicksilverd/priv_validator_state.json.backup
      quicksilverd tendermint unsafe-reset-all --home $HOME/.quicksilverd
      
2 Get and configure the state sync information

      STATE_SYNC_RPC=https://quicksilver.rpc.kjnodes.com:443
      STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@quicksilver.rpc.kjnodes.com:11656
      LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
      SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
      SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)
      ​
      sed -i.bak -e "s|^enable *=.*|enable = true|" $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
        $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
        $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
        $HOME/.quicksilverd/config/config.toml
      sed -i.bak -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
        $HOME/.quicksilverd/config/config.toml
      mv $HOME/.quicksilverd/priv_validator_state.json.backup $HOME/.quicksilverd/data/priv_validator_state.json

3 Restart the service and check the log

      sudo systemctl start quicksilverd && journalctl -u quicksilverd -f --no-hostname -o cat
