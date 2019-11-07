# Stellar - XLM

### 文档版本：12.1.0
检查日期: 2019.11.04

### 官网地址：
https://www.stellar.org/

### 钱包下载：
https://github.com/stellar/packages/tree/master/docs

### 查询网站：
https://dashboard.stellar.org/
https://stellarbeat.io/
https://stellar.expert/explorer/public/

### 开发文档
https://www.stellar.org/developers/

### 安装说明：
1、准备Ubuntu的系统，服务器版本可以是18.04 (文档建议16.04，是文档已经落后了)
2、根据以下链接中的说明，将源添加入系统
```
https://github.com/stellar/packages/blob/master/docs/adding-the-sdf-stable-repository-to-your-system.md
```
3、执行以下命令安装程序
```
sudo apt-get install stellar-core
sudo apt-get install stellar-core-utils
sudo apt-get install stellar-core-postgres
sudo apt-get install stellar-horizon
sudo apt-get install stellar-horizon-utils
sudo apt-get install stellar-horizon-postgres
```

### 配置文件：
1、首先为Postgres的用户postgres建一个密码
```
sudo -u postgres psql
ALTER USER postgres WITH PASSWORD 'postgres';
\q
```
2、为core和horizon创建数据库
```
createdb core_db
createdb horizon_db
```
3、为core创建配置文件
```
根据样例文件进行修改：
https://github.com/stellar/stellar-core/blob/master/docs/stellar-core_example.cfg

LOG_FILE_PATH="日志文件"
BUCKET_DIR_PATH="Bucket文件路径"
DATABASE="postgresql://dbname=core_db user=postgres password=postgres host=127.0.0.1"
NODE_NAMES=[
"GBJQUIXUO4XSNPAUT6ODLZUJRV2NPXYASKUBY4G5MYP3M47PCVI55MNT  satoshipay",
"GAZ437J46SCFPZEDLVGDMKZPLFO77XJ4QVAURSJVRZK2T5S7XUFHXI2Z  coinqvest",
"GAOXP7T6F44Q2F5EBWQEVHPQPOSLQO45IM44IRLKHRDCJZX66B6Y4VAI  blockchain",
"GCWJKM4EGTGJUVSWUJDPCQEOEP5LHSOFKSA4HALBTOO4T4H3HCHOM6UX  keybase",
"GBJ7T3BTLX2BP3T5Q4256PUF7JMDAB35LLO32QRDYE67TDDMN7H33GGE  ibmhk"
]
NETWORK_PASSPHRASE="Public Global Stellar Network ; September 2015"
CATCHUP_COMPLETE=false
CATCHUP_RECENT=100000           #本地保存的区块数量

KNOWN_PEERS=[
"core-live-a.stellar.org:11625",
"core-live-b.stellar.org:11625",
"core-live-c.stellar.org:11625",
"stellar.256kw.com",
"stellar1.tempo.eu.com",
"stellar.satoshipay.io"
]

[[HOME_DOMAINS]]
HOME_DOMAIN="www.stellar.org"
QUALITY="HIGH"

[[VALIDATORS]]
NAME="sdflive1"
HOME_DOMAIN="www.stellar.org"
PUBLIC_KEY="GCGB2S2KGYARPVIA37HYZXVRM2YZUEXA6S33ZU5BUDC6THSB62LZSTYH"
ADDRESS="core-live-a.stellar.org"
HISTORY="curl -sf http://history.stellar.org/prd/core-live/core_live_001/{0} -o {1}"
[[VALIDATORS]]
NAME="sdflive2"
HOME_DOMAIN="www.stellar.org"
PUBLIC_KEY="GCM6QMP3DLRPTAZW2UZPCPX2LF3SXWXKPMP3GKFZBDSF3QZGV2G5QSTK"
ADDRESS="core-live-b.stellar.org"
HISTORY="curl -sf http://history.stellar.org/prd/core-live/core_live_002/{0} -o {1}"
[[VALIDATORS]]
NAME="sdflive3"
HOME_DOMAIN="www.stellar.org"
PUBLIC_KEY="GABMKJM6I25XI4K7U6XWMULOUQIQ27BCTMLS6BYYSOWKTBUXVRJSXHYQ"
ADDRESS="core-live-c.stellar.org"
HISTORY="curl -sf http://history.stellar.org/prd/core-live/core_live_003/{0} -o {1}"
[HISTORY.local]         #此处根据实际情况修改目录
get="cp /tmp/stellar/history/{0} {1}"
put="cp {0} /tmp/stellar/history/{1}"
mkdir="mkdir -p /tmp/stellar/history/{0}"

[HISTORY.sdf1]
get="curl -sf http://history.stellar.org/prd/core-live/core_live_001/{0} -o {1}"
[HISTORY.sdf2]
get="curl -sf http://history.stellar.org/prd/core-live/core_live_002/{0} -o {1}"
[HISTORY.sdf3]
get="curl -sf http://history.stellar.org/prd/core-live/core_live_003/{0} -o {1}"
```
4、stellar-core初始化
```
stellar-core --conf /etc/stellar-core.cfg new-db
stellar-core --conf /etc/stellar-core.cfg new-hist local
```
5、stellar-core启动
```
stellar-core --conf /data/xlm-data/stellar-core.cfg run
```
6、stellar-horizon初始化
```
#初始化数据库
stellar-horizon \
--db-url "postgres://postgres:postgres@localhost/horizon_db?sslmode=disable" \
--stellar-core-db-url "postgres://postgres:postgres@localhost/core_db?sslmode=disable" \
--stellar-core-url http://localhost:11626 \
--network-passphrase "Public Global Stellar Network ; September 2015" \
db init

#从Core取回若干个区块数据
stellar-horizon \
--db-url "postgres://postgres:postgres@localhost/horizon_db?sslmode=disable" \
--stellar-core-db-url "postgres://postgres:postgres@localhost/core_db?sslmode=disable" \
--stellar-core-url http://localhost:11626 \
--network-passphrase "Public Global Stellar Network ; September 2015" \
db backfill 50000
```
7、stellar-horizon启动
```
stellar-horizon \
--db-url "postgres://postgres:postgres@localhost/horizon_db?sslmode=disable" \
--stellar-core-db-url "postgres://postgres:postgres@localhost/core_db?sslmode=disable" \
--stellar-core-url http://localhost:11626 \
--network-passphrase "Public Global Stellar Network ; September 2015" \
--ingest \
--history-retention-count 50000 \
--history-stale-threshold 100000 \
--per-hour-rate-limit 36000 \
serve
```

