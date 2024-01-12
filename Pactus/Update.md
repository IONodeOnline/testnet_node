![image](https://github.com/IONodeOnline/testnet_node/assets/91251550/88adc97c-d958-4058-90e5-a76e1ab92054)


### Pactus 0.20.0 Released

11 January 2024

## Overview

## Validator Stake Management

![image](https://github.com/IONodeOnline/testnet_node/assets/91251550/b2c29a1a-6c75-4d60-b3a0-086a605969ad)

## Improving Network Connectivity

Some users have reported that UDP packets are being dropped by their Internet Service Providers. Therefore, the UDP protocol is disabled by default. Users can still activate the UDP protocol by configuring the settings in the file if necessary.

Also, the experimental Node Gossip feature has been removed in this version. Node gossip could cause some validators to become isolated from the rest of the network.


## Relay service

This version enables users to turn their node into a relay service. A relay service node can help users behind NAT to communicate with each other. It is disabled by default and can be enabled inside the config file. Please note that it is still in an experimental state.

## Download

This version has been implemented for Testnet-2 and is being prepared for the upcoming Mainnet launch. To start using Pactus Blockchain Testnet-2, download the latest version from the download page and join the Testnet.

These guides can help you to download, install and configure your node:

How to run Pactus in graphic mode? (https://pactus.org/2024/01/11/release-0-20-0.html#:~:text=How%20to%20run%20Pactus%20in%20graphic%20mode%3F)

How to run Pactus in command line mode? (https://pactus.org/user-guides/run-pactus-cli)

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

## Feat

implement relay service (#931)

HTTP: Integrate AddRowDouble and update tests (#926)

network: making listen address private in config (#921)

http: adding AvailabilityScore to http module (#917)

network: adding ‘enable_udp’ config (#918)

network: removing gossip node service (#916)

gRPC: adding AvailabilityScore to gRPC (#910)

GUI: unbond and withdraw transaction dialogs (#908)

## Fix

gRPC: adding missing get raw transaction APIs to gRPC gateway (#925)

network: preventing self dial (#924)

fixing time lag on starting node (#923)

network: fixing network deadlock on linux arm64 (#922)

GUI: updating unbond and withdraw dialogs (#911)

fixing gRPC node info issue (#906)

You can find the full list of changes on Github, as well as the source code

### Ready to Experience the Upgrade? Download here: https://pactus.org/download/

## Pactus Command Line Interface (CLI)

Advanced users can run Pactus in command line interface (CLI) mode.

|                    File name                       |      OS      |      Arch     |                     
|----------------------------------------------------|--------------|---------------|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_android_arm64.tar.gz|Android|ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_darwin_amd64.tar.gz|macOS|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_darwin_arm64.tar.gz|macOS|	ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_freebsd_amd64.tar.gz|FreeBSD|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_freebsd_arm.tar.gz|FreeBSD|ARM|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_linux_amd64.tar.gz|Linux|64-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_linux_arm64.tar.gz|Linux|	ARM64|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_windows_386.zip|	Windows|	32-bit|
|https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_windows_amd64.zip|	Windows|	64-bit|

Once you have downloaded the Pactus CLI, you may follow this tutorial to set up and run your node: How to run Pactus in Command Line Mode?(https://pactus.org/user-guides/run-pactus-cli)

## Downloader script

For Unix-like systems (e.g., Linux, macOS, or MSYS2 on Windows), there is a downloader script available. This script can download the archived file, verify it, and extract it for you. To use it, simply run the following command in your terminal:

      https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_windows_amd64.zip

## Upgrade

      [curl --proto '=https' --tlsv1.2 -sSL https://github.com/pactus-project/pactus/releases/download/v0.19.0/pactus_downloader.sh | sh
      mv pactus-cli_0.19.0/pactus* /usr/local/bin/
      pactus-daemon version
      rm -rf pactus-cli_0.19.0 	
      pactus-daemon start -w /root/pactus-working](https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-       
      cli_0.20.0_windows_amd64.zip)https://github.com/pactus-project/pactus/releases/download/v0.20.0/pactus-cli_0.20.0_windows_amd64.zip

