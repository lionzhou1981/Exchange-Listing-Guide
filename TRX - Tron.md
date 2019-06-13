# Tron - TRX

### 文档版本：1.0.6

### 官网地址：
https://tron.network

### 钱包下载：
https://ww.getmonero.org/downloads/

### 查询网站：
https://tronscan.org/

### 开发文档
https://developers.tron.network/

### 安装说明：
1、安装Python3  
2、安装Orcale Java SDK 1.8（这里注意不能用openjdk，而且必须1.8版本）  
3、安装tron-cli，详见：
```
https://github.com/tronprotocol/tron-cli
```
4、在`数据目录`执行`tron-cli i`

### 配置文件：
* 首次同步较慢，可以通过这里 `https://backups.trongrid.io/` 下载离线包后开启节点
* `/数据目录/tron_nodes/fullnode/full.conf` 可修改配置，也可用`tron-cli config`来修改
* 配置文件中的 `storage/transHistory.switch` 需要设置为 `on` 
* 没找到绑定IP的配置，这里给个差评，所有端口都是绑定到 0.0.0.0 的，这个有安全隐患

### 升级说明：
执行一次 `tron-cli i` 来重新下载新版本。

### 创建地址：
1、生成地址
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/generateaddress
```
2、创建账号
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/createaccount \
-d '{"owner_address":"主账号地址的Hex","account_address":"第1步创建的地址"}'
```
3、对创建账号的交易签名
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/gettransactionsign \
-d '{"transaction":第2步返回的整个JSON,"privateKey":"主账号地址的私钥"}'
```
4、对外广播交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/broadcasttransaction \
-d '第3步返回的JSON'
```

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/getnowblock \
```
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/getblockbynum \
-d '{"num":区块高度}}'  

注意验证以下内容：
ret/contractRet = "SUCCESS"
raw_data/contract/type = "TransferContract"
raw_data/contract/parameter/type_url = "type.googleapis.com/protocol.TransferContract"
```
3、获取单个交易详细
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/gettransactionbyid \
-d '{"value":"交易TxId"}'  
```

### 对外提币：
1、创建交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/createtransaction \
-d '{"to_address":"接收地址的Hex","owner_address":"主账号地址的Hex","amount":转账金额x1000000}'
```
2、签名交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/gettransactionsign \
-d '{"transaction":第1步返回的整个JSON,"privateKey":"主账号地址的私钥"}'
```
4、广播交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/broadcasttransaction \
-d '第2步返回的JSON'
```

### 归集处理：
* TRX是1对1的交易，所以需要进行归集

### 灾难恢复：
* 备份地址的私钥即可恢复

### 注意事项：
