Snapshot

Catch the latest block faster by using our daily snapshots.

![image](https://user-images.githubusercontent.com/91251550/213080733-458c9be1-2bad-4247-a7fa-61c4f291b1bb.png)

Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically every 6 hours starting at 04:30 UTC

pruning: 100/0/19 | indexer: null | version tag: v0.2.2

BLOCK                          AGE                    DOWLOAD

307545                         14 minutes             https://snapshots.kjnodes.com/defund-testnet/snapshot_latest.tar.lz4

​​

Instructions

1 Stop the service and reset the data

      sudo systemctl stop defundd
      cp $HOME/.defund/data/priv_validator_state.json $HOME/.defund/priv_validator_state.json.backup
      rm -rf $HOME/.defund/data
      
2 Download latest snapshot

      curl -L https://snapshots.kjnodes.com/defund-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.defund
      mv $HOME/.defund/priv_validator_state.json.backup $HOME/.defund/data/priv_validator_state.json
      
3 Restart the service and check the log

      sudo systemctl start defundd && sudo journalctl -u defundd -f --no-hostname -o cat
