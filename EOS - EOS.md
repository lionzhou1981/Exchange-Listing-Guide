# EOS - EOS

### 文档版本：2.0.3
检查日期: 2020.02.16

### 官网地址：
https://eos.io/

### 钱包下载：
https://github.com/EOSIO/eos/releases

### 查询网站：
https://eosx.io/

### 开发文档
https://developers.eos.io/
* 开发文档严重滞后、差评！

### 安装说明：
1、安装可参照此文档
```
https://developers.eos.io/eosio-home/docs/setting-up-your-environment
```

2、启动节点
```
/usr/bin/nodeos --data-dir (数据目录) --config-dir (config.ini文件所在目录) --genesis-json (genesis.json文件路径)
```

3、启动钱包
```
/usr/bin/keosd --data-dir (钱包文件目录) --config-dir (钱包config.infi目录) --unix-socket-path (sock文件路径)
```
* 可以使用 `screen` 或者 `supervisor` 来运行

### 快照启动
1、下载最新的快照
```
https://snapshots.eossweden.org/
```
2、解压缩快照到任意目录
3、首次执行以下命令启动
```
/usr/bin/nodeos --data-dir (数据目录) --config-dir (config.ini文件所在目录) --snapshot (解压缩后的bin文件的地址)
```
4、以后可以用普通命令启动节点

### 升级说明：
1、停止节点和钱包服务  
2、安装新的版本  
3、重新开启节点和钱包服务

### 创建地址：
1、使用命令行创建一个钱包
```
cleos wallet create -n wallet-name --to-console
```
2、使用命令行创建一个Key
```
cleos wallet create_key -n wallet-name
```
3、使用 eosx.io 对Pubkey进行抵押操作
```
https://www.eosx.io/tools/account/create?by=other&enableTransfer=false&name=&publicKey=
```
4、找一个交易所购买相应的EOS并提取到第3步所指定的地址  
5、开通后可使用，交易所的用户的账号用分配memo编号的方式进行区分  

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/get_info
```
* 获取 `last_irreversible_block_num` 的不可逆的区块高度  
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/get_block \
-d '{"block_num_or_id":区块高度}'  
```
* `account` 为 `eosio.token` 的就是EOS的交易，`account` 字段是EOS Token的区分字段
* `name` 为 `transfer` 的是转账的交易，其余的交易可忽略
* `status` 为 `executed` 的是即时到账的交易，`delayed` 状态的存在隐患，交易所可弃用此状态的交易

### 对外提币：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/get_info
```
* 获取 `last_irreversible_block_num` 的不可逆的区块高度  
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/get_block \
-d '{"block_num_or_id":区块高度}'  
```
* 获取 `ref_block_prefix` 字段
* 获取 `timestamp` 字段，并加上20分钟作为发送时所使用的超时字段  
3、调用 `abi_json_to_bin` 生成转账信息
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/abi_json_to_bin \
-d '{"code":"eosio.token","action":"transfer","args":{"from":"交易所钱包名称","to":"接收人钱包名称","quantity":数量,"memo":"备注信息"}}'  
```
4、解锁钱包
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/wallet/unlock \
-d '["交易所钱包名称","解锁密码"]'  
```
5、对交易进行签名
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/wallet/sign_transaction \
-d '[{"ref_block_num":"第1步取的区块高度","ref_block_prefix":"第2步取的ref_block_prefix","expiration":"第2步取的时间戳加20分钟","max_net_usage_words":0,"max_cpu_usage_ms":0,"delay_sec":0,"signatures":[],"actions":[{"account","eosio.token","name":"transfer","data":"第3步返回的Hex","authorization":[{"actor":"交易所钱包名称","permission":"active"}]}]},"交易所钱包公钥"，"aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906"]'  
```
6、向链上推送交易
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/chain/push_transaction \
-d '{"compression":"none","transaction":{"ref_block_num":"第1步取的区块高度","ref_block_prefix":"第2步取的ref_block_prefix","expiration":"第2步取的时间戳加20分钟","max_net_usage_words":0,"max_cpu_usage_ms":0,"delay_sec":0,"signatures":[],"actions":[{"account","eosio.token","name":"transfer","data":"第3步返回的Hex","authorization":[{"actor":"交易所钱包名称","permission":"active","transaction_extensions":[],"context_free_actions":[]}]}]},"signatures":"第5步的签名"}'  
```
7、锁定钱包
```
curl -X POST -H 'content-type: application/json' http://host:port/v1/wallet/lock \
-d '["交易所钱包名称"]'  
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 单一钱包的私钥保存好即可灾难恢复

### 注意事项：
* 区块快照下载：https://eosnode.tools/blocks
* 停止节点时必须正常停止、否贼需要hard-reply，速度非常慢

### 配置文件：
```
vi /数据目录/config.ini
```
```
# the endpoint upon which to listen for incoming connections (eosio::bnet_plugin)
# bnet-endpoint = 0.0.0.0:4321

