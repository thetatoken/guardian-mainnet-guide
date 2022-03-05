# Install Guardian Node from Source Code

The script was tested on the following device:
* Raspberry Pi OS 64 bits, Kernel Version 5.10, Debian version 11 (bullseye)
* Raspberry Pi 4 4GB RAM

## Install Go 1.17.8

Install Go and set environment variables `GOPATH` , `GOBIN`, and `PATH` following the commands below. The current code base should compile with **Go 1.17.8** on a ARM64 based architecture. Below are the steps to install Go 1.17.8 and setup the environments on Ubuntu.

```
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install gcc
sudo apt-get install make
sudo apt install libstdc++-9-dev
sudo wget https://go.dev/dl/go1.17.8.linux-arm64.tar.gz
sudo tar -C /usr/local -xzf go1.17.8.linux-arm64.tar.gz
echo 'export GOROOT=/usr/local/go' >> ~/.bashrc
echo 'export GOPATH=$HOME/go' >> ~/.bashrc
echo 'export PATH=$PATH:$GOROOT/bin:$GOPATH/bin' >> ~/.bashrc
echo 'export THETA_HOME=$GOPATH/src/github.com/thetatoken/theta' >> ~/.bashrc
source ~/.bashrc
```

## Checkout, fix dependences, and compile the Theta Guardian source code

Clone the `release` branch of the Theta Ledger repo https://github.com/thetatoken/theta-protocol-ledger into your `$GOPATH` with the following command. The path should look like this: `$GOPATH/src/github.com/thetatoken/theta`

```
git clone --branch release https://github.com/thetatoken/theta-protocol-ledger.git $GOPATH/src/github.com/thetatoken/theta
```

Then, we have to fix the package dependences for ARM64:
```
# Fix Go dependences
cd $THETA_HOME
sed 's/v0.0.0-20200107021104-147ed25f233e/v0.0.0-20220216073600-600054663ec1/' go.mod > aux_file
sed 's/github.com\/wedeploy\/gosocketio v0.0.7-beta/github.com\/cih-y2k\/wedeploy-gosocketio v0.0.8/' aux_file > aux_file2
rm go.mod
cp aux_file2 go.mod
rm aux_file aux_file2
go mod download github.com/cih-y2k/wedeploy-gosocketio
go get github.com/thetatoken/theta/crypto/bls

# Fix mul_arm64.h
rm ./crypto/bn256/cloudflare/mul_arm64.h
wget -O ./crypto/bn256/cloudflare/mul_arm64.h https://raw.githubusercontent.com/cloudflare/bn256/master/mul_arm64.h
```

Finally, we build the binaries:
```
cd $THETA_HOME
export GO111MODULE=on
make install
```

## Download necessary data for the Guardian Node

```
cd $THETA_HOME
mkdir -p ../guardian_mainnet/node
curl -k --output ../guardian_mainnet/node/snapshot `curl -k https://mainnet-data.thetatoken.org/snapshot`
curl -k --output ../guardian_mainnet/node/config.yaml `curl -k 'https://mainnet-data.thetatoken.org/config?is_guardian=true'`
```

## Launch the node and stake

Please continue with the instructions [here](./CLI.md#launch-the-guardian-node).


