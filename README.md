# btx-mn-docker
Docker Compose to running BitCore Masternode (Version 0.90.9.0)

## Some important notes:

* Always do a backup of your wallet.dat file

* Encrypt your wallet

## What you need to run a masternode:

* Linux VPS (Ubuntu 18.04) from your favorite provider (e.g. Vultr, Scaleway, OVH, ...)

* IPv4 address from your Linux VPS

* 2,100 BitCore BTX coins for a masternode collateral

* BitCore BTX QT Wallet (min. Version 0.90.9.0)

## Wallet Setup

1. Download BitCore BTX QT Wallet from our GitHub

2. Start your BitCore BTX QT Wallet and wait until it's fully synchronized. The bitcore.conf will created by QT Wallet.

3. OPTIONAL: Encrypt your wallet (best with a strong password)

4. Create a new wallet address for your masternode collateral (save it in a text file, you need it later)
    Debug Console command:
    
    ```sh
    getnewaddress "mn01" "legacy"
    <BTX_WALLET_ADDRESS>
    ```

5. Send 2100 BTX Coins to your new generated wallet address and wait for 15 confirmations

6. Generate a private key for your masternode (save it in a text file, you need it later)
    Debug Console command:
    
    ```sh
    masternode genkey
    <MASTERNODE_PRIVKEY>
    ```

7. Get your masternode collateral output (save it in a text file, you need it later)
    Debug Console command:
    
    ```sh
    masternode outputs
    <TX_ID> <TX_INDEX>
    ```
    
8. Open the masternode.conf file (In wallet: Tools -> Open Masternode Configuration File) and enter all needed information, e.g.

    ```sh
    <MN_ALIAS> <VPS_IP>:8555 <MASTERNODE_PRIVKEY> <TX_ID> <TX_INDEX>
    ```
    For example:
    ```sh
    mn01 1.2.3.4:8555 5Jgo9G7vNJxzmZdvJR5uiifHx3RGzmTF9SBiAKzzbTPpfToNuQw 23e029a26068fc77aa1000a003e0b4ef8273a09fd79b7646d0da87e44fdbb1db 1
    ```

9. Activate Masternode Tab
    Goto Settings -> Options -> Wallet and set Show Masternode Tab
    Restart your Wallet, then you should see the Masternodes Tab in your Wallet.


## VPS Setup

1. Connect to your VPS via SSH (I prefer PuTTY)

2. Clone the GitHub

    ```sh
    git clone https://github.com/dalijolijo/btx-mn-docker.git 
    ```

3. OPTIONAL for Ubuntu 16.04 / 18.04: Install docker-ce and docker-compose packages with install-docker.sh script

   ```sh
   ./install-docker.sh
   ```

4. Update Masternode information in file .env

    ```sh
    EXTERNAL_IP=<VPS_IP>
    MN1_PRIVKEY=<MASTERNODE_PRIVKEY>
    MN1_RPC_PWD=<SECURE_RPC_PASSWORD>
    ```

5. Start your masternode and wait until blockchain is syncronized. 

    ```sh
    docker-compose up -d
    ```

6. Check if blockchain is synchronised and masternode ready to be activated

    ```sh
    docker exec -it bitcore_mn1 bitcore-cli -datadir=/data -conf=/data/bitcore.conf -rpcconnect=172.21.0.11 -rpcuser=rpc -rpcpassword=<SECURE_RPC_PASSWORD> -rpcport=8556 mnsync status
    {
      "AssetID": 1,
      "AssetName": "MASTERNODE_SYNC_WAITING",
      "AssetStartTime": 1586727148,
      "Attempt": 0,
      "IsBlockchainSynced": false,
      "IsMasternodeListSynced": false,
      "IsWinnersListSynced": false,
      "IsSynced": false,
      "IsFailed": false
    }

    #WAITING...

    docker exec -it bitcore_mn1 bitcore-cli -datadir=/data -conf=/data/bitcore.conf -rpcconnect=172.21.0.11 -rpcuser=rpc -rpcpassword=<SECURE_RPC_PASSWORD> -rpcport=8556 mnsync statu
    {
      "AssetID": 999,
      "AssetName": "MASTERNODE_SYNC_FINISHED",
      "AssetStartTime": 1587319491,
      "Attempt": 0,
      "IsBlockchainSynced": true,
      "IsMasternodeListSynced": true,
      "IsWinnersListSynced": true,
      "IsSynced": true,
      "IsFailed": false
    }
    ```

    PLEASE NOTE: It's very important to wait until all values a true except 'IsFailed' your masternode will not be able to start !!
     
7. Activate your Masternode via BitCore BTX QT Wallet
    Goto your Wallet, open Masternodes Tab, select your Masternode and start it with Start Alias button.

    NOTICE: If your wallet is encrypted, you have to enter your password.

    Now your Masternode Status will be PRE_ENABLED. It normally takes about 20 minutes until the masternode is set to ENABLED, but it can also take longer.