# this peer will request only irreversible blocks from other nodes (eosio::bnet_plugin)
# bnet-follow-irreversible = 0

# the number of threads to use to process network messages (eosio::bnet_plugin)
# bnet-threads = 

# remote endpoint of other node to connect to; Use multiple bnet-connect options as needed to compose a network (eosio::bnet_plugin)
# bnet-connect = 

# this peer will request no pending transactions from other nodes (eosio::bnet_plugin)
# bnet-no-trx = false

# The string used to format peers when logging messages about them.  Variables are escaped with ${<variable name>}.
# Available Variables:
#    _name  	self-reported name
# 
#    _id    	self-reported ID (Public Key)
# 
#    _ip    	remote IP address of peer
# 
#    _port  	remote port number of peer
# 
#    _lip   	local IP address connected to peer
# 
#    _lport 	local port number connected to peer
# 
#  (eosio::bnet_plugin)
# bnet-peer-log-format = ["${_name}" ${_ip}:${_port}]

# the location of the blocks directory (absolute path or relative to application data dir) (eosio::chain_plugin)
blocks-dir = "blocks"

# Pairs of [BLOCK_NUM,BLOCK_ID] that should be enforced as checkpoints. (eosio::chain_plugin)
# checkpoint = 

# Override default WASM runtime (eosio::chain_plugin)
wasm-runtime = eos-vm-jit
eos-vm-oc-enable = true

# Override default maximum ABI serialization time allowed in ms (eosio::chain_plugin)
abi-serializer-max-time-ms = 15000

# Maximum size (in MiB) of the chain state database (eosio::chain_plugin)
chain-state-db-size-mb = 200000

# Safely shut down node when free space remaining in the chain state database drops below this size (in MiB). (eosio::chain_plugin)
chain-state-db-guard-size-mb = 128

# Maximum size (in MiB) of the reversible blocks database (eosio::chain_plugin)
reversible-blocks-db-size-mb = 50000

# Safely shut down node when free space remaining in the reverseible blocks database drops below this size (in MiB). (eosio::chain_plugin)
reversible-blocks-db-guard-size-mb = 128

# Number of worker threads in controller thread pool (eosio::chain_plugin)
chain-threads = 20
producer-threads = 20
net-threads = 20
http-threads = 20

# print contract's output to console (eosio::chain_plugin)
contracts-console = false

# Account added to actor whitelist (may specify multiple times) (eosio::chain_plugin)
# actor-whitelist = 

# Account added to actor blacklist (may specify multiple times) (eosio::chain_plugin)
# actor-blacklist = 

# Contract account added to contract whitelist (may specify multiple times) (eosio::chain_plugin)
# contract-whitelist = 

# Contract account added to contract blacklist (may specify multiple times) (eosio::chain_plugin)
# contract-blacklist = 

# Action (in the form code::action) added to action blacklist (may specify multiple times) (eosio::chain_plugin)
# action-blacklist = 

