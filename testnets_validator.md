# Join the Amber as a validator

This document introduces how to join the amber as a validator node.

Before you start, you must have a trusted server running bitcoincashnode's client with RPC enabled. The executable `smartbchd` will connect to it for querying staking information.

#### 1. build the binary

Running the steps of [this document](developers-guide/build-smartbchd.md).

Then clone the source code of smartBCH and build the executable of `smartbchd`.

```bash
cd ~/smart_bch
git clone -b v0.4.2 --depth 1 https://github.com/smartbch/smartbch
cd smartbch
go build -tags "params_amber cppbtree" github.com/smartbch/smartbch/cmd/smartbchd
```

After successfully executing the above commands, you'll get a ~/smart\_bch/smartbch/smartbchd file for amber.



#### 2. prepare the working directory

```bash
cp ~/smart_bch/smartbch/smartbchd ~/build/smartbchd
cd ~
rm -rf .smartbchd
~/build/smartbchd init mynode --chain-id 0x2711
wget https://github.com/smartbch/artifacts/releases/download/v0.0.5/dot.smartbchd.tgz
tar zxvf dot.smartbchd.tgz
cp -rf dot.smartbchd/* .smartbchd/
```



#### 3. Prepare the validator keys

Running step 1 and step 2 of [this doc](./archives/runmultinode.md#1-on-the-nodes-which-do-not-generate-the-genesis-file) to generate validator key and consensus pubkey.

Then, copy the priv_validator_key.json to work directory by running command below:

```
cp ./priv_validator_key.json ~/.smartbchd/config/
```



#### 4. Modify App config

Open the `~/.smartbchd/config/app.toml` file to modify the information of the bitcoincashnode's client with RPC enabled.

```
# BCH mainnet rpc url
mainnet-rpc-url = "http://ip-address:8332"

# BCH mainnet rpc username
mainnet-rpc-username = "<my user name>"

# BCH mainnet rpc password
mainnet-rpc-password = "<my password>"
```



#### 5. start smartbchd 

```bash
cd ~/build
~/build/smartbchd start --mainnet-genesis-height=602983
```



#### 6. Build createValidator Tx

Using validator private key and consensus pubkey generated in step 3 to build command below:

```
./smartbchd staking \
--validator-key=<your validator private key> \
--staking-coin=1 \
--consensus-pubkey=<your consensus pub key> \
--introduction=<your validator introduction> \
--type=create \
--nonce=0 \
--gas-price=10000000000 \
--chain-id=0x2711	
```

This command generate createValidator tx raw data, send it in step 8 below.



#### 7. Get some BCH to Send Tx

Get some BCH from some one has BCH in smartBCH already to your validator account. To check your account balance like this.

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["<your validator account address>","latest"],"id":1}' -H "Content-Type: application/json" http://127.0.0.1:8545
```



#### 8. Send Tx to smartBCH Amber

Please follow step 1, 2 of [this doc](./archives/runmultinode.md#step-1-send-transaction-to-register-as-a-validator).



#### 9. Vote your consensus pubkey in Amber Xhedge Contract

Please go to https://amber.xhedge.cash/ to split your BCH to LeverNFT and HedgeNFT, and vote for your validator just created.
