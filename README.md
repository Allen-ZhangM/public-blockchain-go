# 测试过程
构建文件
```
windows:
go build -o bc.exe main.go
linux:
go build -o bc main.go
```
查看Usage
```
>bc.exe
Usage:
        createwallet -- 创建钱包
        getaddresslists -- 获取所有的钱包地址
        createblockchain -address DATA -- 创建创世区块
        send -from From -to To -amount Amount -- 转账交易
        printchain -- 打印区块
        getbalance -address Data -- 查询余额
        test -- 重置
        startnode -miner Address -- 启动节点，并指定挖矿的奖励地址
```
## 打开 *第一个* 终端进入到项目目录中：
**此节点作为全节点，设置节点id，模拟多台计算机**
```
windows:
set NODE_ID=3000
linux:
export NODE_ID=3000
```
创建钱包
```
>bc.exe createwallet
nodeID: 3000
钱包文件不存在。。
创建的钱包地址：17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
钱包： map[17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD:0xc0420516c0]
```
创建创世区块
```
>bc.exe createblockchain -address 17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
nodeID: 3000
数据库不存在。。
正在创建创世区块。。。。。
7609,000059822ea0bb191f126aeb7f4be80549d3abc83031c0e8810898488fce9e16
```
查询余额，挖矿奖励10个币
```
>bc.exe getbalance -address 17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
nodeID: 3000
17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD,余额是：10
```
查询区块
```
>bc.exe printchain
nodeID: 3000
第1个区块的信息：
        高度：0
        上一个区块Hash：0000000000000000000000000000000000000000000000000000000000000000
        自己的Hash：000059822ea0bb191f126aeb7f4be80549d3abc83031c0e8810898488fce9e16
        交易信息：
                交易ID：28b21d4cb897951965f45c00d72baab4669c82743c172cb664dd9e0e55e46b9f
                Vins:
                        TxID:
                        Vout:-1
                        sign:[]
                        PublicKey:[]
                Vouts:
                        Value:10
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
        随机数：7609
        时间：2018-11-12 14:59:58

```
## 打开 *第二个* 终端进入到项目目录中：
**此节点作为钱包节点，设置节点id，模拟多台计算机**
- 设置节点id为3001
- 手动设置创世区块节点信息，即拷贝一份blockchain_3000.db为blockchain_3001.db。目的是为了让两个节点在同一区块链上，用同一个创世区块
- 创建两个钱包地址
```
全节点：
钱包0：17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
钱包节点：
钱包1：1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx
钱包2：19KAejabvA3hctd95CAiioLrDVyQzykosT
```
## 执行转账操作

