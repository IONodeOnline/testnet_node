## UPGRADE
## Install unzip
```bash
sudo apt install unzip -y
```
## Install screen:
```bash
sudo apt install screen -y
```
## Control Screen:
cntrl+A +D - disconnect from the current session, the node will continue to work in the background

screen -S "name" - new screen

screen -r "name" - join the session in which the node is running

cntrl+A +ESC - scrolling mode

## Upgrade v0.33.2
```bash
  mkdir transformersV2
  cd transformersV2
  rm tfs_v0.33.1_92c26db_testnet
  wget -q -O tmp.zip https://fastcdn.uscloudmedia.com/transformers/formal/tfs_v0.33.2_bbf5ed5_testnet.zip && unzip tmp.zip && rm tmp.zip
  chmod +x tfs_v0.33.2_bbf5ed5_testnet
  ./tfs_v0.33.2_bbf5ed5_testnet -m
```
