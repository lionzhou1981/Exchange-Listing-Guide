# Ethereum - ETH

### 文档版本：1.9.6
检查日期: 2019.11.04

### 官网地址：
https://www.ethereum.org/

### 钱包下载：
https://geth.ethereum.org/downloads

### 查询网站：
https://etherscan.io/

### 开发文档
https://github.com/ethereum/go-ethereum/wiki

### 安装说明：
1、解压缩文件
```
tar -xvf geth-alltools-linux-amd64-1.8.23-c9427004.tar.gz --warning=no-timestamp
```

2、启动节点
```
/data/eth/geth --maxpeers 100 --rpc --rpcaddr "127.0.0.1" --rpcport 8333  --nat=none --rpcapi db,eth,net,web3,personal --datadir "/data/eth-data"

* 第一次启动增加 --syncmode "fast" 来加快同步速度
```

### 配置文件：
* 配置都在启动参数内

### 升级说明：
1、下载新版本  
2、备份旧版本程序  
3、更换程序文件  
4、重启服务

### 创建地址：
1、创建地址
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"personal_newAccount","params":[]}'  
```
2、备份 `/数据目录/keystore/` 目录下的keystore文件

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_blockNumber","params":[]}'  
```
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getBlockByNumber","params":["0x区块高度数字的HEX"]}'  
```
3、根据TxId获取具体信息
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getTransactionByHash","params":["交易TxId"]}'  
```
4、根据TxId获取具体收据
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getTransactionReceipt","params":["交易TxId"]}'  

* 验证收据的 `result/status` 的值为 0x1，交易成功的标志
```

### 对外提币：
1、解锁账户
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"personal_unlockAccount","params":["交易TxId"]}'  
```

2、获得Gas单价
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_gasPrice","params":[]}'  
```

3、估算Gas数量
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_estimateGas","params":[{"from":"本地归集地址","to":"接收地址","value":"0x交易金额的Hex"}]}'  
```

4、发送RAW交易
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_sendTransaction","params":[{"from":"本地归集地址","to":"接收地址","value":"0x交易金额的Hex","gas":"Gas数量","gasPrice":"Gas单价"}]}'  
```

### 归集处理：
* ETH是1对1的交易，所以交易所需要归集

### 灾难恢复：
* 备份 `/数据目录/keystore` 下的文件和相应地址的账户密码

### 注意事项：
