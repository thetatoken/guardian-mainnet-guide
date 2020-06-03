# Running a Guardian Node through Command Line

## Install the guardian node

Please follow the instructions below to download the lastest **Linux** binary and the necessary data. If you prefer to compile from the source code, please follow the steps [here](./COMPILE.md#install-guardian-node-from-source-code). If you are running the node on a Linux server, you'd need to run the node in a **screen or tmux** session, so that after you've logged off, the Theta node can continue to run.

```
screen -S theta_mainnet # if you run the node on a Linux server
mkdir ~/theta_mainnet
cd ~/theta_mainnet
mkdir bin
mkdir -p guardian_mainnet/node
curl -k --output bin/theta `curl -k 'https://mainnet-data.thetatoken.org/binary?os=linux&name=theta'`
curl -k --output bin/thetacli `curl -k 'https://mainnet-data.thetatoken.org/binary?os=linux&name=thetacli'`
wget -O guardian_mainnet/node/snapshot `curl -k https://mainnet-data.thetatoken.org/snapshot`
curl -k --output guardian_mainnet/node/config.yaml `curl -k 'https://mainnet-data.thetatoken.org/config?is_guardian=true'`
chmod +x bin/theta
chmod +x bin/thetacli
cd bin/
```

The steps to install the binary on **macOS** and **Windows** are similar (on Windows you can use Cygwin, but make sure `curl` is installed). The only difference is that the `os` parameter for downloading the macOS and Windows binary are `macos` and `windows`, respectively.

## Launch the guardian node

Now launch the Theta with the following commands.

```
./theta start --config=../guardian_mainnet/node
```

**NOTE**: When the Theta node launches for the first time, you need to choose a password to encrypt the signing key of the guardian node. **Please choose a secure password and keep it in a safe place**. The next time when you restart the node, you will need the password to unlock it.

It might take some time for the node to sync up with the network (typically should be less than 10 minutes). To check if the node is already in-sync with the network, you can execute the following command in **another console**:

```
./thetacli query status
```

The `syncing` field in the return indicates whether the node is still in the synchronization mode. If it is `false`, it means the node is already synced to the lastest block.

## Stake to the guardian node

After the node is synced (i.e. `syncing` is `false`), we can proceed with staking to make it a full **Guardian Node**. First we'd need the "fingerprint" of this node. In another console, execute the following command

```
./thetacli query guardian
```

The output should look something like this:

```
{
    "Address": "0x8f3B...E819",
    "BlsPubkey": "a1225b...16ebe",
    "BlsPop": "b49fd2a...d025c",
    "Signature": "14deb5e...52500",
    "Summary": "0x8f3Bc...952500"
}
```

The `summary` part is the "fingerprint" we need for staking. Next, [please follow the steps here](./GUI.md#stake-to-the-guardian-node) to stake some mainnet Theta tokens from the Theta Wallet. Each guardian staking transaction requires at least 10,000 Theta tokens.

If everything works out, this node will start to send out guardian votes for the checkpoint blocks (i.e. the blocks whose `height%100 == 1`). In a few minutes we should start to see guardian votes in the log.

```
[2019-10-10 10:01:46] DEBUG [guardian] Boardcasting guardian vote vote=AggregatedVotes{Block: 0xab2a360ed292a14bb675837d1b1fe26de87414bdd8b9838d8d3646ca2c004844, Gcp: 0xd717f1d2bbdf0d8314341afcb30dc0d7d5419914c59754158cfa7a830c9d4c74,  Multiplies: [1, 3, 0, ...., 1, 2]}
```

Cheers! Now you should expect to receive your first TFUEL reward at the next checkpoint block!!!

