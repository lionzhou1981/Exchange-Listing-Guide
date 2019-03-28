# NEP5 - NEO NEP5 Token

### 官网地址：
https://neo.org/

### 钱包下载：
https://github.com/neo-project/neo-cli/releases

### 查询网站：
https://neotracker.io/

### 开发文档
https://docs.neo.org/

### 安装说明：
* 同 NEO - NEO

### 配置文件：
* 同 NEO - NEO

### 升级说明：
* 同 NEO - NEO

### 创建地址：
* 同 NEO - NEO

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"getblockcount","params":[]}'  
```
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"getblock","params":[区块高度,1]}'  
```
3、根据TxId获取具体信息
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"getapplicationlog","params":["交易TxId"]}'  
```
* 第2步中`type=InvocationTransaction`的交易在第3部中取详细信息
* 第3步中注意验证`vmstate`字段的状态，防止错误入账

### 对外提币：
* 同 NEO - NEO

### 归集处理：
* 无需归集

### 灾难恢复：
* 同 NEO - NEO

### 注意事项：