# Public key added to blacklist of keys that should not be included in authorities (may specify multiple times) (eosio::chain_plugin)
# key-blacklist = 

# Deferred transactions sent by accounts in this list do not have any of the subjective whitelist/blacklist checks applied to them (may specify multiple times) (eosio::chain_plugin)
# sender-bypass-whiteblacklist = 

# Database read mode ("speculative", "head", or "read-only").
# In "speculative" mode database contains changes done up to the head block plus changes made by transactions not yet included to the blockchain.
# In "head" mode database contains changes done up to the current head block.
# In "read-only" mode database contains incoming block changes but no speculative transaction processing.
#  (eosio::chain_plugin)
read-mode = speculative

# Chain validation mode ("full" or "light").
# In "full" mode all incoming blocks will be fully validated.
# In "light" mode all incoming blocks headers will be fully validated; transactions in those validated blocks will be trusted 
#  (eosio::chain_plugin)
validation-mode = full

# Disable the check which subjectively fails a transaction if a contract bills more RAM to another account within the context of a notification handler (i.e. when the receiver is not the code of the action). (eosio::chain_plugin)
disable-ram-billing-notify-checks = false

# Indicate a producer whose blocks headers signed by it will be fully validated, but transactions in those validated blocks will be trusted. (eosio::chain_plugin)
# trusted-producer = 

# Track actions which match receiver:action:actor. Actor may be blank to include all. Action and Actor both blank allows all from Recieiver. Receiver may not be blank. (eosio::history_plugin)
# filter-on = 

# Do not track actions which match receiver:action:actor. Action and Actor both blank excludes all from Reciever. Actor blank excludes all from reciever:action. Receiver may not be blank. (eosio::history_plugin)
# filter-out = 

# PEM encoded trusted root certificate (or path to file containing one) used to validate any TLS connections made.  (may specify multiple times)
#  (eosio::http_client_plugin)
# https-client-root-cert = 

# true: validate that the peer certificates are valid and trusted, false: ignore cert errors (eosio::http_client_plugin)
https-client-validate-peers = 1

# The local IP and port to listen for incoming http connections; set blank to disable. (eosio::http_plugin)
http-server-address = 127.0.0.1:8888

# The local IP and port to listen for incoming https connections; leave blank to disable. (eosio::http_plugin)
# https-server-address = 

# Filename with the certificate chain to present on https connections. PEM format. Required for https. (eosio::http_plugin)
# https-certificate-chain-file = 

# Filename with https private key in PEM format. Required for https (eosio::http_plugin)
# https-private-key-file = 

# Specify the Access-Control-Allow-Origin to be returned on each request. (eosio::http_plugin)
# access-control-allow-origin = 

# Specify the Access-Control-Allow-Headers to be returned on each request. (eosio::http_plugin)
# access-control-allow-headers = 

# Specify the Access-Control-Max-Age to be returned on each request. (eosio::http_plugin)
# access-control-max-age = 

# Specify if Access-Control-Allow-Credentials: true should be returned on each request. (eosio::http_plugin)
access-control-allow-credentials = false

# The maximum body size in bytes allowed for incoming RPC requests (eosio::http_plugin)
max-body-size = 1048576

# Append the error log to HTTP responses (eosio::http_plugin)
verbose-http-errors = false

# If set to false, then any incoming "Host" header is considered valid (eosio::http_plugin)
http-validate-host = false

# Additionaly acceptable values for the "Host" header of incoming HTTP requests, can be specified multiple times.  Includes http/s_server_address by default. (eosio::http_plugin)
# http-alias = 

# The maximum number of pending login requests (eosio::login_plugin)
max-login-requests = 1000000

# The maximum timeout for pending login requests (in seconds) (eosio::login_plugin)
max-login-timeout = 60

# The target queue size between nodeos and MongoDB plugin thread. (eosio::mongo_db_plugin)
mongodb-queue-size = 1024

