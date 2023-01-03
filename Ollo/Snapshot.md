Snapshot

Catch the latest block faster by using our daily snapshots.

![image](https://user-images.githubusercontent.com/91251550/210381985-6ad8b8df-827e-4788-9f1c-04b93d7a2494.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at 02:45 UTC

pruning: 100/0/19 | indexer: null | version tag: v0.0.1

BLOCK        AGE            DOWNLOAD      
854335      4 minutes       https://snapshots.kjnodes.com/ollo-testnet/snapshot_latest.tar.lz4
   
​​
Instructions

1 Stop the service and reset the data

      sudo systemctl stop ollod
      cp $HOME/.ollo/data/priv_validator_state.json $HOME/.ollo/priv_validator_state.json.backup
      rm -rf $HOME/.ollo/data
      
2 Download latest snapshot

      curl -L https://snapshots.kjnodes.com/ollo-testnet/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.ollo
      mv $HOME/.ollo/priv_validator_state.json.backup $HOME/.ollo/data/priv_validator_state.json
      
3 Restart the service and check the log

      sudo systemctl start ollod && journalctl -u ollod -f --no-hostname -o cat
