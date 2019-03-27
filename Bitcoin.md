# Bitcoin - BTC

### 官网地址：
https://bitcoin.org/

### 钱包下载：
https://bitcoincore.org/en/download/

### 查询网站：
https://btc.com/

### 开发文档
https://bitcoincore.org/en/doc/0.17.0/

### 安装说明：
1、解压缩文件
```
tar -xvf bitcoin-0.17.1-x86_64-linux-gnu.tar.gz
```

2、创建服务
```
vi /usr/lib/systemd/system/bitcoin.service
```
```
[Unit]
Description=Bitcoin

[Service]
Type=forking

ExecStart=/安装目录/bin/bitcoind -daemon -conf=/数据目录/bitcoin.conf
ExecStop=/安装目录/bin/bitcoin-cli -stop -conf=/数据目录/bitcoin.conf

[Install]
WantedBy=multi-user.target
```

### 配置文件：
```
vi /数据目录/bitcoin.conf
```
```
bind=127.0.0.1              #绑定端口，建议127.0.0.1
server=1                    
txindex=1
datadir=/btc-data           #数据存储目录

rpcbind=127.0.0.1           #RPC端口，建议127.0.0.1
rpcport=12345
rpcuser=                    #RPC用户名
rpcauth=                    #RPC认证信息
rpcallowip=                 #允许访问的IP
```

### 升级说明：

### 创建地址：

### 追踪入账：

### 对外提币：

### 归集处理：

### 灾难恢复：