# The maximum size of the abi cache for serializing data. (eosio::mongo_db_plugin)
mongodb-abi-cache-size = 2048

# Required with --replay-blockchain, --hard-replay-blockchain, or --delete-all-blocks to wipe mongo db.This option required to prevent accidental wipe of mongo db. (eosio::mongo_db_plugin)
mongodb-wipe = false

# If specified then only abi data pushed to mongodb until specified block is reached. (eosio::mongo_db_plugin)
mongodb-block-start = 0

# MongoDB URI connection string, see: https://docs.mongodb.com/master/reference/connection-string/. If not specified then plugin is disabled. Default database 'EOS' is used if not specified in URI. Example: mongodb://127.0.0.1:27017/EOS (eosio::mongo_db_plugin)
# mongodb-uri = 

# Update blocks/block_state with latest via block number so that duplicates are overwritten. (eosio::mongo_db_plugin)
mongodb-update-via-block-num = 0

# Enables storing blocks in mongodb. (eosio::mongo_db_plugin)
mongodb-store-blocks = 1

# Enables storing block state in mongodb. (eosio::mongo_db_plugin)
mongodb-store-block-states = 1

# Enables storing transactions in mongodb. (eosio::mongo_db_plugin)
mongodb-store-transactions = 1

# Enables storing transaction traces in mongodb. (eosio::mongo_db_plugin)
mongodb-store-transaction-traces = 1

# Enables storing action traces in mongodb. (eosio::mongo_db_plugin)
mongodb-store-action-traces = 1

# Track actions which match receiver:action:actor. Receiver, Action, & Actor may be blank to include all. i.e. eosio:: or :transfer:  Use * or leave unspecified to include all. (eosio::mongo_db_plugin)
# mongodb-filter-on = 

# Do not track actions which match receiver:action:actor. Receiver, Action, & Actor may be blank to exclude all. (eosio::mongo_db_plugin)
# mongodb-filter-out = 

# The actual host:port used to listen for incoming p2p connections. (eosio::net_plugin)
p2p-listen-endpoint = 0.0.0.0:9876

# An externally accessible host:port for identifying this node. Defaults to p2p-listen-endpoint. (eosio::net_plugin)
# p2p-server-address = 

# The public endpoint of a peer node to connect to. Use multiple p2p-peer-address options as needed to compose a network. (eosio::net_plugin)
# p2p-peer-address = 

p2p-peer-address = seed.acroeos.one:9876

# alohaeosprod: GB, London
p2p-peer-address = peer.main.alohaeos.com:9876

# argentinaeos: AR, eosargentina
p2p-peer-address = p2p.eosargentina.io:5222

# atticlabeosb: UA, Kyiv
p2p-peer-address = eosbp-0.atticlab.net:9876

# auroraeoscom: US, Virginia
p2p-peer-address = mainnet.auroraeos.com:9870

# aus1genereos: AU, Sydney
p2p-peer-address = p2p.genereos.io:9876

# bitfinexeos1: GB, London
p2p-peer-address = eos-bp.bitfinex.com:9876

# bitspacenode: GB, London
p2p-peer-address = p2p.eos.bitspace.no:9876

# blockchained: DE, EOSBLCKCHND
p2p-peer-address = p2p-eos.blckchnd.com:19876

# blockgenesys: GB, London
p2p-peer-address = node1.blockgenesys.com:9876

# blocksmithio: US, Oklahoma
p2p-peer-address = anvil.eosblocksmith.io:9876

# bp.bp: SG, BitPortal
p2p-peer-address = 47.105.190.128:9376

# chainriftxxx: SI, Ljubljana
p2p-peer-address = eosboot.chainrift.com:9876

# costaricaeos: CR, San Jose
p2p-peer-address = p2p.eosio.cr:1976

# cryptolions1: DE, Germany-Finland
p2p-peer-address = bp.cryptolions.io:9876

