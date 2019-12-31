# Tron TRC10 Token - TRC10

### 文档版本：3.6.5
检查日期: 2019.12.31

### 官网地址：
https://tron.network

### 钱包下载：
https://ww.getmonero.org/downloads/

### 查询网站：
https://tronscan.org/

### 开发文档
https://developers.tron.network/

### 安装说明：
* 同 TRX - Tron

### 配置文件：
* 同 TRX - Tron

### 升级说明：
* 同 TRX - Tron

### 创建地址：
* 同 TRX - Tron

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
raw_data/contract/parameter/type_url = "type.googleapis.com/protocol.TransferAssetContract"
raw_data/contract/parameter/value/asset_name = 是Token的名称的ASCII值
```
3、获取单个交易详细
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/gettransactionbyid \
-d '{"value":"交易TxId"}'  
```

### 对外提币：
1、创建交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8500/wallet/transferasset \
-d '{"to_address":"接收地址的Hex","owner_address":"主账号地址的Hex","amount":转账金额,"asset_name":"合约地址"}'
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
* 同 TRX - Tron

### 灾难恢复：
* 同 TRX - Tron

### 注意事项：
