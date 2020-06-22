# Ethereum ERC20 Token - ERC20

### 文档版本：1.9.15
检查日期: 2020.06.21

### 官网地址：
https://www.ethereum.org/

### 钱包下载：
https://geth.ethereum.org/downloads

### 查询网站：
https://etherscan.io/

### 开发文档
https://github.com/ethereum/go-ethereum/wiki

### 安装说明：
* 同 ETH - Ethereum

### 配置文件：
* 同 ETH - Ethereum

### 升级说明：
* 同 ETH - Ethereum

### 创建地址：
* 同 ETH - Ethereum

### 追踪入账：
1、获取最新的区块高度
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_blockNumber","params":[]}'  
```
2、根据区块Hash获取交易列表
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getBlockByNumber","params":["0x区块高度数字的HEX"]}'  
```
4、根据TxId获取具体信息
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getTransactionByHash","params":["交易TxId"]}'  
```
5、根据TxId获取具体收据
```
curl -X POST -H 'content-type:application/json' http://127.0.0.1:8333/ \
-d '{"jsonrpc":"2.0","id":"随便写","method":"eth_getTransactionReceipt","params":["交易TxId"]}'  

* 验证收据的 `result/status` 的值为 0x1，交易成功的标志
```
3、辨别TOKEN的交易
```
a、核对transaction中的to的合约地址是否是本地的Token
b、获取transactions中的input的Hex，取其中的前5个byte匹配合约类型`Transfer`，其他类型如下
TotalSupply  ： 0x18160ddd
BalanceOf    ： 0x70a08231
Transfer     ： 0xa9059cbb
TransferFrom ： 0x23b872dd
Approve      ： 0x095ea7b3
Allowance    ： 0xdd62ed3e
c、取Hex的第6字节开始的32个字节，然后取此32个字节中的后20个字节为首款地址
d、取Hex的第75个字节开始的64个字节，转换成数字后是转账金额，注意需要根据合约的小数点位进行换算
```

### 对外提币：
1、ERC20同样适用ETH的eth_sendTransaction方法提交  
2、`to`的值填入Token的合约地址  
3、`value`的值填入`0x0`  
4、`data`格式如下：
```
0xa9059cbb + 收款地址填充到32字节(前方填充0x0) + 转账数量(数量先乘以 10^小数位数 ，然后转换成16进制)
```

### 归集处理：
1、首先需要往用户的收款地址打入一小币Gas的ETH作为归集的费用  
2、将Token转账到归集地址

* 以下提供一个批量对用户地址进行充值Gas的合约代码
```
pragma solidity ^0.4.0;

contract MultiSend{
    function Send2Many(address[] _to, uint256[] _value) public payable returns (bool _success) {
		assert(_to.length == _value.length);
		assert(_to.length <= 255);
		uint256 beforeValue = msg.value;
		uint256 afterValue = 0;
		for (uint8 i = 0; i < _to.length; i++) {
			afterValue = afterValue + _value[i];
			assert(_to[i].send(_value[i]));
		}
		uint256 remainingValue = beforeValue - afterValue;
		if (remainingValue > 0) {
			assert(msg.sender.send(remainingValue));
		}
		return true;
	}
}
```

### 灾难恢复：
* 同 ETH - Ethereum

### 注意事项：