# cypherglasss: US, Minneapolis, MN
p2p-peer-address = publicnode.cypherglass.com:9876

# dutcheosxxxx: NL, Full, Naaldwijk
p2p-peer-address = p2p.telos.dutcheos.io:9876

# eos42freedom: GB, London
p2p-peer-address = seed2.eos42.io:9876

# eosarabianet: DE, Munich
p2p-peer-address = mainnet.eosarabia.net:3571

# eosasia11111: TW, Changhua
p2p-peer-address = peering1.mainnet.eosasia.one:80

# eosauthority: GB, London
p2p-peer-address = node869-mainnet.eosauthority.com:9393

# eosbarcelona: DE, Germany
p2p-peer-address = mainnet.eosbcn.com:2095

# eosbeaneosbp: US, Kansas
p2p-peer-address = peer1.eosbean.com:9876

# eosbeijingbp: JP, Tokyo
p2p-peer-address = bp.eosbeijing.one:8080

# eosbixinboot: CN, HongKong
p2p-peer-address = mars.fnp2p.eosbixin.com:443

# eoscafeblock: CA, Canada
p2p-peer-address = mainnet.eoscalgary.io:5222

# eoscanadacom: CA, Montreal, Canada
p2p-peer-address = peering.mainnet.eoscanada.com:9876

# eoscannonchn: HK, Hong Kong
p2p-peer-address = node1.eoscannon.io:59876

# eoscleanerbp: US, SV Node A-seed
p2p-peer-address = seed.eoscleaner.com:19876

# eosdacserver: KR, Seoul
p2p-peer-address = ro1.eosdac.io:49876

# eosdotwikibp: SG, Singapore
p2p-peer-address = api.eos.wiki:39876

# eosdublinwow: IE, Dublin
p2p-peer-address = eos-seed.eosdublin.io:9876

# eosecoeoseco: ??, Tokyo
p2p-peer-address = mainnet.eoseco.com:10010

# eoseouldotio: KR, EOSSeoul-seed1
p2p-peer-address = p2p.eoseoul.io:9876

# eosfengwocom: JP, Tokyo
p2p-peer-address = peer.eosfengwo.com:8080

# eosflareiobp: US, GlobalDAC
p2p-peer-address = node.eosflare.io:1883

# eosflytomars: JP, Tokyo
p2p-peer-address = p2p.bitmars.one:8080

# eosgenblockp: IS, Iceland
p2p-peer-address = eos.genesis-mining.com:19876

# eosgermanybp: DE, Falkenstein
p2p-peer-address = p2p.mainnet.eos.eosgermany.online:9876

# eoshuobipool: CN, HuobiNode1
p2p-peer-address = peer1.eoshuobipool.com:18181

# eoshuobipool: CN, HuobiNode2
p2p-peer-address = peer2.eoshuobipool.com:18181

# eosimperabpi: IT, Italy
p2p-peer-address = api.eosimpera.com:9876

# eosiodetroit: US, EOSDetroitNP1
p2p-peer-address = p2p.eosdetroit.io:3018

# eosiomeetone: CN, Taiwan
p2p-peer-address = p2p.meet.one:9876

# eosiosg11111: TW, Taiwan
p2p-peer-address = peer.eosio.sg:80

# eosisgravity: SG, Singapore
p2p-peer-address = p2p-mainnet.eosgravity.com:8001

# eoslaomaocom: US, NewYork
p2p-peer-address = fullnode.eoslaomao.com:443

# eoslaomaocom: US, NewYork
p2p-peer-address = mainnet.eoslaomao.com:443

# eosliquideos: IL, Israel - Center
p2p-peer-address = node2.liquideos.com:9876

# eosmatrixeos: IN, India
p2p-peer-address = 35.200.134.56:9876

# eosnationftw: CA, Toronto
p2p-peer-address = peer.eosn.io:9876

# eosnewyorkio: BR, primary
p2p-peer-address = node1.eosnewyork.io:6987

