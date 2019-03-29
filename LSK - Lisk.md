# Lisk - LSK

### 官网地址：
https://lisk.io

### 钱包下载：
https://lisk.io/documentation/lisk-core/setup

### 查询网站：
https://explorer.lisk.io

### 开发文档
https://lisk.io/documentation/lisk-core/user-guide/api

### 安装说明：
https://lisk.io/documentation/lisk-core/setup

### 配置文件：
https://lisk.io/documentation/lisk-core/user-guide/configuration

### 升级说明：
https://lisk.io/documentation/lisk-core/upgrade

### 创建地址：
没找到创建地址的API，所以只能通过console来创建
```
lisk account:create
```

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8000/api/node/status \
```
2、根据区块高度获取叫一列表
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8000/api/transactions?height=高度&limit=单页最大条数
```
3、根据TxId获取具体信息
```
curl -X POST -H 'content-type: application/json' http://127.0.0.1:8000/api/transactions?id=交易TxId
```

### 对外提币：
在API里没有Account相关的一切操作，所以打币只能通过控制台进行  
1、创建交易
```
lisk transaction:create:transfer 数量 接收方地址 --passphrase="pass:发送地址的助记词"
```
2、广播交易
```
lisk transaction:broadcast 第1步的输出结果
```

### 归集处理：
* Lisk的交易是1对1的所以需要归集，归集与对外打币一样提到一个归集地址即可

### 灾难恢复：
* 每个账号创建时都有私钥和助记词，保存好这些即可

### 注意事项：
