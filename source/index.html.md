---
title: API Reference

# language_tabs: # must be one of https://git.io/vQNgJ
#   - shell
#   - javascript
#   - rust

toc_footers:
  - <a href='https://solana.com'>Solana is scalable blockchain</a>

<!-- includes:
  - errors -->

search: true
---

# Introduction

Welcome to Solana knowledge base.

## What is Solana?

Solana is the name of an open source project that is implementing a new high-performance, permissionless blockchain. Solana is also the name of a company headquartered in San Francisco that maintains the open source project.

## About this book

This book defines the architecture of Solana, a blockchain built from the ground up for scale. The goal of the architecture is to demonstrate there exists a set of software algorithms that in combination, removes software as a performance bottleneck, allowing transaction throughput to scale proportionally with network bandwidth. The architecture goes on to satisfy all three desirable properties of a proper blockchain: that it not only be scalable, but that it is also secure and decentralized.

The architecture describes a theoretical upper bound of 710 thousand transactions per second (tps) on a standard gigabit network and 28.4 million tps on 40 gigabit. Furthermore, the architecture supports safe, concurrent execution of programs authored in general purpose programming languages such as C or Rust.

## History of the Solana codebase

In November of 2017 Anatoly Yakovenko published a whitepaper describing Proof of History, a technique for keeping time between computers that do not trust one another. From Anatoly's previous experience designing distributed systems at Qualcomm, Mesosphere and Dropbox, he knew that a reliable clock makes network synchronization very simple. When synchronization is simple the resulting network can be blazing fast, bound only by network bandwidth.

Anatoly watched as blockchain systems without clocks, such as Bitcoin and Ethereum, struggled to scale beyond 15 transactions per second worldwide when centralized payment systems such as Visa required peaks of 65,000. Without a clock, it was clear they'd never graduate to being the global payment system or global supercomputer they had dreamed to be. When Anatoly solved the problem of getting computers that don’t trust each other to agree on time, he knew he had the key to bring 40 years of distributed systems research to the world of blockchain. The resulting cluster wouldn't be just 10 times faster, or a 100 times, or a 1,000 times, but 10,000 times faster right out of the gate!

Anatoly's implementation began in a private codebase and was implemented in the C programming language. Greg Fitzgerald, who had previously worked with Anatoly at semiconductor giant Qualcomm Incorporated, encouraged him to reimplement the project in the Rust programming language. Greg had worked on the LLVM compiler infrastructure, which underlies both the Clang C/C++ compiler as well as the Rust compiler. Greg claimed that the language's safety guarantees would improve software productivity and that its lack of a garbage collector would allow programs to perform as well as those written in C. Anatoly gave it a shot and just two weeks later, had migrated his entire codebase to Rust. Sold. With plans to weave all the world's transactions together on a single, scalable blockchain, Anatoly called the project Loom.

On February 13th of 2018, Greg began prototyping the first open source implementation of Anatoly's whitepaper. The project was published to GitHub under the name Silk in the loomprotocol organization. On February 28th, Greg made his first release, demonstrating 10 thousand signed transactions could be verified and processed in just over half a second. Shortly after, another former Qualcomm cohort, Stephen Akridge, demonstrated throughput could be massively improved by offloading signature verification to graphics processors. Anatoly recruited Greg, Stephen and three others to co-found a company, then called Loom.

Around the same time, Ethereum-based project Loom Network sprung up and many people were confused if they were the same project. The Loom team decided it would rebrand. They chose the name Solana, a nod to a small beach town North of San Diego called Solana Beach, where Anatoly, Greg and Stephen lived and surfed for three years when they worked for Qualcomm. On March 28th, the team created the Solana Labs GitHub organization and renamed Greg's prototype Silk to Solana.

In June of 2018, the team scaled up the technology to run on cloud-based networks and on July 19th, published a 50-node, permissioned, public testnet consistently supporting bursts of 250,000 transactions per second. In the most recent release, v0.10 Pillbox, the team published a permissioned testnet running 150 nodes on a gigabit network and demonstrated soak tests processing an average of 200 thousand transactions per second with bursts over 500 thousand. The project was also extended to support on-chain programs written in the C programming language and run concurrently in a safe execution environment called BPF. Next step: going permissionless.

## What is a Solana cluster and why would one use it?

