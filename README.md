<p align="center">
    <img src="https://www.btcschools.net/media/images/github/bitcoinborn.PNG"/>
    <h3 align="center">Build Bitcoin With PHP & Mysql</h3>
    <p align="center">
    So let's heading back to year 2008 and meet Satoshi Nakamoto
    </p>
</p>
<br/>
<br/>

## About The Project
In this tutorial we will code from zero to form a basic features of working cryptocurrency. Most of work meets minimal working requirement and aims for simplicity and easy understanding.

We are using `PHP` and `MYSQL `mainly for backend, and a little bit `Jquery` when come to frontend.

This project is not ready for production launching, but rather we try to show you the basic principles of cryptocurrency that can be implemented in a concise way.

I hope this tutorial is helpful to you to step into blockchain development.

应一定数量的中文读者，作者才考虑是否将文章全文中文化。希望本项目可以帮助你了解区块连与加密货币是如何运作。

## Deployment (PHP>=5.5 & Mysql 5.6)

1. Navigate to web root, then download git
```sh
# navigate to your working web root, assume web root folder is name public_html
$ cd public_html

# extract source code without project folder
$ git clone https://github.com/atlaschiew/build-bitcoin-php-mysql.git .
```
2. Create a new Mysql database and name it as `bitcoin_db`, then import and execute all sqls contain in `public_html/mysql.sql`. Repeat this step twice for the other 2 nodes. Make sure all three databases  `bitcoin_db`,  `bitcoin_db2` and  `bitcoin_db3` are in there.

3. Create mysql user `bitcoin_user` with proper password and privilleges and link it up to all 3 databases above. Please grant the new user with following privileges: `SELECT, INSERT, UPDATE, DELETE, CREATE, INDEX, DROP, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES`.

4. Please make sure these two php extensions `bcmath` and `gmp` are enabled.

5. We need 3 ip addresses for 3 TCP servers respectively to form a P2P network. Once you connected to internet, you should be able to get your PUBLIC IP, PRIVATE IP and LOCALHOST IP. In this tutorial, we assume public ip is 210.191.1.97, private ip is 192.168.0.12 and localhost ip is always be 127.0.0.1.

6. Now open up 3 command line interface (cli) as 3 new windows. Login to each of them and proceed to next step.

7. Now you are ready to run all 3 nodes in php-cli mode. Default port is 9981.
```sh
#cli 1
$ php public_html/Main.php --runAs="192.168.0.12" --addNodes="127.0.0.1,210.191.1.97" --dbHost="localhost" --dbName="bitcoin_db" --dbUser="bitcoin_user" --dbPwd='anypassword'

#cli 2
$ php public_html/Main.php --runAs="210.191.1.97" --addNodes="127.0.0.1,192.168.0.12" --dbHost="localhost" --dbName="bitcoin_db2" --dbUser="bitcoin_user" --dbPwd='anypassword'

#cli 3
$ php public_html/Main.php --runAs="127.0.0.1" --addNodes="210.191.1.97,192.168.0.12" --dbHost="localhost" --dbName="bitcoin_db3" --dbUser="bitcoin_user" --dbPwd='anypassword'
```
<p align="center">
    <a href="https://www.btcschools.net/media/images/github/show_3_cli.PNG" target="_blank"><img src="https://www.btcschools.net/media/images/github/show_3_cli.PNG" width="400px" height="250px"></a><br/>
    Diagram 1. Three nodes are running now.
</p>

8. Make sure your HTTP server is running fine. Now open up 3 different browser and navigate to http://localhost/blockexplorer/. To view blockchain, you need to fill in different ip for each blockexplorer and click connect button.
<p align="center">
    <a href="https://www.btcschools.net/media/images/github/blockexplorer.PNG" target="_blank"><img src="https://www.btcschools.net/media/images/github/blockexplorer.PNG" width="400px" height="180px"></a><br/>
    Diagram 2. Blockexplorer with 1 block, a.k.a genesis block.
</p>

9. Get a new address at http://localhost/blockexplorer/new_address.php, then run miner at http://localhost/miner/ to receive network rewards.
<p align="center">
    <a href="https://www.btcschools.net/media/images/github/miner.PNG" target="_blank"><img src="https://www.btcschools.net/media/images/github/miner.PNG" width="400px" height="180px"></a><br/>
    Diagram 3. Miner is running.
</p>

## Project And Table Structure
```sh
blockexplorer/         # Blockexplorer program
miner/                 # Miner program
Vendor/
  Tuaris/
    CryptoCurrencyPHP/ # https://github.com/tuaris/CryptoCurrencyPHP, mainly apply for key-pair generation and signature operation.
Address.php            # Address related functions,such as getBalance() and newAddress().
Block.php              # Block related functions, such as produce valid block structure.
Chain.php              # Core of program, manage requests and coordinate node to work correctly.
Config.php             # Conf file, can be overwritten by command line argument.
Consensus.php          # Validation and verification functions for node to abide network consensus.
Main.php               # An entry point for this program.
mysql.sql              # Database tables to be imported.
Network.php            # TCP socket functions to form the P2P network and serve RPC calling.
Transaction.php        # Contain 3 classes, Transaction, TxIn & TxOut to produce valid TX strcture.
TxPool.php             # A place to hold pending txs. A.k.a mempool in bitcoin.
Utils.php              # Common use functions and classes.
Utxo.php               # Unspent tx output (UTXO) related functions.
```
<p align="center">
    Project structure
