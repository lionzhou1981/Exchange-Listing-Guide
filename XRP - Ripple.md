# Ripple - XRP

### 文档版本：1.2.4

### 官网地址：
https://ripple.com/

### 钱包下载：
* 直接通过源安装

### 查询网站：
https://xrpcharts.ripple.com/

### 开发文档
https://developers.ripple.com/

### 安装说明：
```
/opt/ripple/bin/update-rippled.sh
```

### 配置文件：
```
vi /opt/ripple/etc/rippled.cfg 
```
* 根据服务器配置修改 `node_size` 的配置
* `node_db` 中的 `online_delete` 可配置保留多少个区块的数据
* `ledger_history` 的配置可设置回溯多少个区块
* 配置文件尾部添加 `signing_support` 配置块，值为 `true`

### 升级说明：
根据文档的说明进行更新
```
https://developers.ripple.com/install-rippled.html
```

### 创建地址：
1、通过命令行创建一个地址
```
/opt/ripple/bin/rippled wallet_propose
```
2、往地址上打入20XRP用来激活  
3、交易所用户的地址使用TAG来进行区分，所以整个交易所只需要一个地址即可

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:5005/ \
-d '{"method":"ledger_current","params":[]}'  
```
2、根据区块高度获取叫一列表
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:5005/ \
-d '{"method":"ledger","params":{"binary":false,"ledger_index":区块高度,"transactions":true,"expand":true}}'  
```
3、根据TxId获取具体信息
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:5005/ \
-d '{"method":"tx","params":{"binary":false,"transaction":交易Id}}'  
```
* 注意：金额使用delivered_amount字段，而不是amount字段

### 对外提币：
1、获取当前的手续费
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:5005/ \
-d '{"method":"fee","params":[]}'  
```
2、提交交易
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:5005/ \
-d '{"method":"submit","params":{"seed_hex":"创建地址时的master_hex","key_type":"secp256k1","tx_json":{"TransactionType":"Payment","Account":"交易所地址","Destination":"接收地址","Amount":支出数量,"DestinationTag":"接受地址TAG"},"fee_mult_max":第1步取到的手续费}}'  
```
* 支出数量需要 x 1000000

### 归集处理：
* 无需归集

### 灾难恢复：
* 唯一地址的私钥备份好即可

### 注意事项：