转账逻辑
```
切换到全节点中：
	转账：
		地址0,转账给地址1：5
		地址0,转账给地址2：8

	blockchain_3000.db
		block0,block1,block2

	查询余额：
		地址0：17
		地址1：5
		地址2：8
```
转账操作
```
>bc.exe send -from [\"17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD\"] -to [\"1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx\"] -amount [\"5\"]
nodeID: 3000
137691,000020a4ce60abeb39fc1f47761daa6ccab39194df0bfff730704efbc2b7a1a5

>bc.exe send -from [\"17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD\"] -to [\"19KAejabvA3hctd95CAiioLrDVyQzykosT\"] -amount [\"8\"]
nodeID: 3000
74286,0000499f2676b5aae9ff3be1691192bb0a8d13f9d34898bf5ba2155cb13f7f3e
//注意转义问题，上述写法为windows环境，linux环境写法为：
$./bc send -from '["17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD"]' -to '["1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx"]' -amount '["5"]'

```
查询余额
```
>bc.exe getbalance -address 17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
nodeID: 3000
17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD,余额是：17
>bc.exe getbalance -address 1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx
nodeID: 3000
1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx,余额是：5
>bc.exe getbalance -address 19KAejabvA3hctd95CAiioLrDVyQzykosT
nodeID: 3000
19KAejabvA3hctd95CAiioLrDVyQzykosT,余额是：8

```
查询区块
```
>bc.exe printchain
nodeID: 3000
第3个区块的信息：
        高度：2
        上一个区块Hash：000020a4ce60abeb39fc1f47761daa6ccab39194df0bfff730704efbc2b7a1a5
        自己的Hash：0000499f2676b5aae9ff3be1691192bb0a8d13f9d34898bf5ba2155cb13f7f3e
        交易信息：
                交易ID：1a7868d7926d94ae30256c7d7429912b98b1ca434a83cb0f3f193c274df7b363
                Vins:
                        TxID:ec387ad7df7dd0c9c561e7f4f3ef324f22be6a99b84199e199d65a16243b86d2
                        Vout:0
                        sign:[121 43 177 55 204 218 88 126 31 119 97 184 94 139 254 174 245 145 247 243 143 45 157 106 243 139 105 3 51 16 105 159 46 12 127 16 2
27 118 48 43 158 134 139 214 216 223 228 71 198 63 53 17 173 67 56 113 0 189 101 11 229 63 213 154]
                        PublicKey:[109 37 105 197 239 135 211 189 149 162 147 110 191 88 80 46 87 27 252 70 209 132 241 134 212 189 251 250 120 103 255 23 50 221
 79 76 183 8 130 40 216 210 206 169 236 185 167 29 147 97 36 142 73 8 124 229 10 89 153 233 172 175 225 200]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
                        TxID:a081102bd214e81a292c16cfa57e05d6a4263f5ff37245fcb72532ca76c9727c
                        Vout:1
                        sign:[75 50 96 28 182 196 242 7 86 32 201 95 148 18 89 119 220 197 149 66 6 39 124 119 1 179 33 119 248 11 122 112 200 30 20 205 224 205
218 91 208 0 251 172 113 235 105 84 250 0 203 151 154 240 157 58 200 48 2 210 19 249 148 237]
                        PublicKey:[109 37 105 197 239 135 211 189 149 162 147 110 191 88 80 46 87 27 252 70 209 132 241 134 212 189 251 250 120 103 255 23 50 221
 79 76 183 8 130 40 216 210 206 169 236 185 167 29 147 97 36 142 73 8 124 229 10 89 153 233 172 175 225 200]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
                Vouts:
                        Value:8
                        PubKeyHash:[91 48 140 171 61 148 214 108 37 27 255 122 103 153 13 115 231 127 202 24]
                        address:19KAejabvA3hctd95CAiioLrDVyQzykosT
                        Value:7
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
                交易ID：def74d145cac539c74d1b0da42d01cdb6958f669e5187f6cedcdb9b6f6ec178b
                Vins:
                        TxID:
                        Vout:-1
                        sign:[]
                        PublicKey:[]
                Vouts:
                        Value:10
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
        随机数：74286
        时间：2018-11-12 16:41:32
第2个区块的信息：
        高度：1
        上一个区块Hash：000059822ea0bb191f126aeb7f4be80549d3abc83031c0e8810898488fce9e16
        自己的Hash：000020a4ce60abeb39fc1f47761daa6ccab39194df0bfff730704efbc2b7a1a5
        交易信息：
                交易ID：a081102bd214e81a292c16cfa57e05d6a4263f5ff37245fcb72532ca76c9727c
                Vins:
                        TxID:28b21d4cb897951965f45c00d72baab4669c82743c172cb664dd9e0e55e46b9f
                        Vout:0
                        sign:[247 122 188 8 65 242 10 69 8 198 193 124 182 157 194 122 90 134 25 83 24 235 226 246 112 188 55 221 4 184 23 168 108 209 7 99 107 8
8 218 40 200 204 237 149 13 29 184 243 60 244 51 56 85 246 154 25 205 245 12 107 41 181 88 157]
                        PublicKey:[109 37 105 197 239 135 211 189 149 162 147 110 191 88 80 46 87 27 252 70 209 132 241 134 212 189 251 250 120 103 255 23 50 221
 79 76 183 8 130 40 216 210 206 169 236 185 167 29 147 97 36 142 73 8 124 229 10 89 153 233 172 175 225 200]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
                Vouts:
                        Value:5
                        PubKeyHash:[159 235 149 122 169 198 155 123 17 61 212 195 109 143 94 223 210 23 14 113]
                        address:1FaafA95m8vGoFPj5n2aeSpUoXn2Lk8Wpx
                        Value:5
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
                交易ID：ec387ad7df7dd0c9c561e7f4f3ef324f22be6a99b84199e199d65a16243b86d2
                Vins:
                        TxID:
                        Vout:-1
                        sign:[]
                        PublicKey:[]
                Vouts:
                        Value:10
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
        随机数：137691
        时间：2018-11-12 16:39:07
第1个区块的信息：
        高度：0
        上一个区块Hash：0000000000000000000000000000000000000000000000000000000000000000
        自己的Hash：000059822ea0bb191f126aeb7f4be80549d3abc83031c0e8810898488fce9e16
        交易信息：
                交易ID：28b21d4cb897951965f45c00d72baab4669c82743c172cb664dd9e0e55e46b9f
                Vins:
                        TxID:
                        Vout:-1
                        sign:[]
                        PublicKey:[]
                Vouts:
                        Value:10
                        PubKeyHash:[68 154 70 159 40 109 172 192 152 144 188 40 252 159 111 118 22 208 177 58]
                        address:17FjmKY1iNzEnMAzC2r6jPgWXQeTRVMjmD
        随机数：7609
        时间：2018-11-12 14:59:58
```
**此时钱包节点的信息：**
```
	>bc.exe printchain
		只有创世区块
	查询余额：
		地址0：10
		地址1：0
		地址2：0
```
## 节点同步
```
//切换到全节点并执行
>bc.exe startnode
//再切换到钱包节点并执行
>bc.exe startnode
```
可以看到同步信息，并且钱包节点再次查看的余额、区块等信息与全节点一致。