</p>

```sh
blocks                 # A block is produced by a miner by solving the POW puzzle, it contains many TXes, many blocks are linked up to form a blockchain.
  id                   # Auto-increment primary id.
  blockIndex           # Block height.
  previousHash         # Parent block for this new block to link to.
  timestamp            # Block created time.
  data                 # Block body, contain multiple TXes.
  hash                 # Current block hash.
  difficulty           # A rate to balance block generation spped, difficulty rate down when block generation is fast or converse.
  target               # A target to hit in order to solve the POW puzzle, if hash <= target then the block is mined successfully.
  chainWork            # Chainwork is accumulated from block to block. New mined block will add onto the chain with most chain work.
  nonce                # An answer to the POW puzzle.
  
blockTxIns             # Could be reproduce from `blocks.data`, This table is for quick query usage.
  id                   # Auto-increment primary id.
  address              # Address belong to this TX's input.
  txId                 # TX id.
  blockHash            # Associated block hash.
  blockIndex           # Associated block height.
  txOutId              # Previous Unspent TX id.
  txOutIndex           # Previous output index in Unspent TX .
  signature            # Signature generate from signing this input.
  
blockTxOuts            # Could be reproduce from `blocks.data`, This table is for quick query usage.
  id                   # Auto-increment primary id.
  txId                 # TX id.    
  txOutIndex           # TX output index.
  blockHash            # Associated block hash.
  blockIndex           # Associated block height.
  address              # Output address, or known as recipient address from end user perspective.
  amount               # Send amount.
  
blockTxs               # Could be reproduce from `blocks.data`, This table is for quick query usage.
  id                   # Auto-increment primary id.          
  txId                 # TX id.
  blockHash            # Associated block hash.
  blockIndex           # Associated block height.
  timestamp            # Time submit to TX pool.
  txFees               # TX fees pay to miner, can be reproduce from sum(total TX input amount) - sum(total TX output amount).
  
fork                   # To store status of different fork (branches).
  id                   # Auto-increment primary id.   
  status               # Status of fork.
    valid-fork         # Verified chain, this chain is connected somewhere to main chain.
    valid-headers      # Valid block, program will check and download the chain.
    active             # Current active chain with most chain work.
  chainWork            # Lastest chain work.
  lastFork             # Block for Branch head to extend to.
  branchStartAt        # Branch head.
  lastBlockIndex       # Latest block height.
  lastBlockHash        # Latest block hash.
  
peers                  # To store peers around, so program can broadcast data to all of them.
  id                   # Auto-increment primary id.
  host                 # Peer IP.
  lastUpdateDate       # Added time.
  
transactionPool        # To store pending TXs. A.k.a `mempool` in bitcoin.
  txId                 # Pending TX id.
  timestamp            # Added time.
  txFees               # TX fees pay to miner.
  txIns                # TX's inputs (JSON).
  txOuts               # TX's outputs (JSON).
  
transactionPoolTxIns   # To prevent same UTXO being insert twice. Bitcoin allow this by enabling opt-in RBF (replace by fees).
  id                   # Auto-increment primary id.
  txId                 # TX id.
  txOutId              # Previous Unspent TX id.
  txOutIndex           # Previous output index in Unspent TX. 
  
unspentTxOuts          # Could be reproduce from `blocks.data`. Maintain active UTXO.
  id                   # Auto-increment primary id.   
  blockIndex           # Associated block height. 
  txOutId              # Previous Unspent TX id.  
  txOutIndex           # Previous output index in Unspent TX. 
  address              # Associated address.
  amount               # Unspent amount.
```
<p align="center">
    Table structure
</p>

## Coding Study

### Initial Stage
1. `Main.php` 
  ** Loads all the neccesary php files. 
  * Parse command line argument.
  * Initialise system task variable
  * Connect mysql and start chain.

2. `Chain.php -> start()` 
  * Script add genesis block if not exist.
  * Add initial peers from conf and 
  * Ready to start TCP server for listening with default port is 9981.

3. `Network.php > runServer(...)` 
  * Tell other peers to send me peers known by them.
  * Tell other peers to send me new blocks start last block of my active chain.
  * Grant `systemTask.downloadBlocks` task. This task will be auto kill if idle longer than 5 seconds.
  * Start TCP server and ready to accept new client connection.
  * Any valid client request will be handle by `Chain.php -> handleRequest(...)`.
  
By now, your node will receive new peers and new block from other if there are.

### Request Handling
