Instructions for installing cess

Installation

        wget https://github.com/CESSProject/cess-nodeadm/archive/v0.4.4.tar.gz
        tar -xvf v0.4.4.tar.gz
        cd cess-nodeadm-0.4.4/
        ./install.sh

// Must profile testnet, sometimes source team to devnet    

        cess down -v
        cess profile testnet
        cess config set
        cess reload

        nano /opt/cess/nodeadm/build/docker-compose.yaml
        => 9933=>9993 ; 9944=>9994
        nano /opt/cess/storage/bucket/config.yaml
        => 9944=>9994
        nano /opt/cess/nodeadm/config.yaml
        => 9944 => 9994      

 Fix error
        lease configure the public ip => due to using ipv6 but cess does not yet support it
        nano /etc/sysctl.conf 
        
\\add these 3 lines

        net.ipv6.conf.all.disable_ipv6 = 1cess down
        net.ipv6.conf.default.disable_ipv6 = 1
        net.ipv6.conf.lo.disable_ipv6 = 1
        net.ipv6.conf.enP1091s1.disable_ipv6 = 1
        net.ipv6.conf.eth0.disable_ipv6 = 1
        
\\note that whichever network card you use, you must write down the correct name of that network card for it to work: eg: wlo1

        net.ipv6.conf.wlo1.disable_ipv6 = 1
        net.ipv6.conf.ens3.disable_ipv6 = 1
        
\\check and run this command again when restarting the computer but the bucket still reports the error [cess.New] please configure the public ip

        sudo sysctl -p
        
\\issue "XX 2023-09-21 11:57:56 Account does not exist or balance is empty" , but I sure account have exist and balance isn't empty

        cess down -v or update external IP:
        #nano /opt/cess/nodeadm/config.yaml => set devnet to testnet

How to delete node cess

        cess stop
        cess down -v
        docker image ls
        docker image rm -f
        rm -rf /home/azureuser/cess-nodeadm-0.4.4/
        rm -rf /opt/cess/