### 升级说明：
升级可详见以下链接：
```
https://github.com/stellar/packages/blob/master/docs/upgrading.md
```

### 创建地址：
1、通过命令行创建唯一的地址
```
stellar-core gen-seed
```
2、往地址上打入20XLM用来激活  
3、交易所用户的地址使用TAG来进行区分，所以整个交易所只需要一个地址即可

### 追踪入账：
1、获取最新的区块高度
```
curl -X GET 'http://127.0.0.1:8000/ledgers?limit=1&order=desc'
```
2、根据区块高度获取叫一列表
```
curl -X GET 'http://127.0.0.1:8000/ledgers/区块高度/payments?limit=200&order=asc'

* 验证以下数据
asset_type = 'native'           # 验证是否原生XLM币种
type = 'payment'                # 验证交易类型
transaction_successful = true   # 验证交易是否成功

* 如果一个区块的交易超过200个，需要通过cursor来循环获取
```
3、根据TxId获取具体信息
```
curl -X GET 'http://127.0.0.1:8000/transactions/交易的Hash'
```

### 对外提币：
1、由于Horizon不包含sign功能，建议引用官方的SDK来封装一个专门签名的独立程序
```
官方SDK：
Python版    : https://github.com/StellarCN/py-stellar-base
.Net版      : https://github.com/elucidsoft/dotnet-stellar-sdk
Ruby版      : https://github.com/stellar/ruby-stellar-sdk
iOS & Mac版 : https://github.com/Soneso/stellar-ios-mac-sdk
Scala版     : https://github.com/Synesso/scala-stellar-sdk
C++版       : https://github.com/bnogalm/StellarQtSDK
```
2、提交交易
```
curl -X POST 'http://127.0.0.1:8000/transactions' \
-d 'tx=签名后的交易数据'  
```

### 归集处理：
* 无需归集

### 灾难恢复：
* 唯一地址的私钥备份好即可

### 注意事项：
