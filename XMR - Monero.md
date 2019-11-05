# Monero - XMR

### 文档版本：0.14.1.2
检查日期: 2019.11.04

### 官网地址：
https://ww.getmonero.org/

### 钱包下载：
https://ww.getmonero.org/downloads/

### 查询网站：
https://moneroblocks.info/

### 开发文档
https://ww.getmonero.org/resources/developer-guides/

### 安装说明：
1、解压缩文件  
2、启动节点
```
/安装目录/monerod --data-dir=数据目录
```
3、启动钱包RPC（未创建钱包时）
```
/安装目录/monero-wallet-rpc --rpc-bind-port RPC端口 --wallet-dir 钱包目录 --disable-rpc-login
```
4、启动钱包RPC（创建完钱包后）
```
/安装目录/monero-wallet-rpc --rpc-bind-port RPC端口 --wallet-file 钱包文件 --disable-rpc-login --prompt-for-password
```

### 配置文件：
* 根据需求修改配置

### 升级说明：
1、下载最新的版本  
2、重命名旧版本的程序目录以做备份  
3、解压缩新的压缩包并改名为bitcoin  
4、重启节点和钱包

### 创建地址：
1、通过命令行生成一个钱包
```
/安装目录/monero-wallet-cli --generate-new-wallet 钱包名称
```
2、为每个用户生成独立的Payment ID  

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"get_height","params":[]}'  
```
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"get_transfers","params":{"in":true,"filter_by_height":true,"min_height":起始高度,"max_height":结束高度}}'  
```
3、解析收款人地址
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"get_address","params":{"account_index":本地账户索引号,"address_index":第2步所得到的/in/subaddr_index/minor的值}}'  
```
4、根据地址获取索引
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"get_address_index","params":{"address":本地收款地址}}'  
```
5、获取单个交易详细
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"get_transfer_by_txid","params":{"txid":"交易TxId","account_index":第4步所得到的/index/major的值}}'  
```

### 对外提币：
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:PRC端口/ \
-d '{"jsonrpc":"1.0","id":"随便写","method":"transfer","params":{"destinations":[{"address":"接收方的Address","amount":发送数量}],"account_index":本地账户Account序号,"priority":0,"mixin":,0"ring_size":1,"get_tx_metadata":false,"get_tx_hex":true,"new_algorithm":false,"unlock_time":0,"payment_id":"接收方的Payment ID"}}'  
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 由于是单地址模式所以备份单一钱包文件即可

### 注意事项：
