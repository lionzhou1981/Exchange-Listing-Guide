# Bitcoin - BTC

### 文档版本：0.18.0

### 官网地址：
https://bitcoin.org/

### 钱包下载：
https://bitcoincore.org/en/download/

### 查询网站：
https://btc.com/

### 开发文档
https://bitcoincore.org/en/doc/0.18.0/

### 安装说明：
1、解压缩文件
```
tar -xvf bitcoin-0.18.0-x86_64-linux-gnu.tar.gz
mv bitcoin-0.18.0-x86_64-linux-gnu bitcoin
```

2、创建服务
```
vi /usr/lib/systemd/system/bitcoin.service
```
```
[Unit]
Description=Bitcoin

[Service]
Type=forking

ExecStart=/安装目录/bin/bitcoind -daemon -conf=/数据目录/bitcoin.conf
ExecStop=/安装目录/bin/bitcoin-cli -stop -conf=/数据目录/bitcoin.conf

[Install]
WantedBy=multi-user.target
```

### 配置文件：
```
vi /数据目录/bitcoin.conf
```
```
bind=127.0.0.1              #绑定端口，建议127.0.0.1
server=1                    
txindex=1
datadir=/btc-data           #数据存储目录

rpcbind=127.0.0.1           #RPC端口，建议127.0.0.1
rpcport=8332
rpcuser=                    #RPC用户名
rpcauth=                    #RPC认证信息
rpcallowip=                 #允许访问的IP
```

### 升级说明：
1、下载最新的版本  
2、重命名旧版本的程序目录以做备份  
3、解压缩新的压缩包并改名为bitcoin  
4、重启服务
```
systemctl restart bitcoin
```
5、观察debug.log是否正常
```
tail -f /数据目录/debug.log
```

### 创建地址：
1、创建地址
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"getnewaddress","params":[]}'  
```
2、获取私钥
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"dumpprivkey","params":["Bitcoin地址"]}'
```

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"getblockcount","params":[]}'  
```
2、根据区块高度获取区块Hash
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"getblockhash","params":[区块高度]}'  
```
3、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"getblock","params":["区块Hash"]}'  
```
4、根据TxId获取具体信息
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"gettransaction","params":["交易TxId"]}'  
```

### 对外提币：
1、估算当前费用
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"estimatesmartfee","params":[期望多少个区块内到达]}'  
```

2、创建RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"createrawtransaction","params":[[{"txid":"输出的Txid","vout":输出的Tx的序号}],[{"接收地址":接收金额}]}'  
```

3、签名RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"signrawtransactionwithkey","params":["创建RAW返回的Hex",["相关输出地址的私钥"]]}'  
```

4、发送RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"sendrawtransaction","params":["完成签名的Hex"]}'  
```

### 归集处理：
* Bitcoin无需归集

### 灾难恢复：
* 用户私钥加密后另外存储，以做备份，灾难发生时重建节点即可

### 注意事项：
