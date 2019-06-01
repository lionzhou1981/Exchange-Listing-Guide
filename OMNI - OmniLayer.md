# OmniLayer - OMNI

### 官网地址：
http://www.omnilayer.org/

### 钱包下载：
https://github.com/OmniLayer/omnicore/releases

### 查询网站：
https://www.omniexplorer.info

### 开发文档
https://github.com/OmniLayer/omnicore/wiki

### 安装说明：
* 同 BTC - Bitcoin (文件名不同)

### 配置文件：
* 同 BTC - Bitcoin

### 升级说明：
* 同 BTC - Bitcoin

### 创建地址：
* 同 BTC - Bitcoin

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"getblockcount","params":[]}'  
```
2、获取区块内OMNI的交易列表
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_listblocktransactions","params":[区块高度]}'  
```
3、根据TxId获取具体信息
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_gettransaction","params":["交易TxId"]}'  
```
* 注意需要验证valid字段是否为true。
* 追踪OMNI入账的同时需要追踪BTC的入账，以备对外提币是使用

### 对外提币：
1、估算当前费用
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"estimatesmartfee","params":[期望多少个区块内到达]}'  
```

2、创建Payload
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_createpayload_simplesend","params":["Token编号(USDT:31)",支出Token数量]}'  
```

3、创建RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"createrawtransaction","params":[[{"txid":"输出的Txid","vout":输出的Tx的序号}],{}}'  
```

4、在RAW上加载支出数据
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_createrawtx_opreturn","params":["第3步的结果Hex","第2步的结果Hex"}'  
```

5、在RAW上添加接收者的信息
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_createrawtx_reference","params":["第4步的结果Hex","接收者的地址"]}'  
```

6、在RAW上添加手续费金额和支出BTC信息
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"omni_createrawtx_change","params":["第5步的结果Hex",[{"txid":"输出的Txid","vout":输出的Tx的序号,"scriptPubKey":"输出的收款人公钥"}],"找零收款地址",交易手续费金额]}'  
```

7、导入私钥（如果私钥已在节点上，可跳过）
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"importprivkey","params":["私钥密文","标签（可留空）",false]}'  
```

8、签名RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"signrawtransaction","params":["第6步的结果Hex"]}'  
```

9、发送RAW交易
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 \
-d '{"jsonrpc":"1.0","id":"随便写","method":"sendrawtransaction","params":["完成签名的Hex"]}'  
```

### 归集处理：
* 由于OMNI是单对单的交易，所以交易所必须进行一次归集；
* 归集的操作和对外提币的操作相同，提币至交易所的一个专用归集地址即可

### 灾难恢复：
* 同 BTC - Bitcoin

### 从Segwit地址找回：
https://github.com/OmniLayer/omniwallet/wiki/Recovering-funds-from-a-Segwit-Address
