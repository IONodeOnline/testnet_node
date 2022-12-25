Snapshot

![image](https://user-images.githubusercontent.com/91251550/209468673-f60ed7c9-d9ea-4ce7-ab12-1ea2f490a79a.png)

Catch the latest block faster by using our daily snapshots
.
Snapshots allows a new node to join the network by recovering application state from a backup file. Snapshot contains compressed copy of chain data directory. To keep backup files as small as plausible, snapshot server is periodically beeing state-synced.

Snapshots are taken automatically each day at 08:30 UTC
pruning: 100/0/19 | indexer: null | version tag: v1.3.0

                                   BLOCK               AGE              DOWNLOAD   
                                   1524295             4 hours ago      https://snapshots.kjnodes.com/haqq-testnet/snapshot_latest.tar.lz4
​​
Instructions

Stop the service and reset the data
           sudo systemctl stop haqqd
           cp $HOME/.haqqd/data/priv_validator_state.json $HOME/.haqqd/priv_validator_state.json.backup
           rm -rf $HOME/.haqqd/data
Download latest snapshot
           curl -L https://snapshots.kjnodes.com/haqq-testnet/snapshot_latest.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.haqqd
           mv $HOME/.haqqd/priv_validator_state.json.backup $HOME/.haqqd/data/priv_validator_state.json
Restart the service and check the log
           sudo systemctl start haqqd && journalctl -u haqqd -f --no-hostname -o cat
