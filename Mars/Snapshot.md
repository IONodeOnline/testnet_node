Snapshot

Catch the latest block faster by using our daily snapshots.

![image](https://user-images.githubusercontent.com/91251550/213096377-ddb34c02-5f61-42b6-a836-180757dbabff.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at 06:00 UTC

pruning: 100/0/19 | indexer: null | version tag: v1.0.0-rc7

BLOCK: 61833
AGE :48 minutes
DOWNLOAD:https://snapshots.kjnodes.com/mars-testnet/snapshot_latest.tar.lz4

​​
Instructions

1 Stop the service and reset the data

      sudo systemctl stop marsd
      cp $HOME/.mars/data/priv_validator_state.json $HOME/.mars/priv_validator_state.json.backup
      rm -rf $HOME/.mars/data

2 Download latest snapshot

      curl -L https://snapshots.kjnodes.com/mars-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.mars
      mv $HOME/.mars/priv_validator_state.json.backup $HOME/.mars/data/priv_validator_state.json

3 Restart the service and check the log
      sudo systemctl start marsd &&
