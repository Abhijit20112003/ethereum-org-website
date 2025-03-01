---
title: Spin up your own Ethereum node
description: General introduction to running your own instance of an Ethereum client.
lang: en
sidebar: true
sidebarDepth: 2
preMergeBanner: true
---

Running your own node provides you various benefits, opens new possibilities, and helps to support the ecosystem. This page will guide you through spinning up your own node and taking part in validating Ethereum transactions.

Note that after [The Merge](/upgrades/merge) a minimum of two clients are required to run an Ethereum node. These are an execution client and a consensus client. This page will show how to install, configure and connect these two clients to form an Ethereum node.

## Prerequisites {#prerequisites}

You should understand what an Ethereum node is and why you might want to run a client. This is covered in [Nodes and clients](/developers/docs/nodes-and-clients/).

If you're new to the topic of running a node, or looking for a less technical path, we recommend first checking out our user-friendly introduction on [running an Ethereum node](/run-a-node).

## Choosing an approach {#choosing-approach}

The first step in spinning up your node is choosing your approach. You have to choose the client (the software), the environment, and the parameters you want to start with.
This includes the hardware (NUC, laptop, virtual machine, etc), operating system (Linux, Windows, macOS, etc), client implementation and configuration. Users will have their own individual preferences for each option.

#### Client settings {#client-settings}