# eosninetiess: TW, Changhua
p2p-peer-address = peering1.mainnet.eosasia.one:80

# eosnodeonebp: KR, Seoul
p2p-peer-address = 807534da.eosnodeone.io:19872

# eosoceaniabp: NZ, Wellington, WLG
p2p-peer-address = p2p.eosoceania.io:19876

# eosonoeosono: JP, Tokyo
p2p-peer-address = peer.ono.chat:9876

# eospaceioeos: US, LosAngelesSeed1
p2p-peer-address = p2p.mainnet.eospacex.com:88

# eospacificbp: JP, Tokyo, Japan
p2p-peer-address = eos.nodepacific.com:9876

# eosphereiobp: AU, Sydney
p2p-peer-address = peer1.eosphere.io:9876

# eosplayworld: TW, Taiwan
p2p-peer-address = 35.186.155.213:9876

# eosriobrazil: BR, Rio de Janeiro
p2p-peer-address = br.eosrio.io:9876

# eossv12eossv: ??, Montreal
p2p-peer-address = fn001.eossv.org:443

# eosswedenorg: SE, Sweden
p2p-peer-address = p2p.eossweden.se:9876

# eostitanprod: CA, Toronto
p2p-peer-address = boot.eostitan.com:9876

# eostribeprod: US, Bluffdale,UT
p2p-peer-address = api.eostribe.io:9115

# eosukblocpro: DE, Falkenstein
p2p-peer-address = api.eosuk.io:12000

# eosvibesbloc: FR, Gravelines
p2p-peer-address = node1.eosvibes.io:9876

# eoswinwinwin: JP, Tokyo
p2p-peer-address = peer.eoswin.info:9876

# eosyskoreabp: KR, Seoul
p2p-peer-address = pub0.eosys.io:6637

# eosyskoreabp: KR, Seoul
p2p-peer-address = pub1.eosys.io:6637

# franceosysbp: GB, London
p2p-peer-address = peer2.franceos.fr:19876

# helloeoscnbp: JP, Tokyo
p2p-peer-address = peer1.mainnet.helloeos.com.cn:80

# itokenpocket: SG, TokenPocket
p2p-peer-address = 47.105.190.128:9376

# jedaaaaaaaaa: JP, Tokyo
p2p-peer-address = m.jeda.one:3322

# jrrcryptoeos: JP, eosjrr_tokyo
p2p-peer-address = peer.eosjrr.io:9876

# kunablockprd: UA, Ukraine
p2p-peer-address = eosbp.kuna.io:9876

# libertyblock: CA, Toronto, Canada
p2p-peer-address = p2p.libertyblock.io:9800

# moreisfuture: ??, Shenzhen
p2p-peer-address = peering.mainnet.more.top:443

# onechaindapp: ??, chongqing
p2p-peer-address = eosdapp.oneeos.org:9000

# oraclegogogo: SG, Singapore
p2p-peer-address = peer.oraclechain.io:19876

# privexinceos: ??, Falkenstein
p2p-peer-address = eos-seed-de.privex.io:9876

# sheos21sheos: IE, Dublin
p2p-peer-address = p2p.sheos.org:5556

# stakedstaked: US, Virginia
p2p-peer-address = eos.staked.us:9870

# starteosiobp: US, Ohio
p2p-peer-address = node1.starteos.io:9876

# superoneiobp: CN, Shenzhen, China
p2p-peer-address = bp.superone.io:57576

# switzerlanda: CH, Geneva
p2p-peer-address = peer.eosgeneva.io:9876

# teamgreymass: CA, YUL
p2p-peer-address = seed.greymass.com:9876

# unlimitedeos: JP, Tokyo
p2p-peer-address = eos.unlimitedeos.com:15555

# zbeosbp11111: ??, ZBnode1
p2p-peer-address = node1.zbeos.com:9876

