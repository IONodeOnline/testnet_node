State sync

With our state sync services you will be able to catch up latest chain block in matter of minutes

![image](https://user-images.githubusercontent.com/91251550/213096710-c95875ad-d4f8-40a8-b4e8-ddf2c69bdf03.png)

State Sync allows a new node to join the network by fetching a snapshot of the application state at a recent height instead of fetching and replaying all historical blocks. Since the application state is generally much smaller than the blocks, and restoring it is much faster than replaying blocks, this can reduce the time to sync with the network from days to minutes.

Instructions

1 Stop the service and reset the data

      sudo systemctl stop marsd
      cp $HOME/.mars/data/priv_validator_state.json $HOME/.mars/priv_validator_state.json.backup
      marsd tendermint unsafe-reset-all --home $HOME/.mars

2 Get and configure the state sync information

      STATE_SYNC_RPC=https://mars-testnet.rpc.kjnodes.com:443
      STATE_SYNC_PEER=d5519e378247dfb61dfe90652d1fe3e2b3005a5b@mars-testnet.rpc.kjnodes.com:45656
      LATEST_HEIGHT=$(curl -s $STATE_SYNC_RPC/block | jq -r .result.block.header.height)
      SYNC_BLOCK_HEIGHT=$(($LATEST_HEIGHT - 2000))
      SYNC_BLOCK_HASH=$(curl -s "$STATE_SYNC_RPC/block?height=$SYNC_BLOCK_HEIGHT" | jq -r .result.block_id.hash)
      ​
      sed -i \
        -e "s|^enable *=.*|enable = true|" \
        -e "s|^rpc_servers *=.*|rpc_servers = \"$STATE_SYNC_RPC,$STATE_SYNC_RPC\"|" \
        -e "s|^trust_height *=.*|trust_height = $SYNC_BLOCK_HEIGHT|" \
        -e "s|^trust_hash *=.*|trust_hash = \"$SYNC_BLOCK_HASH\"|" \
        -e "s|^persistent_peers *=.*|persistent_peers = \"$STATE_SYNC_PEER\"|" \
        $HOME/.mars/config/config.toml
      ​
      mv $HOME/.mars/priv_validator_state.json.backup $HOME/.mars/data/priv_validator_state.json

3 Download latest wasm

      Currently state sync does not support copy of the wasm folder. Therefore, you will have to download it manually.
      curl -L https://snapshots.kjnodes.com/mars-testnet/wasm_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.mars

4 Restart the service and check the log

      sudo systemctl start marsd 
