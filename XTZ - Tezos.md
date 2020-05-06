# Tezos - XTZ

### 文档版本：2020-01
检查日期: 2020.05.05

### 官网地址：
https://www.tezos.com/

### 钱包下载：
https://github.com/tezoscommunity/FAQ/blob/master/Compile_Mainnet.md

### 查询网站：
https://tezos.id/ <br>
https://arronax.io/

### 开发文档
https://developers.tezos.com/  
http://tezos.gitlab.io/

### 安装说明：
根据官网访问文档安装没有问题
```
https://github.com/tezoscommunity/FAQ/blob/master/Compile_Mainnet.md
```

### 配置文件：
* 首次同步较慢，可以通过这里 `https://tezosshots.com/` 下载快照数据
* 快照数据用mega存储，mega有一个megacmd，可以在Linux运行
* `./tezos-node identity generate 26.` 生成identity，数字不可低于26，数字越大时间越长
* `./tezos-node run --rpc-addr 127.0.0.1:8732 --data-dir=/xxx` 运行节点，可以指定数据目录
* `./tezos-client bootstrapped` 通过此命令可以查看同步进度

### 升级说明：
由于是源码编译的，所以需要更新源码，然后可根据安装文档下的Rebuild来进行升级

### 创建地址：
由于在 `tezos-client` 或 `rpc` 中均为找到地址生成的功能，所以请引用或者参考三方库来做地址的生成：
```
https://developers.tezos.com/
Getting Started -> 3 Use a Library -> 选择一个符合的语言
```

### 追踪入账：
1、获取最新的区块高度
```
curl -X GET -H 'content-type: application/json' http://127.0.0.1:8732/chains/main/blocks/head 
```
2、根据区块Hash获取交易列表
```
curl -X GET -H 'content-type: application/json' http://127.0.0.1:8732/chains/main/blocks/940067 

注：由于没有获取单独交易的方法，所以所有的交易和验证都在获取区块的列表中完成

注意验证以下内容：
oprations/protocol/contentes/kind = "transaction"
oprations/protocol/contentes/amount > 0
oprations/protocol/contentes/metadatas 是转账明细，可根据需要验证
```

### 对外提币：
1、导入地址
```
tezos-client import secret key 归集地址 unencrypted:私钥 --force
```
2、发送交易
```
tezos-client transfer 交易金额 from 归集地址 to 目标地址

注：如果输出错误说要补充burn-cap，并且会给出金额，则重新执行以下命令
tezos-client transfer 交易金额 from 归集地址 to 目标地址 --burn-cap 需要烧掉的带宽金额
```
3、等待执行完成并获取到上链后的区块Hash，以备后期使用   
4、删除本地导入的地址（如果需要，这样可以保持节点上不存私钥）
```
tezos-client forget address 归集地址 --force
```

### 归集处理：
* 从协议和区块的数据来看Tezos应该支持多对多的交易，可以免于归集，但由于文档不明和操作复杂所以还是选择需要归集比较稳妥

### 灾难恢复：
* 地址由三方类库生成时备份地址的私钥即可恢复

### 注意事项：
