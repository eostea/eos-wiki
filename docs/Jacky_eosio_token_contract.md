## eosio代币合约教程

### Eosio.token, Exchange与Eosio.msig合约
本节内容假设您已掌握《智能合约入门》一节。

现在，其实已经搭建起来的区块链并不能做多少事情，所以，首先让我们来部署一个代币合约`eosio.token`。这个合约能让许多不同的代币同时在一个智能合约上发行，但是却能被不同用户管理。

在发行一个代币前，我们需要建立一个账户用来部署代币合约。
> cleos create account eosio eosio.token 公钥1 公钥2

以上命令创建了一个名为`eosio.token`的账户。

然后，将位于`${EOS根目录}/build/contracts/eosio.token`的文件部署到eosio.token账户。
> cleos set contract eosio.token build/contracts/eosio.token -p eosio.token
> &nbsp;&nbsp;&nbsp;&nbsp;Reading WAST...
> &nbsp;&nbsp;&nbsp;&nbsp;Assembling WASM...
> &nbsp;&nbsp;&nbsp;&nbsp;Publishing contract...
> executed transaction: 528bdbce1181dc5fd72a24e4181e6587dace8ab43b2d7ac9b22b2017992a07ad  8708 bytes  10000 cycles
>          eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001ce011d60067f7e7f7f7f7f00...
>          eosio <= eosio::setabi                {"account":"eosio.token","abi":{"types":[],"structs":[{"name":"transfer","base":"","fields":[{"name"...

#### 创建一个代币
以下是代币发型合约`eosio.token`的标准接口，文件位于 `contracts/eosio.token/eosio.token.hpp`:
```
   void create( account_name issuer,
                asset        maximum_supply,
                uint8_t      can_freeze,
                uint8_t      can_recall,
                uint8_t      can_whitelist );

   void issue( account_name to, asset quantity, string memo );

   void transfer( account_name from,
                  account_name to,
                  asset        quantity,
                  string       memo );
```
译者注：开发过以太坊ERC20代币的一定熟悉，ERC20规范了totalSupply(), balanceOf(), transfer(), transferFrom()等六个方法。`eosio.token`也做了类似规范，只要是create(),issue(),transfer()三个方法构造的代码，就是一个发币智能合约。

如果需要创建一个代币，首先需要执行create(...)方法，在这个方法中，`maximum_supply`代表需要发行的资产，用字符串表示，比如发行10亿个BOS，则为`"1000000000.0000 BOS"`。发行方`issuer`必须是经过授权的能够执行发行资产`issue`、冻结资产`freezing`、召回资产`recall`、设置白名单`whitelist`等操作。

下面用`cleos`命令来创建一个名为BOS的代币，发行量为10亿，精度为小数点后4位：
```
$ cleos push action eosio.token create '[ "eosio", "1000000000.0000 EOS", 0, 0, 0]' -p eosio.token
执行结果:
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```

另外，还有一种较为复杂的方式，使用JSON格式的参数：
```
$ cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 EOS", "can_freeze":0, "can_recall":0, "can_whitelist":0}' -p eosio.token
执行结果
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```
由于创建的代币需要“拥有”这个代币的命名空间（如EOS），因而获得`eosio.token`智能合约的权限，所以在以上命令中，有一个参数 `-p eosio.token`。

#### 让账户`user`发行代币
现在，我们来创建一个代币，代币的发行方为账户`user`。
可以使用如下命令：
```
$ cleos push action eosio.token issue '[ "user", "100.0000 EOS", "memo" ]' -p eosio
执行结果：
executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019  268 bytes  1000 cycles
#   eosio.token <= eosio.token::issue           {"to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> issue
#   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> transfer
#         eosio <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
#          user <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
```
这次操作，输出的信息中出现了一个`issue`和三个`transfer`。但是，我们只签名了一个操作`issue`，怎么会出现三个`transfer`呢？这是因为这个`issue`操作带动了`inline transfer`（内联交易操作），并且这个内联交易操作通知了发送方和接收方的帐号。以上三个`transfer`显示了所有的操作细节，包括操作命令、顺序等等。

如果需要查看被广播的实际交易，可以使用`-d -j`选项来指示：不要广播，以JSON形式返回交易内容，如下：
```
$ cleos push action eosio.token issue '["user", "100.0000 EOS", "memo"]' -p eosio -d -j
结果如下：
{
  "expiration": "2018-04-01T15:20:44",
  "region": 0,
  "ref_block_num": 42580,
  "ref_block_prefix": 3987474256,
  "net_usage_words": 21,
  "kcpu_usage": 1000,
  "delay_sec": 0,
  "context_free_actions": [],
  "actions": [{
      "account": "eosio.token",
      "name": "issue",
      "authorization": [{
          "actor": "eosio",
          "permission": "active"
        }
      ],
      "data": "00000000007015d640420f000000000004454f5300000000046d656d6f"
    }
  ],
  "signatures": [
    "EOSJzPywCKsgBitRh9kxFNeMJc8BeD6QZLagtXzmdS2ib5gKTeELiVxXvcnrdRUiY3ExP9saVkdkzvUNyRZSXj2CLJnj7U42H"
  ],
  "context_free_data": []
}
```

#### 将代币转到账户`tester`
经过上一步操作，账户`user`已经拥有了代币，现在我们将25个EOS转给账户`tester`。我们使用选项 `-p user`来授权这个操作。
```
$ cleos push action eosio.token transfer '[ "user", "tester", "25.0000 EOS", "m" ]' -p user
执行结果：
executed transaction: 06d0a99652c11637230d08a207520bf38066b8817ef7cafaab2f0344aafd7018  268 bytes  1000 cycles
#   eosio.token <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#          user <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
#        tester <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
```
### 部署交易所合约
与上述例子类似，我们也可以部署交易所`exchange`合约。`exchange`合约提供了创建与交易代币的功能。这个部署需要在EOSIO的根目录进行。
第一步：创建`exchange`帐号
```
$ cleos create account eosio exchange 公钥1 公钥2
执行结果：
executed transaction: 4d38de16631a2dc698f1d433f7eb30982d855219e7c7314a888efbbba04e571c  364 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"exchange","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJxe...
```

第二步：创建`exchange`智能合约
```
$ cleos set contract exchange build/contracts/exchange -p exchange
执行结果：
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 5a63b4de8a1da415590778f163c5ed26dc164c960185b20fd834c297cf7fa8f4  35172 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"exchange","vmtype":0,"vmversion":0,"code":"0061736d0100000001f0023460067f7e7f7f7f7f00600...
#         eosio <= eosio::setabi                {"account":"exchange","abi":{"types":[{"new_type_name":"account_name","type":"name"}],"structs":[{"n...
```

### 部署Eosio.msig多人签名合约
`eosio.msig`合约用来允许多方共同对一个交易进行异步签名。
EOSIO已经在基础层上支持多方签名，但是需要一个异步通道来传递交易并签名。`eosio.msg` 是一个非常友好的异步提议、确认、最终发布交易的方式。
下面是部署`eosio.msig`合约的步骤。
第一步：创建`eosio.msig`账户：
```
$ cleos create account eosio eosio.msig 公钥1 公钥2 
执行结果：
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"eosio.msig","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJ...
```
第二步：部署`eosio.msig`合约：
```
$ cleos set contract eosio.msig build/contracts/eosio.msig -p eosio.msig
执行结果：
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: a113a7db8c878dfd894671792770b59a04efb3aa8295f5b3d585daf89c314ec9  8964 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio.msig","vmtype":0,"vmversion":0,"code":"0061736d0100000001bd011b60047f7e7e7f0060047...
#         eosio <= eosio::setabi                {"account":"eosio.msig","abi":{"types":[{"new_type_name":"account_name","type":"name"},{"new_type_na...
```
