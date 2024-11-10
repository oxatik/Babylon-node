# Babylon-node
Babylon is a trustless Bitcoin staking protocol that allows Bitcoin holders to stake their coins for Proof of Stake (PoS) blockchains without needing third-party custody
# Bitcoin Staking Protocol: Complete Guide to Setting Up  Babylon Node
![image](https://github.com/user-attachments/assets/e48b8a15-c928-4909-a5e7-96bfb66a7a9b)


# Babylon: An Overview
## Revolutionizing Blockchain: Babylon’s Innovative Bitcoin Staking for Enhanced PoS Security and Dapp Utility”
Babylon is a trustless Bitcoin staking protocol that allows Bitcoin holders to stake their coins for Proof of Stake (PoS) blockchains without needing third-party custody or bridges.
It provides slashable economic security guarantees to PoS chains while ensuring efficient stake unbonding to enhance liquidity for Bitcoin holders.The protocol is designed as a modular plugin compatible with various PoS consensus protocols,It aims to serve as a foundational component for building restaking protocols.

![image](https://github.com/user-attachments/assets/f4ebbe5b-c5f1-4d66-b7d3-31abed544b37)


# Core Concepts
The protocol uses extractable one-time signatures (EOTS) to enable slashing in response to safety violations.It introduces an additional "finality round" after the base consensus protocol. A block is considered finalized only if it receives EOTS signatures from over 2/3 of the Bitcoin stake,Safety violations can be reduced to double signing in this finality round, allowing for slashing of staked Bitcoin.


# Installation
# Prerequisites for Deploying a Babylon Node
Recommended:

System: Linux Ubuntu 22.04 with Docker installed

CPU: 4 cores

RAM: 32 GB

Storage: 1 TB NVMe

For my setup, I selected Contabo, a well-regarded provider of VPS rentals. Opting for a plan with at least 100 GB of storage is advisable to ensure sufficient space for long-term node operation.My suggestion would be to go for the Cloud VPS M option. https://contabo.com/en/

Although the current hardware requirements are high, I recommend opting for a Contabo VPS 2 server for now. However, as the network expands, it may be wise to consider upgrading to a Cloud VPS 3 or even 4 in the long term.

# Connect to the server:
  You’ll need to download and use Putty, a tool that enables you to securely connect to your VPS and utilize its functionalities. Download it here https://www.putty.org/

 ➢ Open PuTTY and paste the IP address into the "Host Name" field

➢ Click "Open" and accept an Alert

➢ Type "root" and hit Enter

➢ Paste your password and hit Enter

![image](https://github.com/user-attachments/assets/90afd7df-c5ad-4d74-a648-2f90ea5bace2)![image](https://github.com/user-attachments/assets/f462e192-11c6-430a-8322-79cb9b463a0c)![image](https://github.com/user-attachments/assets/ef60bb77-96d7-40c5-a730-7fca99433695)
![image](https://github.com/user-attachments/assets/2fa9a2f0-2e17-4fbe-a4a7-61ef5f86f2a3)




# Preparation
# Install essential components

```bash
sudo apt update && sudo apt upgrade -y 
```
![image](https://github.com/user-attachments/assets/ecf0d6a7-390f-4950-b76b-8892d01357cd)

 Set Validator Name
```bash
MONIKER="YOUR_MONIKER_GOES_HERE"  
```
Replace YOUR_MONIKER_IS_HERE with your node name:

example:
MONIKER="babylon Node"

# Install Build Tools
```bash
sudo apt -qy install curl git jq lz4 build-essential  
```
![image](https://github.com/user-attachments/assets/0fa1dbc8-b699-43ac-872c-c0d41de8731a)

# Install Go
```bash
sudo rm -rf /usr/local/go
```
```bash
 curl -Ls https://go.dev/dl/go1.20.12.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local 
```
```bash
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)  
```
```bash
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)  
```
![image](https://github.com/user-attachments/assets/5ff5f49c-4481-47a8-a30c-55efe364f9b7)

# Download and build binaries
### Clone project repository
```bash
cd $HOME  
```
```bash
rm -rf babylon  
```
```bash
git clone https://github.com/babylonchain/babylon.git  
```
```bash
cd babylon  
```
```bash
git checkout v0.7.2  
```
# Build binaries
```bash
make build  
```
 Prepare binaries for Cosmovisor

```bash
 mkdir -p $HOME/.babylond/cosmovisor/genesis/bin 
```
```bash
 mv build/babylond $HOME/.babylond/cosmovisor/genesis/bin/ 
```
```bash
rm -rf build  
```
# Create application symlinks
```bash
sudo ln -s $HOME/.babylond/cosmovisor/genesis $HOME/.babylond/cosmovisor/current -f  
```
```bash
 sudo ln -s $HOME/.babylond/cosmovisor/current/bin/babylond /usr/local/bin/babylond -f 
```
![image](https://github.com/user-attachments/assets/0ee1e1de-0fdf-4220-bdad-65f124967683)

# Set up Cosmovisor
# Download and install Cosmovisor
```bash
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest  
```
# Create and start service
```bash
sudo tee /etc/systemd/system/babylon.service > /dev/null << EOF

[Unit]

Description=babylon node service

After=network-online.target

[Service]

User=$USER

ExecStart=$(which cosmovisor) run start

Restart=on-failure

RestartSec=10

LimitNOFILE=65535

Environment="DAEMON_HOME=$HOME/.babylond"

Environment="DAEMON_NAME=babylond"

Environment="UNSAFE_SKIP_BACKUP=true"

Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.babylond/cosmovisor/current/bin"

[Install]

WantedBy=multi-user.target

EOF  
```
```bash
 sudo systemctl daemon-reload 
```
```bash
sudo systemctl enable babylon.service  
```
![image](https://github.com/user-attachments/assets/75e97308-42c3-4055-8c57-583e10c746a6)

# Initialize the node
# Set node configuration
```bash
babylond config chain-id bbn-test-2  
```
```bash
babylond config keyring-backend test  
```
```bash
babylond config node tcp://localhost:16457  
```
# Initialize the node
```bash
 babylond init $MONIKER --chain-id bbn-test-2 
```
# Download genesis and addrbook
```bash
 curl -Ls https://snapshots.kjnodes.com/babylon-testnet/genesis.json > $HOME/.babylond/config/genesis.json 
```
```bash
curl -Ls https://snapshots.kjnodes.com/babylon-testnet/addrbook.json > $HOME/.babylond/config/addrbook.json  
```
# Add seeds
```bash
sed -i -e "s|^seeds *=.*|seeds = \"3f472746f46493309650e5a033076689996c8881@babylon-testnet.rpc.kjnodes.com:16459\"|" $HOME/.babylond/config/config.toml  
```
# Set minimum gas price
```bash
 sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.00001ubbn\"|" $HOME/.babylond/config/app.toml 
```
# Set pruning
```bash
sed -i \  
```
```bash
-e 's|^pruning *=.*|pruning = "custom"|' \  
```
-e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \```bash
  
```
```bash
-e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \  
```
```bash
 -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \ 
```
```bash
 $HOME/.babylond/config/app.toml 
```
# Set custom ports
```bash
 sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:16458\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:16457\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:16460\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:16456\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":16466\"%" $HOME/.babylond/config/config.toml 
```
```bash
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:16417\"%; s%^address = \":8080\"%address = \":16480\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:16490\"%; s%^address = \"localhost:9091\"%address = \"0.0.0.0:16491\"%; s%:8545%:16445%; s%:8546%:16446%; s%:6065%:16465%" $HOME/.babylond/config/app.toml  
```
![image](https://github.com/user-attachments/assets/aaffcc63-6460-4368-b804-14d48abf8df1)

# Download latest chain snapshot
```bash
curl -L https://snapshots.kjnodes.com/babylon-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.babylond [[ -f $HOME/.babylond/data/upgrade-info.json ]] && cp $HOME/.babylond/data/upgrade-info.json $HOME/.babylond/cosmovisor/genesis/upgrade-info.json  
```
![image](https://github.com/user-attachments/assets/3aa0151c-1b0f-4d85-8dfb-5e8921a7e81a)

# Start service and check the logs
```bash
 sudo systemctl start babylon.service && sudo journalctl -u babylon.service -f --no-hostname -o cat 
```
![image](https://github.com/user-attachments/assets/b0a39f6f-385a-4cbe-86cf-48933d6946b8)

# Becoming a Validator Create a Keyring and Get Funds
Validators are required to have funds for two reasons:

*They need to provide a self delegation
*They need to pay for transaction fees for submitting BLS signature transactions

Currently, validators can only use the test keyring backend. In the future, Babylon will support other types of encrypted backends provided by the Cosmos SDK for validators.
# ADD NEW KEY
```bash
babylond keys add wallet  
```
![image](https://github.com/user-attachments/assets/5779be00-2439-4be7-9894-94ea0ce558ff)
# Optional: RECOVER EXISTING KEY
```bash
babylond keys add wallet --recover  
```
# REQUEST TESTNET FUNDS
To request funds, visit the #faucet channel on the official Babylon Discord server
https://discord.com/invite/babylonglobal Once in the channel, submit your request by typing !faucet followed by your address. For instance, you would type this to request funds for that specific address:

!faucet bbn1ghe5wh00ayug79cxrvpyt7n2vh46pjsuw8spug

You can check your wallet balance using this command:
```bash
babylond q bank balances $(babylond keys show wallet -a)  
```
# Create a BLS key
Validators are expected to submit a BLS signature at the end of each epoch. To do that, a validator needs to have a BLS key pair to sign information with.
```bash
babylond create-bls-key $(babylond keys show wallet -a)  
```
This command will create a BLS key and add it to the $HOME/.babylond/config/priv_validator_key.json. This is the same file that stores the private key that the validator uses to sign blocks. Please ensure that this file is secured properly.

After creating a BLS key, you need to restart your node to load this key into memory.
```bash
sudo systemctl restart babylon.service  
```
# Modify the Configuration Settings
It’s required to identify the key name your validator will utilize for BLS signature transactions in the app.toml file found at $HOME/.babylond/config/. You should adjust this file to include the key name that corresponds to the one with funds in your keyring.
```bash
sed -i -e "s|^key-name *=.*|key-name = \"wallet\"|" $HOME/.babylond/config/app.toml  
```
Lastly, it’s important to change the timeout_commit setting in the config.toml file at $HOME/.babylond/config/. This setting controls how long a validator waits to commit a block and then start on the next one. Since Babylon wants a 10-second gap between blocks, make sure to set this value to match that goal.
```bash
sed -i -e "s|^timeout_commit *=.*|timeout_commit = \"10s\"|" $HOME/.babylond/config/config.toml  
```
# Create the Validator
Setting up a validator on Babylon is different from typical Cosmos SDK chains. You need to use the command babylond tx checkpointing create-validator. This method needs a BLS validator key, which should be in the file $HOME/.babylond/config/priv_validator_key.json.

Babylon validators are required to submit a BLS signature transaction every epoch (with current parameters every ~30mins). Those transactions currently cost a static gas fee of 100ubbn. Therefore, it is important that validators maintain enough unbonded funds in their keyring to pay for those transaction fees.
# Check the network synchronization status with the command:
```bash
babylond status | jq .SyncInfo  
```
![image](https://github.com/user-attachments/assets/bb458236-cc58-4cac-8017-b6d9b89a41da)

As soon as the value of catching_up becomes “false”, you can proceed to the final step.

# Create the Validator
```bash
# Make sure you have adjusted **moniker**, **details** and **website** to match your values.

babylond tx checkpointing create-validator \
--amount 1000000ubbn \
--pubkey $(babylond tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id bbn-test-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.00001ubbn \
-y  
```
Note: In order to become an active validator, you need to have more ubbn tokens bonded than the last validator ordered by the tokens bonded (or the validator set to not be full) as well as have at least 10000000ubbn bonded.

Thus, it’s necessary to acquire a significant amount of testnet tokens from the faucet. After obtaining these tokens, you should delegate them to your node using the following command:
```bash
babylond tx epoching delegate $(babylond keys show wallet --bech val -a) 1000000ubbn --from wallet --chain-id bbn-test-2 --gas-adjustment 1.4 --gas auto --fees 10ubbn -y  
```
# To see how many tokens delegated to your Validator, use this command:
```bash
babylond q staking validator $(babylond keys show wallet --bech val -a)  
```
*1,000,000 ubbn = 1 $BBN

Congrats! You are now a validator on the Babylon system. In about half an hour, you should be able to see your validator on the list at https://babylon.explorers.guru/validators. You can locate it by looking up your wallet address or your Moniker. Once found, you’ll also be able to view any delegations made to your validator under this wallet. Note that your node will be labeled as “inactive” at first when it shows up on the list.

![image](https://github.com/user-attachments/assets/9bfafb82-a09f-4d7f-88e0-9142ae4b00de)
# went to learn more
https://babylonlabs.io/

https://docs.babylonchain.io/docs/introduction/babylon-overview

https://discord.com/invite/babylonglobal

https://x.com/babylonlabs_io



