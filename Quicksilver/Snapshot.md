Snapshot

Catch the latest block faster by using our daily snapshots.

![image](https://user-images.githubusercontent.com/91251550/210760652-bd509446-edea-49d5-a815-04140c04eea3.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at 02:15 UTC

pruning: 100/0/19 | indexer: null | version tag: v1.2.0

BLOCK         AGE           DOWNLOAD   
20321         5 hours       https://snapshots.kjnodes.com/quicksilver/snapshot_latest.tar.lz4

​​
Instructions

1 Stop the service and reset the data

      sudo systemctl stop quicksilverd
      cp $HOME/.quicksilverd/data/priv_validator_state.json $HOME/.quicksilverd/priv_validator_state.json.backup
      rm -rf $HOME/.quicksilverd/data
       
2 Download latest snapshot

      curl -L https://snapshots.kjnodes.com/quicksilver/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.quicksilverd
      mv $HOME/.quicksilverd/priv_validator_state.json.backup $HOME/.quicksilverd/data/priv_validator_state.json
      
3 Restart the service and check the log

      sudo systemctl start quicksilverd && journalctl -u quicksilverd -f --no-hostname -o cat
