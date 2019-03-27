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
mv bitcoin-0.17.1-x86_64-linux-gnu bitcoin
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
rpcport=8332
rpcuser=                    #RPC用户名
rpcauth=                    #RPC认证信息
rpcallowip=                 #允许访问的IP
```

### 升级说明：
1、下载最新的版本
2、重命名旧版本的程序目录以做备份
3、解压缩新的压缩包并改名为bitcoin
4、重启服务
```
systemctl restart bitcoin
```
5、观察debug.log是否正常
```
tail -f /数据目录/debug.log
```

### 创建地址：
```
curl -X POST -H 'content-type: text/plain;' http://127.0.0.1:8332/ -u RPC用户:RPC密码 -d '{"jsonrpc":"1.0","id":"curltest","method":"getnewaddress","params":[]}'  
```

### 追踪入账：

### 对外提币：

### 归集处理：

### 灾难恢复：

