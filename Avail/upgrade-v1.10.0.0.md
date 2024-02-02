# update v1.10.0.0 :  https://github.com/availproject/avail/releases/tag/v1.10.0.0
## delete containner 
### if docker 
sudo docker rm -f YOUR_CONTIANER
### if docker compose
docker compose down -v
## delete db & network folder
rm -rf xxx/.../db
rm -rf xxx/.../network
## restore new db
### make xxx/.../db/full folder
### cd to xxx/.../db/full folder
### restore new db: 
curl -o - -L https://snapshots.avail.nexus/goldberg/avail_goldberg_testnet_snapshot_jan_31.tar.gz | tar -xz -C .
### make new session key
### after finished synced to validator mode and start containner
## Update new session key in exploer
