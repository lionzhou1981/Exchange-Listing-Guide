# Cardano - ADA

### 文档版本：3.0.1

### 官网地址：
https://www.cardano.org/

### 钱包下载：
https://github.com/input-output-hk/cardano-sl

### 查询网站：
https://cardanoexplorer.com/

### 开发文档
https://cardanodocs.com

### 安装说明：
1、安装可参照此文档
```
https://github.com/input-output-hk/cardano-sl/blob/master/docs/how-to/build-cardano-sl-and-daedalus-from-source-code.md
```
* 在安装好nix后不要忘记查看一下文档中的Source设定，否则会编译不通过
```
https://github.com/input-output-hk/cardano-sl/blob/master/docs/nix.md
```
* 编译时间很长，请预先使用`screen`等分屏工具，以防掉线、超时等异常退出导致中断

### 配置文件：
配置以参数的形式输入启动程序，编译完成后在目录下查找`connect-to-mainnet`的启动脚本文件，参数参考如下：
```
https://cardanodocs.com/technical/cli-options/
```

### 升级说明：
1、下载新版代码  
2、重新编译  
3、检查配置  
3、重新开启节点

### 创建地址：
1、ADA的钱包规则是 `WalletId` -> `Account` -> `Address` 三级交易所只需要一个`WalletId`即可，首先是创建钱包
```
curl -X POST http://127.0.0.1:8090/api/v1/wallets \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{ "operation": "create", "assuranceLevel": "normal", "name": "钱包本地名称", "spendingPassword": "支付密码","backupPhrase": [12个助记词]}'
```
2、然后创建`Account`
```
curl -X POST http://127.0.0.1:8090/api/v1/wallets/第一步创建的WalletId/accounts \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"name":"Account名称","spendingPassword":"第一步设置的支付密码"}'
```
3、最后创建`Address`
```
curl -X POST http://127.0.0.1:8090/api/v1/addresses \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"walletId":"第一步创建的WalletId","accountIndex":第二步获得的AccountIndex,"spendingPassword":"第一步设置的支付密码"}'
```
* 助记词可通过以下网站生成
```
https://www.liaoxuefeng.com/wiki/0015223693709562f80977e6c9549f0a1e17640a61433d6000/0015223800842062cc09cdd70dc45b8992c3b399386673a000
```

### 追踪入账：
1、ADA是通过的WalletId的页码来进行追踪入账的  
```
curl -X GET http://127.0.0.1:8090/api/v1/transactions?wallet_id=WalletId&per_page=每页行数&sort_by=ASC[created_at]&page=页码 \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
```  
2、获取交易详细  
```
curl -X GET http://127.0.0.1:8090/api/v1/transactions?wallet_id=WalletId&id=交易TxId \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
```
* 注意如果input和output的地址都是本地的话会产生`status`是`wontApply`的交易

### 对外提币：
1、获取最新的区块高度
```
curl -X POST http://127.0.0.1:8090/api/v1/transactions \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"destinations":[{"address":"目标地址","amount":提币金额}],"source":{"walletId":"本地钱包Id","accountIndex":本地Account序号},"spendingPassword":"支付密码"}'
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 记住WalletId和助记词即可，然后再恢复的节点上使用`restore`和`--flush-wallet-db`进行启动  
* 特别注意：`flush-wallet-db`只会找出有过交易的地址，无交易的地址不会被恢复进新的节点  
* 解决方案：在恢复前手工往所有未有交易的地址上交易一笔最低金额，这样有了历史交易再进行`--flush-wallet-db`就会被恢复出来

### 注意事项：
