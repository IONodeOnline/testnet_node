![image](https://github.com/IONodeOnline/testnet_node/assets/91251550/88adc97c-d958-4058-90e5-a76e1ab92054)


### Pactus 0.19.0 Released

04 January 2024

## Overview

Pactus Blockchain Version 0.19.0 is now available for download. This release includes a graphical user interface (GUI) that is suitable for both beginners and experienced users. It also includes a command-line interface (CLI) for advanced users and wallet software that allows users to interact with the blockchain without the need to sync the entire blockchain.

## Highlights

This version has improved the overall performance of the blockchain by implementing PIP-15. PIP-15 suggests to add caching algorithms to the database for the most frequently used items.

Additionally, this version has enhanced the connectivity of the nodes in the P2P network.

## Availability Score

This version comes with the implementation of PIP-19. PIP-19 suggests assigning the “Availability Score” to each validator based on their activities in the committee. Validators with a score less than 0.8 won’t receive the block reward, as their proposals will be rejected by other validators. The main reason behind this PIP is to prevent users from downgrading their node specifications and running Pactus on machines with low resources.

## Breaking change

The Docker container now contains both daemon and wallet binary files. Users who use Docker to run Pacts now need to add the entry point to run Pactus.

## Download

This version has been implemented for Testnet-2 and is being prepared for the upcoming Mainnet launch. To start using Pactus Blockchain Testnet-2, download the latest version from the download page and join the Testnet.

These guides can help you to download, install and configure your node:

How to run Pactus in graphic mode?

How to run Pactus in graphic mode? (https://pactus.org/2024/01/04/release-0-19-0.html#:~:text=This%20version%20has%20been,Pactus%20in%20graphic%20mode%3F)

How to run Pactus in command line mode? (https://pactus.org/2024/01/04/release-0-19-0.html#:~:text=How%20to%20run%20Pactus%20in%20command%20line%20mode%3F)

How to run Pactus using Docker? (https://pactus.org/user-guides/run-pactus-docker)

## How to Upgrade

If you are running an older version, shut it down. Uninstall the previous version and install the newer version. If you are using the archived version, simply use the new version.

## Change log

This version includes several changes and improvements, such as:

## Feat

gRPC: defining network and peers info response’s properly (#898)
implementing pip-19 (#899)
network: disabling GosipSub, only FloodSub (#895)
www: adding change proposer round and value to consensus info votes (#892)
network: adding relay service to dial relay nodes (#887)
implementing pip-15 (#843)
check already running by lock file (#871)

## Fix

store: use cache to check if public key exists (#902)
executor: not rejecting bond transaction for bootstrap validator (#901)
GUI: removing unnecessary tags in transaction confirm dialog (#893)
network: close relay connection for public node (#891)
network: refining GossipSubParams for Gossiper node (#882)
sync: adding sequence number to the bundle (#881)
network: turn off mesh for gossiper node (#880)
consensus: check voteset for CP strong termination (#879)
adding querier to query messages (#878)
execution: fixing issue #869 (#870)
fixing logger issue on rotating log file (#859)

You can find the full list of changes on Github, as well as the source code

### Ready to Experience the Upgrade? Download here: https://pactus.org/download/

## Pactus Command Line Interface (CLI)

Advanced users can run Pactus in command line interface (CLI) mode.

|                    File name                       |      OS      |      Arch     |                     
|----------------------------------------------------|--------------|---------------|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_android_arm64.tar.gz|Android|ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_darwin_amd64.tar.gz|macOS|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_darwin_arm64.tar.gz|macOS|	ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_freebsd_amd64.tar.gz|FreeBSD|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_freebsd_arm.tar.gz|FreeBSD|ARM|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_linux_amd64.tar.gz|Linux|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_linux_arm64.tar.gz|Linux|	ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_windows_386.zip|	Windows|	32-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus-cli_0.19.0_windows_amd64.zi|	Windows|	64-bit|

Once you have downloaded the Pactus CLI, you may follow this tutorial to set up and run your node: How to run Pactus in Command Line Mode?(https://pactus.org/user-guides/run-pactus-cli)

## Downloader script

For Unix-like systems (e.g., Linux, macOS, or MSYS2 on Windows), there is a downloader script available. This script can download the archived file, verify it, and extract it for you. To use it, simply run the following command in your terminal:

      curl --proto '=https' --tlsv1.2 -sSL https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus_downloader.sh | sh

## Upgrade

      curl --proto '=https' --tlsv1.2 -sSL https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus_downloader.sh | sh
      mv pactus-cli_0.19.0/pactus* /usr/local/bin/
      pactus-daemon version
      rm -rf pactus-cli_0.19.0 	
      pactus-daemon start -w /root/pactus-working

