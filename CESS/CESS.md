#### Instructions for installing cess

#### Installation

        wget https://github.com/CESSProject/cess-nodeadm/archive/v0.4.4.tar.gz
        tar -xvf v0.4.4.tar.gz
        cd cess-nodeadm-0.4.4/
        ./install.sh

#### Must profile testnet, sometimes source team to devnet   

        cess profile testnet
        cess config set
        cess start
        
#### How to change default port (if desired)

        nano /opt/cess/nodeadm/build/docker-compose.yaml
        => 9933=>aaaa ; 9944=>bbbb
        nano /opt/cess/storage/bucket/config.yaml
        => 9944=>aaaa
        nano /opt/cess/nodeadm/config.yaml
        => 9944 =>bbbb  
        
#### How to delete node cess

        cess stop
        cess down -v
        docker image ls
        docker image rm -f <ID image>
        rm -rf /home/<user>/cess-nodeadm-0.4.4/
        rm -rf /opt/cess/
