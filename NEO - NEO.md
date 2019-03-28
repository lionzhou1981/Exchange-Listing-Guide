# NEO - BNEO

### 官网地址：
https://neo.org/

### 钱包下载：
https://github.com/neo-project/neo-cli/releases

### 查询网站：
https://neotracker.io/

### 开发文档
https://docs.neo.org/

### 安装说明：
1、解压缩文件
```
unzip neo-cli-linux-x64.zip
mv neo-cli-linux-x64 neo
```

2、启动节点
```
dotnet neo-cli.dll --rpc --log
```
* 建议使用 screen 来运行，因为neo-cli是可以做一些交互查询的
* --rpc 是开启rpc端口的参数
* --log 是开启ApplicationLog插件的参数，此参数是NEP5 Token必须的

### 配置文件：
```
vi /程序目录/config.json
```
```
{
  "ApplicationConfiguration": {
    "Paths": {
      "Chain": "/数据目录/Chain_{0}",
      "Index": "/数据目录/Index_{0}",
      "ApplicationLogs": "/数据目录/ApplicationLogs_{0}"
    },
    "P2P": {
      "Port": 10333,
      "WsPort": 10334
    },
    "RPC": {
      "BindAddress": "127.0.0.1",
      "Port": 10332,
      "SslCert": "",
      "SslCertPassword": ""
    },
    "UnlockWallet": {
      "Path": "/钱包目录/钱包.json",
      "Password": "钱包密码",
      "StartConsensus": false,
      "IsActive": true
    },
    "PluginURL": "https://github.com/neo-project/neo-plugins/releases/download/v{1}/{0}.zip"
  }
}
```
```
vi /程序目录/Plugins/ApplicationLogs/config.json
```
```
{
  "PluginConfiguration": {
    "Path": "/数据目录/ApplicationLogs_{0}"
  }
}
```

### 升级说明：
1、下载最新的版本  
2、重命名旧版本的程序目录以做备份  
3、解压缩新的压缩包并改名为neo
4、复制config.json文件、Plugins目录至新版neo程序目录  
5、重启节点程序

### 创建地址：
1、创建地址
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"getnewaddress","params":[]}'  
```
2、获取私钥
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"dumpprivkey","params":["地址"]}'
```

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
-d '{"jsonrpc":"2.0","id":"随便写","method":"getrawtransaction","params":["交易TxId",1]}'  
```
* 入账需验证Asset字段
* NEO：c56f33fc6ecfcd0c225c4ab356fee59390af8560be0e930faebe74a6daff7c9b
* GAS：602c79718b16e442de58778e148d0b1084e3b2dffd5de6b7b16cee7969282de7

### 对外提币：
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:10332/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"sendmany","params":[{"asset":"币种Hex","value":提币金额,"address":"收款地址"},0,"找零地址"]}'  
```
* 由于NEO不提供RAW相关的接口（只有sendraw，没有create和sign）只能使用此方法进行
* 带来的问题是如果节点发生灾难必须花时间将所有私钥导回节点
* 同时从安全性上来说私钥无法独立保存，也会降低安全性
* 这里给个差评

### 归集处理：
* 无需归集

### 灾难恢复：
* 用户私钥加密后另外存储，以做备份，灾难发生时重建节点即可

### 注意事项：
