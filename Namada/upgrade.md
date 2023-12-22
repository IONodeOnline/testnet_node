## Upgrade to v0.28.1:
```bash
cd $HOME
wget https://github.com/anoma/namada/releases/download/v0.28.1/namada-v0.28.1-Linux-x86_64.tar.gz
tar -xvf namada-v0.28.1-Linux-x86_64.tar.gz
rm namada-v0.28.1-Linux-x86_64.tar.gz
cd namada-v0.28.1-Linux-x86_64
sudo mv namada namadan namadac namadaw /usr/local/bin/
sudo systemctl restart namadad && sudo journalctl -u namadad -f
```
