# Run you LND with btcd simnet

Simnet is a development/test network run locally that allows us to generate blocks at will, so we can avoid the time-consuming process of waiting for blocks to arrive for any on-chain functionality.

## Install GO and LND

In order to work with [`lnd`](https://github.com/lightningnetwork/lnd), the following build dependencies are required:

- **Go:** `lnd` and `btcd` are written in Go. To install, run one of the following commands:

  ```
  sudo apt-get install golang-1.11-go
  ```

  And adding the environment variables to your user's .profile::

  ```
  export PATH=$PATH:/usr/local/go/bin
  export PATH=$PATH:$(go env GOPATH)/bin
  export GOPATH=$(go env GOPATH)
  ```

- [`btcd`](https://github.com/btcsuite/btcd)
  Run the following commands to obtain btcd, all dependencies, and install it:

  ```bash
  go get -u github.com/Masterminds/glide
  git clone https://github.com/btcsuite/btcd $GOPATH/src/github.com/btcsuite/btcd
  cd $GOPATH/src/github.com/btcsuite/btcd
  glide install
  go install . ./cmd/...
  ```

- [`lnd`](https://github.com/lightningnetwork/lnd)
  Run the following commands to obtain and install lnd:

  ```
  go get -d github.com/lightningnetwork/lnd
  cd $GOPATH/src/github.com/lightningnetwork/lnd
  make && make install
  ```

Once you have `lnd` and `btcd` you can continue:

## Running simnet

```
btcd --simnet --txindex --rpcuser=kek --rpcpass=kek
```

## Run LND

```bash
lnd --restlisten=localhost:8001 --datadir=~/.lnd/data --logdir=~/.lnd/log --debuglevel=info --bitcoin.simnet --bitcoin.active --bitcoin.node=btcd --btcd.rpcuser=kek --btcd.rpcpass=kek
```

## Test if it works:

```bash
lncli --macaroonpath=~/.lnd/data/chain/bitcoin/simnet/admin.macaroon getinfo
```

## Use the REST API

```bash
curl --insecure --header "Grpc-Metadata-macaroon: $(xxd -ps -u -c 1000  ~/.lnd/data/chain/bitcoin/simnet/admin.macaroon)" https://localhost:8001/v1/getinfo
```

## How to get founds in your lnd wallet

- Get an address from your lnd wallet

```
lncli --macaroonpath=~/.lnd/data/chain/bitcoin/simnet/admin.macaroon newaddress np2wkh
```

It gives you an output like: `rd6y7jJVPHGUBxbGjXer8W4ycQgVNbFHPb`

## Assign the new created address as your mining address in btcd

```
btcd --simnet --txindex --rpcuser=kek --rpcpass=kek --miningaddr=NEW_ADDRESS
```

## Generate bloks using btcctl cli

```
btcctl --simnet --rpcuser=kek --rpcpass=kek generate 100
```

## Check your balance

Now you should have founds in your lnd wallet, test it with the command bellow:

```
lncli --macaroonpath=~/.lnd/data/chain/bitcoin/simnet/admin.macaroon walletbalance
```

It should output:

```
{
  "total_balance": "5000000000",
  "confirmed_balance": "5000000000",
  "unconfirmed_balance": "0"
}
```