# zbeosbp11111: ??, ZBnode2
p2p-peer-address = node2.zbeos.com:9876

# Maximum number of client nodes from any single IP address (eosio::net_plugin)
p2p-max-nodes-per-host = 1

# The name supplied to identify this node amongst the peers. (eosio::net_plugin)
agent-name = "EOS Node"

# Can be 'any' or 'producers' or 'specified' or 'none'. If 'specified', peer-key must be specified at least once. If only 'producers', peer-key is not required. 'producers' and 'specified' may be combined. (eosio::net_plugin)
allowed-connection = any

# Optional public key of peer allowed to connect.  May be used multiple times. (eosio::net_plugin)
# peer-key = 

# Tuple of [PublicKey, WIF private key] (may specify multiple times) (eosio::net_plugin)
# peer-private-key = 

# Maximum number of clients from which connections are accepted, use 0 for no limit (eosio::net_plugin)
max-clients = 25

# number of seconds to wait before cleaning up dead connections (eosio::net_plugin)
connection-cleanup-period = 30

# max connection cleanup time per cleanup call in millisec (eosio::net_plugin)
max-cleanup-time-msec = 10

# True to require exact match of peer network version. (eosio::net_plugin)
network-version-match = 0

# number of blocks to retrieve in a chunk from any individual peer during synchronization (eosio::net_plugin)
sync-fetch-span = 100

# maximum sizes of transaction or block messages that are sent without first sending a notice (eosio::net_plugin)
#max-implicit-request = 1500

# Enable expirimental socket read watermark optimization (eosio::net_plugin)
use-socket-read-watermark = 0

# The string used to format peers when logging messages about them.  Variables are escaped with ${<variable name>}.
# Available Variables:
#    _name  	self-reported name
# 
#    _id    	self-reported ID (64 hex characters)
# 
#    _sid   	first 8 characters of _peer.id
# 
#    _ip    	remote IP address of peer
# 
#    _port  	remote port number of peer
# 
#    _lip   	local IP address connected to peer
# 
#    _lport 	local port number connected to peer
# 
#  (eosio::net_plugin)
peer-log-format = ["${_name}" ${_ip}:${_port}]

# Enable block production, even if the chain is stale. (eosio::producer_plugin)
enable-stale-production = false

# Start this node in a state where production is paused (eosio::producer_plugin)
pause-on-startup = false

# Limits the maximum time (in milliseconds) that is allowed a pushed transaction's code to execute before being considered invalid (eosio::producer_plugin)
max-transaction-time = 30

# Limits the maximum age (in seconds) of the DPOS Irreversible Block for a chain this node will produce blocks on (use negative value to indicate unlimited) (eosio::producer_plugin)
max-irreversible-block-age = -1

# ID of producer controlled by this node (e.g. inita; may specify multiple times) (eosio::producer_plugin)
producer-name = eosnode

# (DEPRECATED - Use signature-provider instead) Tuple of [public key, WIF private key] (may specify multiple times) (eosio::producer_plugin)
# private-key = 

# Key=Value pairs in the form <public-key>=<provider-spec>
# Where:
#    <public-key>    	is a string form of a vaild EOSIO public key
# 
#    <provider-spec> 	is a string in the form <provider-type>:<data>
# 
#    <provider-type> 	is KEY, or KEOSD
# 
#    KEY:<data>      	is a string form of a valid EOSIO private key which maps to the provided public key
# 
#    KEOSD:<data>    	is the URL where keosd is available and the approptiate wallet(s) are unlocked (eosio::producer_plugin)
signature-provider = EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV=KEY:5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3

# Limits the maximum time (in milliseconds) that is allowd for sending blocks to a keosd provider for signing (eosio::producer_plugin)
keosd-provider-timeout = 5

# account that can not access to extended CPU/NET virtual resources (eosio::producer_plugin)
# greylist-account = 

# offset of non last block producing time in microseconds. Negative number results in blocks to go out sooner, and positive number results in blocks to go out later (eosio::producer_plugin)
produce-time-offset-us = 0