A cluster is a set of computers that work together and can be viewed from the outside as a single system. A Solana cluster is a set of independently owned computers working together (and sometimes against each other) to verify the output of untrusted, user-submitted programs. A Solana cluster can be utilized any time a user wants to preserve an immutable record of events in time or programmatic interpretations of those events. One use is to track which of the computers did meaningful work to keep the cluster running. Another use might be to track the possession of real-world assets. In each case, the cluster produces a record of events called the ledger. It will be preserved for the lifetime of the cluster. As long as someone somewhere in the world maintains a copy of the ledger, the output of its programs (which may contain a record of who possesses what) will forever be reproducible, independent of the organization that launched it.

## What are sols?

A sol is the name of Solana's native token, which can be passed to nodes in a solana cluster in exchange for running an on-chain program or validating its output. The Solana protocol defines that only 1 billion sols will ever exist, but that the system may perform micropayments of fractional sols and that a sol may be split as many as 34 times. The fractional sol is called a lamport in honor of Solana's biggest technical influence, [Leslie Lamport](https://en.wikipedia.org/wiki/Leslie_Lamport). A lamport has a value of approximately 0.0000000000582 sol (2^-34).

## Disclaimer

All claims, content, designs, algorithms, estimates, roadmaps, specifications, and performance measurements described in this project are done with the author's best effort. It is up to the reader to check and validate their accuracy and truthfulness. Furthermore, nothing in this project constitutes a solicitation for investment.

# Terminology

## Terminology currently in use

The following list contains words commonly used throughout the Solana architecture.

* account - a persistent file addressed by pubkey and with tokens tracking its lifetime
* cluster - a set of fullnodes maintaining a single ledger
* finality - the wallclock duration between a leader creating a tick entry and recoginizing a supermajority of validator votes with a ledger interpretation that matches the leader's
* fullnode - a full participant in the cluster - either a leader or validator node
* entry - an entry on the ledger - either a tick or a transactions entry
* instruction - the smallest unit of a program that a client can include in a transaction
* keypair - a public and secret key
* node count - the number of fullnodes participating in a cluster
* program - the code that interprets instructions
* pubkey - the public key of a keypair
* tick - a ledger entry that estimates wallclock duration
* tick height - the Nth tick in the ledger
* tps - transactions per second
* transaction - one or more instructions signed by the client and executed atomically
* transactions entry - a set of transactions that may be executed in parallel

## Terminology reserved for future use

The following keywords do not have any functionality but are reserved by Solana for potential future use.

* epoch - the time in which a leader schedule is valid
* mips - millions of instructions per second
* public key - We currently use pubkey
* slot - the time in which a single leader may produce entries
* secret key - Users currently only use keypair


# Getting started

The Solana git repository contains all the scripts you might need to spin up your own local testnet. Depending on what you're looking to achieve, you may want to run a different variation, as the full-fledged, performance-enhanced multinode testnet is considerably more complex to set up than a Rust-only, singlenode testnode. If you are looking to develop high-level features, such as experimenting with smart contracts, save yourself some setup headaches and stick to the Rust-only singlenode demo. If you're doing performance optimization of the transaction pipeline, consider the enhanced singlenode demo. If you're doing consensus work, you'll need at least a Rust-only multinode demo. If you want to reproduce our TPS metrics, run the enhanced multinode demo.

For all four variations, you'd need the latest Rust toolchain and the Solana source code.

> Install Rust's package manager Cargo.

```shell
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
```

> Now checkout the code from github.

```shell
git clone https://github.com/solana-labs/solana.git
cd solana
```

The demo code is sometimes broken between releases as we add new low-level features, so if this is your first time running the demo, you'll improve your odds of success if you check out the [latest release](https://github.com/solana-labs/solana/releases) before proceeding.

```shell
TAG=$(git describe --tags $(git rev-list --tags --max-count=1))
git checkout $TAG
```

## Configuration setup

The network is initialized with a genesis ledger and leader/validator configuration files. These files can be generated by running a script.

> Setup ledger and leader/validator configuration files.

```shell
./multinode-demo/setup.sh
```


## Drone

In order for the leader, client and validators to work, we'll need to spin up a drone to give out some test tokens. The drone delivers Milton Friedman-style "air drops" (free tokens to requesting clients) to be used in test transactions.

> Start the drone on the leader node.

```shell
./multinode-demo/drone.sh
```


## Singlenode Testnet

Before you start a fullnode, make sure you know the IP address of the machine you want to be the leader for the demo, and make sure that udp ports 8000-10000 are open on all the machines you want to test with.

> Start the server in a separate shell.

```shell
./multinode-demo/leader.sh
```

Wait a few seconds for the server to initialize. It will print "leader ready..." when it's ready to receive transactions. The leader will request some tokens from the drone if it doesn't have any. The drone does not need to be running for subsequent leader starts.


## Multinode Testnet

To run a multinode testnet, after starting a leader node, spin up some validator nodes in separate shells.

> Start the server in a separate shell.

```shell
./multinode-demo/validator.sh
```

To run a performance-enhanced leader or validator (on Linux), [CUDA 10.0](https://developer.nvidia.com/cuda-downloads) must be installed on your system.

> Install CUDA environment.

```shell
./fetch-perf-libs.sh
SOLANA_CUDA=1 ./multinode-demo/leader.sh
SOLANA_CUDA=1 ./multinode-demo/validator.sh
```


## Testnet Client Demo

Now that your singlenode or multinode testnet is up and running let's send it some transactions! In a separate shell start the client.

> Start the client.

```shell
./multinode-demo/client.sh # runs against localhost by default
```

What just happened? The client demo spins up several threads to send 500,000 transactions to the testnet as quickly as it can. The client then pings the testnet periodically to see how many transactions it processed in that time. Take note that the demo intentionally floods the network with UDP packets, such that the network will almost certainly drop a bunch of them. This ensures the testnet has an opportunity to reach 710k TPS. The client demo completes after it has convinced itself the testnet won't process any additional transactions. You should see several TPS measurements printed to the screen. In the multinode variation, you'll see TPS measurements for each validator node as well.

## Public Testnet

In this example the client connects to our public testnet. To run validators on the testnet you would need to open udp ports 8000-10000.

> Run validators.

```shell
./multinode-demo/client.sh --network $(dig +short testnet.solana.com):8001 --identity config-private/client-id.json --duration 60
```

You can observe the effects of your client's transactions on our [dashboard](https://metrics.solana.com:3000/d/testnet/testnet-hud?orgId=2&from=now-30m&to=now&refresh=5s&var-testnet=testnet).


## Linux Snap

A Linux [Snap](https://snapcraft.io/) is available, which can be used to easily get Solana running on supported Linux systems without building anything from source for evaluation. Note that CUDA is not supported by the Snap so performance will be limited.

The ```edge``` Snap channel is updated daily with the latest development from the ```master``` branch.

> Install edge Snap channel.

```shell
sudo snap install solana --edge --devmode
```

Once installed the usual Solana programs will be available as ```solona.*``` instead of ```solana-*```. For example, ```solana.fullnode``` instead of ```solana-fullnode```.

> Update to the latest version.

```shell
snap info solana
sudo snap refresh solana --devmode
```

## Daemon support
The snap supports running a leader, validator or leader+drone node as a system daemon.

> View the current daemon configuration:

```shell
sudo snap get solana
```

> Runtime configuration files for the daemon can be found in ```/var/snap/solana/current/config```.

> View the daemon initialization log:

```shell
sudo snap logs -n=all solana
```

> Depend on which ```mode``` was selected runtime logs can be found in next locations: 

```shell
/var/snap/solana/current/leader/
/var/snap/solana/current/validator/ 
/var/snap/solana/current/drone/ 
```
> Within each log directory the file ```current``` contains the latest log, and the files ```*.s``` (if present) contain older rotated logs.


> Disable the daemon at any time by running:

```shell
sudo snap set solana mode=
```

## Leader daemon

> Run leader daemon.

``` 
sudo snap set solana mode=leader
```

```rsync``` must be configured and running on the leader.

> Ensure ```rsync``` is installed.

```shell
sudo apt-get -y install rsync
```

> Edit ```/etc/rsyncd.conf``` to include the following:

```shell
[config]
path = /var/snap/solana/current/config
hosts allow = *
read only = true
```

> Run systemd service:
 
```shell
sudo systemctl enable rsync; sudo systemctl start rsync
```

> Test by running next command from another machine: 

```shell
rsync -Pzravv rsync://<ip-address-of-leader>/config solana-config```
```

> To run both the Leader and Drone:

```shell
sudo snap set solana mode=leader+drone
```

<aside class="notice">
If the leader is running on a cloud provider it may be necessary to configure the Firewall rules to permit ingress to port <code>tcp:873</code>, <code>tcp:9900</code> and the port range <code>udp:8000-udp:10000</code>.
</aside>

## Validator daemon
> Run validator daemon.

``` 
sudo snap set solana mode=validator
```

By default the validator will connect to ```testnet.solana.com```.

> To override the leader IP address do next (replace ip address with yours):

```
sudo snap set solana mode=validator leader-address=127.0.0.1
```

<aside class="notice">
It's assumed that the leader will be running rsync configured as described in the previous Leader daemon section.
</aside>
