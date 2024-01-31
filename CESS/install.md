# Testnet v0.7.6
## https://medium.com/@CESS_LAB/cess-cumulus-encrypted-storage-system-incentive-testnet-v0-7-6-will-launch-on-jan-31th-12aa95ad0aa9
## Doc: https://docs.cess.cloud/core/storage-miner/running
## Install cess-nodeadm v0.5.3
```bash
wget https://github.com/CESSProject/cess-nodeadm/archive/v0.5.3.tar.gz
tar -xvzf v0.5.3.tar.gz
cd cess-nodeadm-0.5.3/
./install.sh
```
## Install storage node
```bash
cess profile testnet
cess config set
cess pullimg
cess version
cess start
```
## Check docker & logs
```bash
docker ps
docker logs -f chain
docker logs -f bucket
```
## option: if setup is error, please try again
```bash
cess stop
cess down
cess purge
cess pullimg
cess config set
cess start
```