Client implementations enable different sync modes and various other options. [Sync modes](/developers/docs/nodes-and-clients/#sync-modes) represent different methods of downloading and validating blockchain data. Before starting the node, you should decide what network and sync mode to use. The most important things to consider is the disk space and sync time client will need. Note that after [The Merge](/upgrades/merge) a light execution client will no longer work - a full node will be required.

All features and options can be found in the client's documentation. Various client configurations can be set by executing the client with the corresponding flags. You can get more information on flags from the client documentation.

For testing purposes, you might prefer running a client on one of testnet networks. [See overview of supported networks](/developers/docs/nodes-and-clients/#execution-clients).

### Environment and hardware {#environment-and-hardware}

#### Local or cloud {#local-vs-cloud}

Ethereum clients are able to run on consumer grade computers and don't require special hardware, like mining for example. Therefore, you have various options for deploying based on your needs.
To simplify, let's think about running a node on both a local physical machine and a cloud server:

- Cloud
  - Providers offer high server uptime, static public IP addresses
  - Getting dedicated or virtual server can be more comfortable than building your own
  - Trade off is trusting a third party - server provider
  - Because of required storage size for full node, price of a rented server might get high
- Own hardware
  - More trustless and sovereign approach
  - One time investment
  - An option to buy preconfigured machines
  - You have to physically prepare, maintain, and potentially troubleshoot the machine

Both options have different advantages summed up above. If you are looking for a cloud solution, in addition to many traditional cloud computing providers, there are also services focused on deploying nodes. For example:

- [QuickNode](https://www.quicknode.com/)
- [Blockdaemon](https://blockdaemon.com)
- [LunaNode](https://www.lunanode.com/)
- [Alchemy](https://www.alchemy.com/)

#### Hardware {#hardware}

However, a censorship-resistant, decentralized network should not rely on cloud providers. It's healthier for the ecosystem if you run your own node on hardware. The easiest options are preconfigured machines like:

- [DappNode](https://dappnode.io/)
- [Avado](https://ava.do/)

Check the minimum and recommended [disk space requirements for each client and sync mod](/developers/docs/nodes-and-clients/#requirements).
Generally, modest computing power should be enough. The problem is usually drive speed. During initial sync, Ethereum clients perform a lot of read/write operations. Therefore SSD is strongly recommended. A client might not even [be able to sync current state on HDD](https://github.com/ethereum/go-ethereum/issues/16796#issuecomment-391649278) and get stuck a few blocks behind Mainnet.
You can run most of the clients on a [single board computer with ARM](/developers/docs/nodes-and-clients/#ethereum-on-a-single-board-computer/). You can also use the [Ethbian](https://ethbian.org/index.html) operating system for Raspberry Pi 4. This lets you [run a client by flashing the SD card](/developers/tutorials/run-node-raspberry-pi/).
Based on your software and the hardware choices, the initial synchronization time and storage requirements may vary. Be sure to [check sync times and storage requirements](/developers/docs/nodes-and-clients/#recommended-specifications).
Also make sure your internet connection is not limited by a [bandwidth cap](https://wikipedia.org/wiki/Data_cap). It's recommended to use an unmetered connection since initial sync and data broadcasted to the network could exceed your limit.

#### Operating system {#operating-system}

All clients support major operating systems - Linux, MacOS, Windows. This means you can run nodes on regular desktop or server machines with the operating system (OS) that suits you the best. Make sure your OS is up to date to avoid potential issues and security vulnerabilities.

## Spinning up the node {#spinning-up-node}

### Getting the execution client software {#getting-the-execution-client}

First, download your preferred [execution client software](/developers/docs/nodes-and-clients/#execution-clients)

You can simply download an executable application or installation package which suits your operating system and architecture. Always verify signatures and checksums of downloaded packages. Some clients also offer repositories for easier installation and updates.
If you prefer, you can build from source. All of the clients are open source so you can build them from source code with the proper compiler.

Executable binaries for stable Mainnet client implementations can be downloaded from their release pages:

- [Geth](https://geth.ethereum.org/downloads/)
- [OpenEthereum,](https://github.com/openethereum/openethereum/releases)
- [Nethermind](https://downloads.nethermind.io/)
- [Besu](https://besu.hyperledger.org/en/stable/)
- [Erigon](https://github.com/ledgerwatch/erigon)

It is also worth noting that client diversity is an [issue on the execution layer](https://clientdiversity.org/),
with Geth being run on a supermajority (>66%) of all Ethereum nodes. It is recommended that readers on this page consider
running a minority execution client.

**Note that OpenEthereum [has been deprecated](https://medium.com/openethereum/gnosis-joins-erigon-formerly-turbo-geth-to-release-next-gen-ethereum-client-c6708dd06dd) and is no longer being maintained.** Use it with caution and preferably switch to another client implementation.

### Starting the execution client {#starting-the-execution-client}

Before starting Ethereum client software, perform a last check that your environment is ready. For example, make sure:

- There is enough disk space considering chosen network and sync mode.
- Memory and CPU is not halted by other programs.
- Operating system is updated to latest version.
- System has correct time and date.
- Your router and firewall accept connections on listening ports. By default Ethereum clients use a listener (TCP) port and a discovery (UDP) port, both on 30303 by default.

Run your client on a testnet first to help make sure everything is working correctly. You need to declare any client settings that aren't default at the start.
In order to connect to a consensus client the execution client must generate a `jwtsecret` at a known path. This path must be known by both clients as it is used
to authenticate a local RPC connection between them. The execution client must also define a listening port for authenticated APIs.

**Note that it is recommended to connect an execution and consensus client on a testnet only for now (e.g. Kiln) and await merge-ready client releases before replicating the process on Mainnet.**

There are many ways to configure the execution client. You can use flags or the config file to declare your preferred configuration. Check out your client's documentation for the specific details.

Client execution will initiate its core functions, chosen endpoints, and start looking for peers. After successfully discovering peers, the client starts synchronization. Current blockchain data will be available once the client is successfully synced to the current state.

### Getting the consensus client {#getting-the-consensus-client}

There are currently five consensus clients to choose from. These are:

- [Lighthouse](https://lighthouse-book.sigmaprime.io/): written in Rust
- [Nimbus](https://nimbus.team/): written in Nim
- [Prysm](https://docs.prylabs.network/docs/getting-started/): written in Go
- [Teku](https://pegasys.tech/teku): written in Java

There is currently a [client diversity](/developers/docs/nodes-and-clients/client-diversity/) issue where a large dominance of Prysm clients poses a risk
to the health of the network. In response to the initial drive to even out the client diversity many Prysm nodes switched
to Lighthouse to the extent that it now also has a problematic market share. It is therefore recommended to consider choosing
a minority client. [See the latest network client usage](https://clientdiversity.org/)

There are several ways to download and install the consensus clients including prebuilt binaries, docker containers
or building from source. Instructions for each client are provided in the documentation linked in the client list above.
Users can choose the method that is right for them.

### Starting the consensus client {#starting-the-consensus-client}

The consensus client must be started with the right port configuration to establish a local RPC connection to the execution
client. The consensus clients all have a command similar to `--http-webprovider` that takes the exposed execution
client port as an argument.

The consensus client also needs the path to the execution client's `jwt-secret` in order to authenticate the RPC
connection between them. Each consensus client has a command similar to `--jwt-secret` that takes the file
path as an argument. This must be consistent with the `jwtsecret` path provided to the execution client.

**Note that we recommend waiting for merge-ready client releases before doing this on Ethereum Mainnet—for now just practice on a testnet such as Kiln**

### Adding Validators {#adding-validators}

Each of the consensus clients have their own validator software that is described in detail in their respective documentation. The easiest way to get started with
staking and validator key generation is to use the [Goerli Testnet Staking Launchpad](https://goerli.launchpad.ethereum.org/), allowing you to test your setup. When you're ready for Mainnet, you can repeat these steps using the [Mainnet Staking Launchpad](https://launchpad.ethereum.org/).

### Using the node {#using-the-node}

Execution clients offer RPC API endpoints that you can use to submit transactions, interact with or deploy smart contracts on the Ethereum network in various ways:

- Manually calling them with a suitable protocol (e.g. using `curl`)
- Attaching a provided console (e.g. `geth attach`)
- Implementing them in applications

Different clients have different implementations of the RPC endpoints. But there is a standard JSON-RPC which you can use with every client. For an overview [read the JSON-RPC docs](https://eth.wiki/json-rpc/API). Applications that need information from the Ethereum network can use this RPC. For example, popular wallet MetaMask lets you [run a local blockchain instance and connect to it](https://metamask.zendesk.com/hc/en-us/articles/360015290012-Using-a-Local-Node).

The consensus clients all expose a [Beacon API](https://ethereum.github.io/beacon-APIs) that can be used to check the status of the consensus client or download blocks and consensus data by sending requests using tools such as [Curl](https://curl.se). More information on this can be found in the documentation for each consensus client.

#### Reaching RPC {#reaching-rpc}

The default port for the execution client JSON-RPC is `8545` but you can modify the ports of local endpoints in the config file. By default, the RPC interface is only reachable on the localhost of your computer. To make it remotely accessible, you might want to expose it to the public by changing the address to `0.0.0.0`. This will make it reachable over local and public IP addresses. In most cases you'll also need to set up port forwarding on your router.

You should do this with caution as this will let anyone on the internet control your node. Malicious actors could access your node to bring down your system or steal your funds if you're using your client as a wallet.

A way around this is to prevent potentially harmful RPC methods from being modifiable. For example, with Geth, you can declare modifiable methods with a flag: `--http.api web3,eth,txpool`.

You can also host access to your RPC interface by pointing service of web server, like Nginx, to your client's local address and port.

The most privacy-preserving and simple way to set up a publicly reachable endpoint, you can host it on your own [Tor](https://www.torproject.org/) onion service. This will let you reach the RPC outside your local network without a static public IP address or opened ports.
To do this:

- Install `tor`
- Edit `torrc` config to enable hidden service with address of your client's RPC address and port
- Restart `tor` service

Once you restart Tor, you'll get hidden service keys and a hostname in your desired directory. From then, your RPC will be reachable on a `.onion` hostname.

### Operating the node {#operating-the-node}

You should regularly monitor your node to make sure it's running properly. You may need to do occasional maintenance.

#### Keeping node online {#keeping-node-online}

Your node doesn't have to be online nonstop but you should keep it online as much as possible to keep it in sync with the network. You can shut it down to restart it but keep in mind that:

- Shutting down can take up to a few minutes if the recent state is still being written on disk.
- Forced shut downs can damage the database.
- Your client will go out of sync with the network and will need to resync when you restart it.

_This doesn't apply on consensus layer validator nodes._ Taking your node offline will affect all services dependent on it. If you are running a node for _staking_ purposes you should try to minimize downtime as much as possible.

#### Creating client services {#creating-client-services}

Consider creating a service to run your clients automatically on startup. For example on Linux servers, good practice would be creating a service that executes the client with proper config, under user with limited privileges and automatically restarts.

#### Updating clients {#updating-clients}

You need to keep your client software up-to-date with the latest security patches, features, and [EIPs](/eips/). Especially before [hard forks](/history/), make sure you are running the correct client versions. Updating clients is very simple. Each client has specific instructions in their documentation but the process is generally just to stop the client, download the latest version and restart. The client should pick up where it left off but with the updates applied.

Each client implementation has a human-readable version string used in the peer-to-peer protocol but is also accessible from the command line. This version string lets users check they are running the correct version and allows block explorers and other analytical tools interested in quantifying the distribution of specific clients over the network. Please refer to the individual client documentation for more information about version strings.

#### Running additional services {#running-additional-services}

Running your own node lets you use services that require direct access to Ethereum client RPC. These are services built on top of Ethereum like [layer 2 solutions](/developers/docs/scaling/#layer-2-scaling) and other Ethereum infrastructure.

#### Monitoring the node {#monitoring-the-node}

"To properly monitor your node, consider collecting metrics. Clients provide metrics endpoints so you can get comprehensive data about your node. Use tools like [InfluxDB](https://www.influxdata.com/get-influxdb/) or [Prometheus](https://prometheus.io/) to create databases which you can turn into visualizations and charts in software like [Grafana](https://grafana.com/). There are many setups for using this software and different Grafana dashboards for you to visualise your node and the network as a whole.
As part of your monitoring, make sure to keep an eye on your machine's performance. During your node's initial sync, the client software may be very heavy on CPU and RAM. In addition to Grafana, you can use the tools your OS offers like `htop` or `uptime` to do this.

## Further reading {#further-reading}

- [Analyzing the hardware requirements to be an Ethereum full validated node](https://medium.com/coinmonks/analyzing-the-hardware-requirements-to-be-an-ethereum-full-validated-node-dc064f167902) _– Albert Palau, 24 September 2018_
- [Running Ethereum Full Nodes: A Guide for the Barely Motivated](https://medium.com/@JustinMLeroux/running-ethereum-full-nodes-a-guide-for-the-barely-motivated-a8a13e7a0d31) _– Justin Leroux, 7 November 2019_
- [Running an Ethereum Node](https://docs.ethhub.io/using-ethereum/running-an-ethereum-node/) _– ETHHub, updated often_
- [Running a Hyperledger Besu Node on the Ethereum Mainnet: Benefits, Requirements, and Setup](https://pegasys.tech/running-a-hyperledger-besu-node-on-the-ethereum-mainnet-benefits-requirements-and-setup/) _– Felipe Faraggi, 7 May 2020_
- [Deploying Nethermind Ethereum Client with Monitoring Stack](https://medium.com/nethermind-eth/deploying-nethermind-ethereum-client-with-monitoring-stack-55ce1622edbd) _– Nethermind.eth, 8 July 2020_

## Related topics {#related-topics}

- [Nodes and clients](/developers/docs/nodes-and-clients/)
- [Blocks](/developers/docs/blocks/)
- [Networks](/developers/docs/networks/)
