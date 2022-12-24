Upgrade

Prepare for and the upcomming chain upgrade using Cosmovisor.

Chain ID: haqq_54211-3 | Latest Version Tag: v1.3.0 | Custom Port: 35

! Since we are using Cosmovisor, it makes it very easy to prepare for upcomming upgrade. You just have to build new binaries and move it into cosmovisor upgrades directory.

Download and build upgrade binaries

# Clone project repository
           cd $HOME
           rm -rf haqq
           git clone https://github.com/haqq-network/haqq.git
           cd haqq

​
# Build binaries
           git checkout v1.3.0
           make build
           mkdir -p $HOME/.haqqd/cosmovisor/upgrades/v1.3.0/bin
           mv build/haqqd $HOME/.haqqd/cosmovisor/upgrades/v1.3.0/bin/
           rm -rf build

Thats it! Now when upgrade block height is reached, Cosmovisor will handle it automatically!
