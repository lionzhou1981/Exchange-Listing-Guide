# Cardano - ADA

### 文档版本：1.18.0
检查日期: 2020.07.29
* 注意此版本需要更新配置文件

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
https://hydra.iohk.io/job/Cardano/cardano-node/cardano-deployment/latest-finished/download/1/index.html
```

### 升级说明：
1、下载新版代码  
2、重新编译  
3、检查配置  
3、重新开启节点

### 创建地址：
1、创建助记词
```
cardano-address recovery-phrase generate --size XX
XX是助记词的长度，可以是15、18、21、24
```
2、将助记词创建入wallet
```
cardano-wallet wallet create from-recovery-phrase 设置钱包名称 --address-pool-gap 100
输入助记词 -> 二级助记词（可以不设置） -> 设置一个钱包密码
返回中会有钱包的ID
```
3、等待钱包同步完成，可以通过以下命令查看同步状态
```
cardano-wallet wallet get 钱包ID
```
* 1.18.0版没有创建地址的命令，真不知道设计的人脑子怎么想的。
* 解决方案是，每次获取unused的地址，然后往地址上打一笔最小金额的钱
* 这样地址就会被用掉了，wallet会自动生成新的unused的地址

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
curl -X POST http://127.0.0.1:8090/v2/byron-wallets/{walletId}/transactions \
  -H "Accept: application/json; charset=utf-8" \
  -H "Content-Type: application/json; charset=utf-8" \
-d '{"payments":[{"address":"目标地址","amount":{"quantity":提币金额,"unit":"lovelace"}}],"passphrase":"支付密码"}'
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 新版改进后灾备简单了，先用12个助记词恢复wallet后直接用Import Address把所有地址都执行一遍即可。  

### 注意事项：
