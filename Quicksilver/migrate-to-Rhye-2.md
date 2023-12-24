# Migrate testnet : Rhye-1 to Rhye-2: v1.4.5-rc1
## stop service
```bash
sudo systemctl stop quicksilver.service && sudo journalctl -u quicksilver.service -f --no-hostname -o cat
```
## install go1.21.0
```bash
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.21.0.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
## Prepare binaries for quicksiver
```bash
cd $HOME
rm -rf quicksilver
git clone https://github.com/ingenuity-build/quicksilver.git
cd quicksilver
git checkout v1.4.5-rc1 
make build
mv build/quicksilverd $HOME/.quicksilverd/cosmovisor/genesis/bin/
rm -rf build
```
## unsafe reset all
```bash
quicksilverd tendermint unsafe-reset-all
```
## Create application symlinks
```bash
rm -rf .quicksilverd/cosmovisor/upgrades/v1.4.4-rc.3
sudo ln -s $HOME/.quicksilverd/cosmovisor/genesis $HOME/.quicksilverd/cosmovisor/current -f
sudo ln -s $HOME/.quicksilverd/cosmovisor/current/bin/quicksilverd /usr/local/bin/quicksilverd -f
rm .quicksilverd/cosmovisor/current/upgrade-info.json
```
## Set node configuration
```bash
sudo quicksilverd config chain-id rhye-2
```
## download genesis.json
```bash
wget -O $HOME/.quicksilverd/config/genesis.json https://testnet-files.itrocket.net/quicksilver/genesis.json
```
## start service
```bash
sudo systemctl start quicksilver.service && sudo journalctl -u quicksilver.service -f --no-hostname -o cat
```
## unjail validator
```bash
quicksilverd tx slashing unjail --from <wallet> --chain-id rhye-2 --gas-adjustment 1.4 --gas auto --gas-prices 0.0001uqck -y
```
