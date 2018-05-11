# 命令参考

## 命令参考

|操作|语法|例子|
|---|---|---|
|获取所有命令|$ cleos|[例子](#全部命令)|
|获取所有子命令|$ cleos ${command}|[例子](#所有子命令)|
|链接节点|$ cleos --url ${node}:${port}|[例子](#链接节点)|
|查询区块链状态|$ cleos get info|[例子](#查询区块链状态)|
|通过transaction_id获取交易|$ cleos get transaction ${transaction_id}|[例子](#通过transaction_id获取交易)|
|通过帐户获取交易|$ cleos get transaction ${account}|[例子](#通过帐户获取交易)|
|转账EOS|$ cleos transfer ${from_account} ${to_account} ${quantity}|[例子](#转账EOS)|
|钱包 - 创建钱包|$ cleos wallet create {-n} ${wallet_name}|[例子](#创建钱包)|
|钱包 - 钱包列表|$ cleos wallet list|[例子](#钱包列表)|
|钱包 - 导入密钥|$ cleos wallet import ${key}	|[例子](#导入密钥)|
|钱包 - key列表|$ cleos wallet keys|[例子](#列出钱包密钥)|
|钱包 - 锁|$ cleos wallet lock -n ${wallet_name}|[例子](#锁钱包)|
|钱包 - 解锁钱包|$ cleos wallet unlock -n ${wallet_name} --password ${password}|[例子](#解锁钱包)|
|钱包 - 打开钱包|$ cleos wallet open|[例子](#打开钱包)|
|帐户 - 创建密钥|$ cleos create key|[例子](#创建密钥)|
|帐户 - 创建帐户|$ cleos create account ${control_account} ${account_name} ${owner_public_key} ${active_public_key}|[例子](#创建帐户)|
|帐户 - 查看子账户|$ cleos get servants ${account_name}|[例子](#子帐户)|
|帐户 - 检查帐户余额|$ cleos get account ${account_name}|[例子](#检查帐户余额)|
|权限 - 创建或修改权限|$ cleos set account permission ${permission} ${account} ${permission_json} ${account_authority}|[例子](#创建或修改权限)|
|合约 - 部署|$cleos set contract ../${contract}.wast ../${contract}.abi　or $cleos set contract ../${contract}|[例子](#部署合约)|
|合约 - 查询ABI|$ cleos get code -a ${contract}.abi ${contract}|[例子](#查询合约)|
|合约 - 推送操作|$ cleos push action ${contract} ${action} ${param} -S ${scope_1} -S ${scope_2} -p ${account}@active|[例子](#推送操作)|
|合约 - 查询表|$ cleos get table ${field} ${contract} ${table}|[例子](#查询表)|

## nodeos命令

|操作|语法|例子|
|---|---|---|
|跳过签名|$ nodeos --skip-transaction-signatures|[例子](#跳过签名)|

## keosd 命令

|操作|语法|例子|
|---|---|---|
|使用独立的钱包应用|$ keosd --http-server-endpoint ${node}:{port}|[例子](#使用独立的钱包应用)|

# 例子

## 全部命令

cleos包含所有命令的文档。有关cleos已知的所有命令的列表，只需简单地运行它，不带任何参数：

```
$ cleos
ERROR: RequiredError: Subcommand required
Command Line Interface to EOSIO Client
Usage: cleos [OPTIONS] SUBCOMMAND

Options:
  -h,--help                   打印帮助信息并且退出
  -u,--url TEXT=http://localhost:8888/
                              nodeos 运行的http/https URL
  --wallet-url TEXT=http://localhost:8888/
                              keosd运行的 http/https URL
  -v,--verbose                输出对错误的详细操作

子命令:
  version                     返回版本信息
  create                      在区块链上和区域外创建数据
  get                         从区块链中检索各种数据和信息
  set                         设置或更新区块链状态
  transfer                    从账户到账户转移EOS
  net                         与本地p2p网络连接进行交互
  wallet                      与本地钱包互动
  sign                        签署交易
  push                        将任意事务推送到区块链
  multisig                    Multisig合同命令
  system                      将eosio.system合约行为发送到区块链。
```

## 所有子命令

要获得有关任何特定子命令的帮助，请使用无参数运行它：

```
$ cleos create
ERROR: RequiredError: Subcommand required
Create various items, on and off the blockchain
Usage: ./cleos create SUBCOMMAND
Subcommands:
  key                         Create a new keypair and print the public and private keys
  account                     Create a new account on the blockchain
  producer                    Create a new producer on the blockchain

$ cleos create account
ERROR: RequiredError: creator
Create a new account on the blockchain
Usage: ./cleos create account creator name OwnerKey ActiveKey
Positionals:
  creator TEXT                The name of the account creating the new account
  name TEXT                   The name of the new account
  OwnerKey TEXT               The owner public key for the account
  ActiveKey TEXT              The active public key for the account
```

## 链接节点

这会将您连接到本地节点
```
$ cleos -u localhost:8889 <subcommand>
$ cleos --url localhost:8889 <subcommand>
```
您还可以调整节点参数以连接到其他节点，例如公共测试网
```
$ cleos -H test1.eos.io -p 80 <subcommand>
```
#### 注意 `你需要在每个请求中包含-H和-p参数来解决问题`

## 查询区块链状态

```
$ cleos get info
{
  "server_version": "7451e092",
  "head_block_num": 6980,
  "last_irreversible_block_num": 6963,
  "head_block_id": "00001b4490e32b84861230871bb1c25fb8ee777153f4f82c5f3e4ca2b9877712",
  "head_block_time": "2017-12-07T09:18:48",
  "head_block_producer": "initp",
  "recent_slots": "1111111111111111111111111111111111111111111111111111111111111111",
  "participation_rate": "1.00000000000000000"
}
```

## 通过transaction_id获取交易

通过在nodeos中加载account_history_api_plugin，我们可以使用transaciton_id查询特定事务

```
$ cleos get transaction eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703
{
  "transaction_id": "eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703",
  "processed": {
    "refBlockNum": 2206,
    "refBlockPrefix": 221394282,
    "expiration": "2017-09-05T08:03:58",
    "scope": [
      "inita",
      "tester"
    ],
    "signatures": [
      "1f22e64240e1e479eee6ccbbd79a29f1a6eb6020384b4cca1a958e7c708d3e562009ae6e60afac96f9a3b89d729a50cd5a7b5a7a647540ba1678831bf970e83312"
    ],
    "messages": [{
        "code": "eos",
        "type": "transfer",
        "authorization": [{
            "account": "inita",
            "permission": "active"
          }
        ],
        "data": {
          "from": "inita",
          "to": "tester",
          "amount": 1000,
          "memo": ""
        },
        "hex_data": "000000008040934b00000000c84267a1e80300000000000000"
      }
    ],
    "output": [{
        "notify": [{
            "name": "tester",
            "output": {
              "notify": [],
              "sync_transactions": [],
              "async_transactions": []
            }
          },{
            "name": "inita",
            "output": {
              "notify": [],
              "sync_transactions": [],
              "async_transactions": []
            }
          }
        ],
        "sync_transactions": [],
        "async_transactions": []
      }
    ]
  }
}
```

## 通过帐户获取交易

我们也可以查询某个账户从最近一个账户开始执行的交易清单

```
$ cleos get transactions inita
[
  {
    "transaction_id": "eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703",
    ...
  },
  {
    "transaction_id": "6acd2ece68c4b86c1fa209c3989235063384020781f2c67bbb80bc8d540ca120",
    ...
  },
  ...
]
```

## 转账EOS

```
$ cleos transfer inita tester 1000
{
  "transaction_id": "eb4b94b72718a369af09eb2e7885b3f494dd1d8a20278a6634611d5edd76b703",
  "processed": {
    "refBlockNum": 2206,
    "refBlockPrefix": 221394282,
    "expiration": "2017-09-05T08:03:58",
    "scope": [
      "inita",
      "tester"
    ],
    "signatures": [
      "1f22e64240e1e479eee6ccbbd79a29f1a6eb6020384b4cca1a958e7c708d3e562009ae6e60afac96f9a3b89d729a50cd5a7b5a7a647540ba1678831bf970e83312"
    ],
    "messages": [{
        "code": "eos",
        "type": "transfer",
        "authorization": [{
            "account": "inita",
            "permission": "active"
          }
        ],
        "data": {
          "from": "inita",
          "to": "tester",
          "amount": 1000,
          "memo": ""
        },
        "hex_data": "000000008040934b00000000c84267a1e80300000000000000"
      }
    ],
    "output": [{
        "notify": [{
            "name": "tester",
            "output": { ... }
          },{
            "name": "inita",
            "output": { ... }
          }
        ],
        "sync_transactions": [],
        "async_transactions": []
      }
    ]
  }
}
```

## 创建钱包

创建钱包时不指定名称，钱包将使用名称'default'

```
$ cleos wallet create
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5JD9cw9YY288AXPvnbwUk5JK4Cy6YyZ83wzHcshu8F2akU9rRWE"
```
您可以在命令中添加-n ${wallet_name}命名钱包

```
$ cleos wallet create -n second-wallet
Creating wallet: second-wallet
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5Ji6JUrLjhKAVn68nmacLxwhvtqUAV18J7iycZppsPKeoGGgBEw"
```

## 钱包列表

列表钱包命令将列出每个钱包状态的所有钱包，*符号表示钱包当前已解锁。
```
$ cleos wallet list
Wallets:
[
  "default *",
  "second-wallet *"
]
```

## 导入密钥

注意: 如果您没有持有账户密钥，则需要使用create account命令先创建账户密钥。

```
$ cleos wallet import 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
imported private key for: EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

## 列出钱包密钥
这将列出公钥中存储在钱包中的所有密钥。

```
$ cleos wallet keys
[[
    "EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"
  ]
]
```

## 锁钱包
```
$ cleos wallet lock -n second-wallet
Locked: 'second-wallet'
```
注意锁定的钱包在列表中没有*符号
```
$ cleos wallet list
Wallets:
[
  "default *",
  "second-wallet"
]
```

## 解锁钱包

要解锁它，请指定创建钱包时获得的密码
```
$ cleos wallet unlock -n second-wallet --password PW5Ji6JUrLjhKAVn68nmacLxwhvtqUAV18J7iycZppsPKeoGGgBEw
Unlocked: 'second-wallet'
```

## 打开钱包
```
$ cleos wallet open
Wallets: [
  "default"
]
$ cleos wallet open -n second-wallet
Wallets: [
  "default",
  "second-wallet"
]
```

## 创建密钥

为了创建一个帐户，你需要两个新的密钥：所有者和主动。您可以要求cleos为您创建一些钥匙：

这将是你的`Owner Key`，
```
$ cleos create key
public: EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq
private: 5JKbLfCXgcafDQVwHMm3shHt6iRWgrr9adcmt6vX3FNjAEtJGaT
```
这将是你的主动键，
```
$ cleos create key
public: EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA
private: 5Hv22aPcjnENBv6X9o9nKGdkfrW44En6z4zJUt2PobAvbQXrT9z
```
注意：cleos不保存生成的私钥。

## 创建帐号

您将需要您的EOS密钥才能创建帐户，您必须在Ethereum网络上注册您的EOS密钥，或者您可以使用创建密钥功能创建一组新密钥

```
$ cleos create account inita tester EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA
{
  "transaction_id": "6acd2ece68c4b86c1fa209c3989235063384020781f2c67bbb80bc8d540ca120",
  "processed": {
    "refBlockNum": "25217",
    "refBlockPrefix": "2095475630",
    "expiration": "2017-07-25T17:54:55",
    "scope": [
      "eos",
      "inita"
    ],
    "signatures": [],
    "messages": [{
        "code": "eos",
        "type": "newaccount",
        "authorization": [{
            "account": "inita",
            "permission": "active"
          }
        ],
        "data": "c9251a0000000000b44c5a2400000000010000000102bcca6347d828d4e1868b7dfa91692a16d5b20d0ee3d16a7ca2ddcc7f6dd03344010000010000000102bcca6347d828d4e1868b7dfa91692a16d5b20d0ee3d16a7ca2ddcc7f6dd03344010000010000000001c9251a000000000061d0640b000000000100010000000000000008454f5300000000"
      }
    ],
    "output": [{
        "notify": [],
        "sync_transactions": [],
        "async_transactions": []
      }
    ]
  }
}
```

## 子帐户
要检查由帐户（控制帐户）创建的子帐户
```
$ cleos get servants inita
{
  "controlled_accounts": [
    "tester"
  ]
}
```
## 检查帐户余额

```
$ cleos get account tester
{
  "name": "tester",
  "eos_balance": 0,
  "staked_balance": 1,
  "unstaking_balance": 0,
  "last_unstaking_time": "1969-12-31T23:59:59",
  "permissions": [{
      "name": "active",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [{
            "key": "EOS7d9A3uLe6As66jzN8j44TXJUqJSK3bFjjEEqR4oTvNAB3iM9SA",
            "weight": 1
          }
        ],
        "accounts": []
      }
    },{
      "name": "owner",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [{
            "key": "EOS4toFS3YXEQCkuuw1aqDLrtHim86Gz9u3hBdcBw5KNPZcursVHq",
            "weight": 1
          }
        ],
        "accounts": []
      }
    }
  ]
}
```

## 创建或修改权限

要修改帐户的权限，您必须拥有该帐户的权限和您正在修改的权限。设置帐户权限命令可能会更改，因此它关联的类没有完整记录。

```
$ cleos set account permission test active '{"threshold" : 1, "keys" : [{"permission":{"key":"EOS8X7Mp7apQWtL6T2sfSZzBcQNUqZB7tARFEm9gA9Tn9nbMdsvBB","permission":"active"},"weight":1}], "accounts" : [{"permission":{"actor":"acc2","permission":"active"},"weight":50}]}' owner
```
第二个示例修改相同的帐户权限，但删除上一个示例中的密钥集，并将@test帐户的活动权限授予另一个帐户。
```
$ cleos set account permission test active '{"threshold" : 1, "keys" : [], "accounts" : [{"permission":{"actor":"sandwich","permission":"active"},"weight":1},{"permission":{"actor":"acc1","permission":"active"},"weight":50}]}' owner
```
第三个示例演示如何设置multisig的权限

```
$ cleos set account permission test active '{"threshold" : 100, "keys" : [{"permission":{"key":"EOS8X7Mp7apQWtL6T2sfSZzBcQNUqZB7tARFEm9gA9Tn9nbMdsvBB","permission":"active"},"weight":25}], "accounts" : [{"permission":{"actor":"@sandwich","permission":"active"},"weight":75}]}' owner
```
此命令中使用的JSON对象实际上由两种不同类型的对象组成
权限JSON对象...
```
{
  "threshold"       : 100,    /*An integer that defines cumulative signature weight required for authorization*/
  "keys"            : [],     /*An array made up of individual permissions defined with an EOS PUBLIC KEY*/
  "accounts"        : []      /*An array made up of individual permissions defined with an EOS ACCOUNT*/
}
```
...其中包括一个或多个权限对象
```
/*Set Permission with Key*/
{
  "permission" : {
    "key"           : "EOS8X7Mp7apQWtL6T2sfSZzBcQNUqZB7tARFEm9gA9Tn9nbMdsvBB",
    "permission"    : "active"
  },
  weight            : 25      /*Set the weight of a signature from this permission*/
}

/*Set Permission with Account*/
{
  "permission" : {
    "actor"       : "sandwich",
    "permission"  : "active"
  },
  weight            : 75      /*Set the weight of a signature from this permission*/
}
```

## 部署合约
要将合约上传到区块链，您必须先持有一个帐户和一个持有该帐户的钱包。其次，你需要你的合同文件（.wast）和它的abi（.abi）。然后你可以继续设置代码。

```
$ cleos set contract currency ../../../contracts/currency/currency.wast ../../../contracts/currency/currency.abi
Reading WAST...
Assembling WASM...
Publishing contract...
{
  "transaction_id": "9990306e13f630a9c5436a5a0b6fb8fe2c7f3da2f342b4898a39c4a2c17dcdb3",
  "processed": {
    "refBlockNum": 1208,
    "refBlockPrefix": 3058534156,
    "expiration": "2017-08-24T18:29:52",
    "scope": [
      "currency",
      "eos"
    ],
    "signatures": [],
    "messages": [{
        "code": "eos",
        "type": "setcode",
        "authorization": [{
            "account": "currency",
            "permission": "active"
          }
        ],
        "data": "00000079b822651d0000e8150061736d0100000001390a60017e0060037e7e7f017f60047e7e7f7f017f60017f0060057e7e7e7f7f017f60027f7f0060027f7f017f60027e7f0060000060027e7e00029d010a03656e7606617373657274000503656e76086c6f61645f693634000403656e76067072696e7469000003656e76067072696e746e000003656e76067072696e7473000303656e760b726561644d657373616765000603656e760a72656d6f76655f693634000103656e760b7265717569726541757468000003656e760d726571756972654e6f74696365000003656e760973746f72655f6936340002030706000007030809040401700000050301000107cc0107066d656d6f72790200205f5a4e33656f733133726571756972654e6f74696365454e535f344e616d6545000a1e5f5a4e33656f7331317265717569726541757468454e535f344e616d6545000b345f5a4e3863757272656e6379313273746f72654163636f756e74454e33656f73344e616d6545524b4e535f374163636f756e7445000c355f5a4e3863757272656e637932336170706c795f63757272656e63795f7472616e7366657245524b4e535f385472616e7366657245000d04696e6974000e056170706c79000f0a9d0d060600200010080b0600200010070b3400024020012903084200510d0020004280808080a8d7bee3082001411010091a0f0b20004280808080a8d7bee308200110061a0b8a0604017e027f047e017f4100410028020441206b2208360204200029030821052000290300210720002903102104411010042004100241c000100442808080c887d7c8b21d100341d00010042007100341e000100420051003200029030021052000290308100820051008200029030010072000290300210142002105423b210441f00021034200210603400240024002400240024020054206560d0020032c00002202419f7f6a41ff017141194b0d01200241a0016a21020c020b420021072005420b580d020c030b200241ea016a41002002414f6a41ff01714105491b21020b2002ad42388642388721070b2007421f83200442ffffffff0f838621070b200341016a2103200542017c2105200720068421062004427b7c2204427a520d000b420021052008420037031820082006370310200142808080c887d7c8b21d4280808080a8d7bee308200841106a411010011a200041086a2903002101423b210441f00021034200210603400240024002400240024020054206560d0020032c00002202419f7f6a41ff017141194b0d01200241a0016a21020c020b420021072005420b580d020c030b200241ea016a41002002414f6a41ff01714105491b21020b2002ad42388642388721070b2007421f83200442ffffffff0f838621070b200341016a2103200542017c2105200720068421062004427b7c2204427a520d000b2008200637030020084200370308200142808080c887d7c8b21d4280808080a8d7bee3082008411010011a200841186a2203290300200041106a22022903005a418001100020032003290300200229030022057d370300200520082903087c20055a41b00110002008200829030820022903007c370308200029030021050240024020032903004200510d0020054280808080a8d7bee308200841106a411010091a0c010b20054280808080a8d7bee308200841106a10061a0b200041086a290300210502400240200841086a2903004200510d0020054280808080a8d7bee3082008411010091a0c010b20054280808080a8d7bee308200810061a0b4100200841206a3602040b980303027f057e017f4100410028020441106b220736020442002103423b210241e00121014200210403400240024002400240024020034207560d0020012c00002200419f7f6a41ff017141194b0d01200041a0016a21000c020b420021052003420b580d020c030b200041ea016a41002000414f6a41ff01714105491b21000b2000ad42388642388721050b2005421f83200242ffffffff0f838621050b200141016a2101200342017c2103200520048421042002427b7c2202427a520d000b42002103423b210241f00021014200210603400240024002400240024020034206560d0020012c00002200419f7f6a41ff017141194b0d01200041a0016a21000c020b420021052003420b580d020c030b200041ea016a41002000414f6a41ff01714105491b21000b2000ad42388642388721050b2005421f83200242ffffffff0f838621050b200141016a2101200342017c2103200520068421062002427b7c2202427a520d000b2007428094ebdc033703082007200637030020044280808080a8d7bee3082007411010091a4100200741106a3602040bb10303027f047e017f4100410028020441206b220836020442002105423b210441e00121034200210603400240024002400240024020054207560d0020032c00002202419f7f6a41ff017141194b0d01200241a0016a21020c020b420021072005420b580d020c030b200241ea016a41002002414f6a41ff01714105491b21020b2002ad42388642388721070b2007421f83200442ffffffff0f838621070b200341016a2103200542017c2105200720068421062004427b7c2204427a520d000b024020062000520d0042002105423b210441f00121034200210603400240024002400240024020054207560d0020032c00002202419f7f6a41ff017141194b0d01200241a0016a21020c020b420021072005420b580d020c030b200241ea016a41002002414f6a41ff01714105491b21020b2002ad42388642388721070b2007421f83200442ffffffff0f838621070b200341016a2103200542017c2105200720068421062004427b7c2204427a520d000b20062001520d00200842003703102008420037030820084200370318200841086a4118100541174b4180021000200841086a100d0b4100200841206a3602040b0bff010b0041040b04200500000041100b2254686973206170706561727320746f2062652061207472616e73666572206f6620000041c0000b0220000041d0000b072066726f6d20000041e0000b0520746f20000041f0000b086163636f756e7400004180010b2c696e746567657220756e646572666c6f77207375627472616374696e6720746f6b656e2062616c616e6365000041b0010b26696e7465676572206f766572666c6f7720616464696e6720746f6b656e2062616c616e6365000041e0010b0963757272656e6379000041f0010b097472616e7366657200004180020b1e6d6573736167652073686f72746572207468616e2065787065637465640000fd02046e616d651006617373657274020000086c6f61645f693634050000000000067072696e74690100067072696e746e0100067072696e747301000b726561644d6573736167650200000a72656d6f76655f693634030000000b726571756972654175746801000d726571756972654e6f7469636501000973746f72655f6936340400000000205f5a4e33656f733133726571756972654e6f74696365454e535f344e616d65450101301e5f5a4e33656f7331317265717569726541757468454e535f344e616d6545010130345f5a4e3863757272656e6379313273746f72654163636f756e74454e33656f73344e616d6545524b4e535f374163636f756e74450201300131355f5a4e3863757272656e637932336170706c795f63757272656e63795f7472616e7366657245524b4e535f385472616e73666572450901300131013201330134013501360137013804696e69740801300131013201330134013501360137056170706c7909013001310132013301340135013601370138010b4163636f756e744e616d65044e616d6502087472616e7366657200030466726f6d0b4163636f756e744e616d6502746f0b4163636f756e744e616d65087175616e746974790655496e743634076163636f756e740002036b65790655496e7436340762616c616e63650655496e74363401000000b298e982a4087472616e736665720100000080bafac608076163636f756e74"
      }
    ],
    "output": [{
        "notify": [],
        "sync_transactions": [],
        "async_transactions": []
      }
    ]
  }
}
```

## 查询ABI

我们可以在区块链中查询合同的.abi，我们可以在其中查看可用操作列表及其各自的消息结构
```
$ cleos get code -a currency.abi currency
code hash: 9b9db1a7940503a88535517049e64467a6e8f4e9e03af15e9968ec89dd794975
saving abi to currency.abi
$ cat currency.abi
{
  "types": [{
      "newTypeName": "AccountName",
      "type": "Name"
    }
  ],
  "structs": [{
      "name": "transfer",
      "base": "",
      "fields": {
        "from": "AccountName",
        "to": "AccountName",
        "amount": "UInt64"
      }
    },{
      "name": "account",
      "base": "",
      "fields": {
        "account": "Name",
        "balance": "UInt64"
      }
    }
  ],
  "actions": [{
      "action": "transfer",
      "type": "transfer"
    }
  ],
  "tables": [{
      "table": "account",
      "indextype": "i64",
      "keynames": [
        "account"
      ],
      "keytype": [],
      "type": "account"
    }
  ]
}
```
## 推送操作

根据合同ABI，消息应该是合同。

例如，货币合约的ABI被构造如下。

```
$ cleos get code -a currency.abi currency
code hash: 9b9db1a7940503a88535517049e64467a6e8f4e9e03af15e9968ec89dd794975
saving abi to currency.abi
$ cat currency.abi
{
  "types": [{
      "newTypeName": "AccountName",
      "type": "Name"
    }
  ],
  "structs": [{
      "name": "transfer",
      "base": "",
      "fields": {
        "from": "AccountName",
        "to": "AccountName",
        "amount": "UInt64"
      }
    },{
      "name": "account",
      "base": "",
      "fields": {
        "account": "Name",
        "balance": "UInt64"
      }
    }
  ],
  "actions": [{
      "action": "transfer",
      "type": "transfer"
    }
  ],
  "tables": [{
      "table": "account",
      "indextype": "i64",
      "keynames": [
        "account"
      ],
      "keytype": [],
      "type": "account"
    }
  ]
}
```
从上面的abi中，我们可以看到货币合约接受一个叫做transfer的操作，它接受带有from，to和amount字段的消息。

```
$ ./cleos push message currency transfer '{"from":"currency","to":"tester","amount":50}' -S currency -S tester -p currency@active
1589302ms thread-0   main.cpp:271                  operator()           ] Converting argument to binary...
1589304ms thread-0   main.cpp:290                  operator()           ] Transaction result:
{
  "transaction_id": "1c4911c0b277566dce4217edbbca0f688f7bdef761ed445ff31b31f286720057",
  "processed": {
    "refBlockNum": 1173,
    "refBlockPrefix": 2184027244,
    "expiration": "2017-08-24T18:28:07",
    "scope": [
      "currency",
      "tester"
    ],
    "signatures": [],
    "messages": [{
        "code": "currency",
        "type": "transfer",
        "authorization": [{
            "account": "currency",
            "permission": "active"
          }
        ],
        "data": {
          "from": "currency",
          "to": "tester",
          "quantity": 50
        },
        "hex_data": "00000079b822651d00000000c84267a13200000000000000"
      }
    ],
    "output": [{
        "notify": [{
            "name": "tester",
            "output": {
              "notify": [],
              "sync_transactions": [],
              "async_transactions": []
            }
          }
        ],
        "sync_transactions": [],
        "async_transactions": []
      }
    ]
  }
}
```
## 查询合约

根据合约中定义的表结构，您可以查询合同中的数据。
例如，currency合约ABI包含账户表。

```
$ cleos get code -a currency.abi currency
code hash: 9b9db1a7940503a88535517049e64467a6e8f4e9e03af15e9968ec89dd794975
saving abi to currency.abi
$ cat currency.abi
{
  ...
  "tables": [{
      "table": "account",
      "indextype": "i64",
      "keynames": [
        "account"
      ],
      "keytype": [],
      "type": "account"
    }
  ]
}
```

您可以查询指定必要字段的表格。

```
$ cleos get table inita currency account
{
  "rows": [{
      "account": "account",
      "balance": 50
    }
  ],
  "more": true
}
```

## 跳过签名

如果您的本地运行nodeos，作为开发人员在不处理密钥的情况下测试功能的简便方法，则可以运行nodeos以便不需要Transaction签名。

```
$ nodeos --skip-transaction-signatures
```
然后对于任何需要签名的操作，请使用-s选项
```
$ cleos ${command} ${subcommand} -s ${param}
```

## 使用独立的钱包应用

您可以使用可在`program/keosd`中找到的单独的钱包应用程序，而不是使用内置于nodeos的钱包功能。默认情况下，节点使用端口8888，因此请为钱包应用选择另一个端口。
```
$ keosd --http-server-endpoint 127.0.0.1:8887

```
然后，对于任何需要签名的操作，请使用-wallet-host和-wallet-port选项.
```
$ cleos —-wallet-url 127.0.0.1:8887 ${command} ${subcommand} ${param}
```
## 错误示例
```
status_code == 200: Error
: 10 assert_exception: Assert Exception
test: assertion failed: integer underflow subtracting token balance
    {"s":"integer underflow subtracting token balance","ptr":176}
    thread-1  wasm_interface.cpp:248 assertnonei32i32
[...snipped...]
```
