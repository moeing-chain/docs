# Testnets for smartBCH

You can test your DApp using a [local single-node testnet](archives/runsinglenode.md) if you want to set one up. In most cases, though, you might not need to set up a testnet yourself. Instead, you can using an existing testnet. Here are the running testnets you can utilize.

You can run tests with [metamask](archives/test-using-metamask.md), [truffle](developers-guide/deploy-contract-using-truffle.md), or [remix](developers-guide/deploy-contract-using-remix.md).



### The Amber Testnet

This is a testnet for smartBCH with votes from a BCHN testnet, which has a bitcoincashnode client which mines with CPU.The chain ID is 0x2711. You can use the following JSON-RPC nodes:

1. http://35.220.203.194:8545
2. https://moeing.tech:9545

Test coin faucet can be found at http://13.214.162.63:8080/faucet or http://moeing.tech:8080/faucet

We sincerely thank [Allen Day](https://github.com/allenday) for donating several cloud servers to bring up this testnet. 

In this testnet, the gas price can be as low as 0.2Gwei.

To join this testnet as a non-validator node, follow the steps below:

First, build the latest binary by running the steps of [this document](developers-guide/build-smartbchd.md).

Second, clone & build smartbch:

```bash
cd ~/smart_bch
git clone -b v0.4.2 --depth 1 https://github.com/smartbch/smartbch
cd smartbch
go build -tags "params_amber cppbtree" github.com/smartbch/smartbch/cmd/smartbchd
```

Third, prepare the working directory:

```bash
cp ~/smart_bch/smartbch/smartbchd ~/build/smartbchd
cd ~
rm -rf .smartbchd
~/build/smartbchd init mynode --chain-id 0x2711
wget https://github.com/smartbch/artifacts/releases/download/v0.0.5/dot.smartbchd.tgz
tar zxvf dot.smartbchd.tgz
cp -rf dot.smartbchd/* .smartbchd/
```

Last, start smartbchd. 

```bash
~/build/smartbchd start --mainnet-genesis-height=602983
```

Then you can join the testnet as a non-validator. If want to become a validator, please follow  [this guide](testnets_validator.md).

If you want to join the underlying BCH testnet, here is the configuration file for bitcoincashnode:

```
regtest=1

[regtest]

bind=0.0.0.0
port=8331
rpcbind=0.0.0.0
rpcport=8332

zmqpubhashtx=tcp://0.0.0.0:8333
zmqpubhashblock=tcp://0.0.0.0:8333

rpcuser=test
rpcpassword=test
rpcthreads=4

rpcallowip=0.0.0.0/0

whitelistrelay=0

addnode=47.115.171.70:28331
```

## Docker

You can also run your smartBCH testnet node using Docker. First, clone smartBCH and build Docker image for testnet (or [pull prebuilt images from DockerHub](https://hub.docker.com/r/smartbch/smartbchd/tags)):

```bash
git clone https://github.com/smartbch/smartbch.git
cd smartbch
docker image build -f Dockerfile.optimized \
	--build-arg SMARTBCH_BUILD_TAGS='cppbtree,params_amber' \
	--build-arg SMARTBCH_VERSION=v0.4.2 \
	--build-arg CONFIG_VERSION=v0.0.5 \
	--build-arg CHAIN_ID=0x2711 \
	-t smartbchd-amber:latest .
```

Second, prepare smartBCH testnet home directory:

```bash
cd somewhere
mkdir smartbchd_home
docker run \
  -v path/to/smartbchd_home:/root/.smartbchd \
  smartbchd:latest init mynode --chain-id 0x2711

wget https://github.com/smartbch/artifacts/releases/download/v0.0.5/dot.smartbchd.tgz
tar xvf dot.smartbchd.tgz
cp -rfv dot.smartbchd/* smartbchd_home/
```

Last, start smartbchd using Docker like this:

```bash
docker run \
  -v path/to/smartbchd_home:/root/.smartbchd \
  -p 0.0.0.0:18545:8545 \
  -p 0.0.0.0:18546:8546 \
  --ulimit nofile=65535:65535 \
  -d smartbchd-amber:latest start --mainnet-genesis-height=602983
```