# offset of last block producing time in microseconds. Negative number results in blocks to go out sooner, and positive number results in blocks to go out later (eosio::producer_plugin)
last-block-time-offset-us = 0

# ratio between incoming transations and deferred transactions when both are exhausted (eosio::producer_plugin)
incoming-defer-ratio = 1

# the location of the snapshots directory (absolute path or relative to application data dir) (eosio::producer_plugin)
snapshots-dir = "snapshots"

# the location of the state-history directory (absolute path or relative to application data dir) (eosio::state_history_plugin)
state-history-dir = "state-history"

# enable trace history (eosio::state_history_plugin)
trace-history = false

# enable chain state history (eosio::state_history_plugin)
chain-state-history = false

# the endpoint upon which to listen for incoming connections (eosio::state_history_plugin)
state-history-endpoint = 0.0.0.0:8080

# Lag in number of blocks from the head block when selecting the reference block for transactions (-1 means Last Irreversible Block) (eosio::txn_test_gen_plugin)
txn-reference-block-lag = 0

# Plugin(s) to enable, may be specified multiple times
# plugin = 
plugin = eosio::mongo_db_plugin
#plugin = eosio::bnet_plugin
plugin = eosio::net_plugin
plugin = eosio::chain_plugin
plugin = eosio::chain_api_plugin
plugin = eosio::http_plugin
plugin = eosio::net_api_plugin
plugin = eosio::history_api_plugin
```
```
vi /钱包目录/config.ini
```
```
# The filename (relative to data-dir) to create a unix socket for HTTP RPC; set blank to disable. (eosio::http_plugin)
unix-socket-path = 

# The local IP and port to listen for incoming http connections; leave blank to disable. (eosio::http_plugin)
http-server-address = 127.0.0.1:8889

# The local IP and port to listen for incoming https connections; leave blank to disable. (eosio::http_plugin)
# https-server-address = 

# Filename with the certificate chain to present on https connections. PEM format. Required for https. (eosio::http_plugin)
# https-certificate-chain-file = 

# Filename with https private key in PEM format. Required for https (eosio::http_plugin)
# https-private-key-file = 

# Specify the Access-Control-Allow-Origin to be returned on each request. (eosio::http_plugin)
# access-control-allow-origin = 

# Specify the Access-Control-Allow-Headers to be returned on each request. (eosio::http_plugin)
# access-control-allow-headers = 

# Specify the Access-Control-Max-Age to be returned on each request. (eosio::http_plugin)
# access-control-max-age = 

# Specify if Access-Control-Allow-Credentials: true should be returned on each request. (eosio::http_plugin)
access-control-allow-credentials = false

# The maximum body size in bytes allowed for incoming RPC requests (eosio::http_plugin)
max-body-size = 1048576

# Append the error log to HTTP responses (eosio::http_plugin)
verbose-http-errors = false

# If set to false, then any incoming "Host" header is considered valid (eosio::http_plugin)
http-validate-host = false

# Additionaly acceptable values for the "Host" header of incoming HTTP requests, can be specified multiple times.  Includes http/s_server_address by default. (eosio::http_plugin)
# http-alias = 

# The path of the wallet files (absolute path or relative to application data dir) (eosio::wallet_plugin)
wallet-dir = ""

# Timeout for unlocked wallet in seconds (default 900 (15 minutes)). Wallets will automatically lock after specified number of seconds of inactivity. Activity is defined as any wallet command e.g. list-wallets. (eosio::wallet_plugin)
unlock-timeout = 900

# Override default URL of http://localhost:12345 for connecting to yubihsm-connector (eosio::wallet_plugin)
# yubihsm-url = 

# Enables YubiHSM support using given Authkey (eosio::wallet_plugin)
# yubihsm-authkey = 

# Plugin(s) to enable, may be specified multiple times
# plugin = 
```
