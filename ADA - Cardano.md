# Cardano - ADA

### 文档版本：1.14.2
检查日期: 2020.07.15

### 官网地址：
https://www.cardano.org/

### 钱包下载：
https://github.com/input-output-hk/cardano-node

### 查询网站：
https://cardanoexplorer.com/

### 开发文档
https://cardanodocs.com
https://input-output-hk.github.io/cardano-wallet/api/edge/

### 安装说明：
1、安装可参照此文档
```
https://docs.cardano.org/projects/cardano-node/en/latest/getting-started/install.html
```
* 如发生错误：I've had most trouble fulfilling: cardano-crypto-class
```
git clone https://github.com/input-output-hk/libsodium
cd libsodium
git checkout 66f017f1
./autogen.sh
./configure
make
sudo make install

export LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH"
export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig
```

### 配置文件：
```
https://hydra.iohk.io/build/3462018/download/1/index.html
```

### 升级说明：
1、下载新版代码  
2、重新编译  
3、检查配置  
3、重新开启节点

### 创建地址：
1、的钱包规则是 `Wallet` -> `Address`，交易所只需要一个`Wallet`即可，首先是创建钱包
```
curl -X POST http://127.0.0.1:8090/v2/byron-wallets \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{ "style": "random", "name": "钱包本地名称", "passphrase": "支付密码","mnemonic_sentence": [12个助记词]}'
```
2、最后创建`Address`
```
curl -X POST http://127.0.0.1:8090/v2/byron-wallets/{第一步创建的WalletId}/addresses \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"passphrase":"第一步设置的支付密码"}'
```
* 助记词可通过以下网站生成
```
https://www.liaoxuefeng.com/wiki/0015223693709562f80977e6c9549f0a1e17640a61433d6000/0015223800842062cc09cdd70dc45b8992c3b399386673a000
```

### 追踪入账：
1、交易列表可以根据时间断来查询，由于不支持通过block查询，所以可以用上一笔交易的时间作为查询的start
```
curl -X GET http://127.0.0.1:8090/v2/byron-wallets/{walletId}/transactions?start={起始查询时间} \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
```  
2、获取交易详细
```
curl -X GET http://127.0.0.1:8090/v2/byron-wallets/{walletId}/transactions/交易TxId \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
```
* 注意现在的交易单位是lovelace，`1 lovelace = 0.0000001 ada`
* 注意验证交易的`status`为`in_ledger`，文档说`in_ledger`还可能rollback，保险起见注意实际入账需要延迟N个block
* 注意如果input和output的地址都是本地的话会产生`status`是`wontApply`的交易

### 对外提币：
```
curl -X POST http://127.0.0.1:8090/api/v1/transactions \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"payments":[{"address":"目标地址","amount":{"quantity":提币金额,"unit":"lovelace"}}],"passphrase":"支付密码"}'
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 新版改进后灾备简单了，先用12个助记词恢复wallet后直接用Import Address把所有地址都执行一遍即可。  

### 注意事项：
