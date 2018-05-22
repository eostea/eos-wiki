# Tutorial eosio token Contract
Greg Lee 18 天之前编辑过 · 有 2 处修订

Eosio.token，Exchange和Eosio.msig合约
---------------------------------
本教程假定您已完成教程：[智能合约入门](https://github.com/eostea/eos-wiki/blob/master/docs/10.1.%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E5%85%A5%E9%97%A8.md)。

我们来部署eosio.token合同，该合约允许在同一个合约上，创建并运行许多不同的`token`，但这些`token`可能由不同的用户管理。

在部署`token`合同之前，必须创建一个帐户来部署它。这里创建的账户名是`eosio.token`，当然也可以用别的账户名。
```cpp
$ cleos create account eosio eosio.token EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
...
```
然后可以部署合约，合约在这个目录下：`${EOSIO_SOURCE}/build/contracts/eosio.token`
```cpp
$ cleos set contract eosio.token build/contracts/eosio.token -p eosio.token
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 528bdbce1181dc5fd72a24e4181e6587dace8ab43b2d7ac9b22b2017992a07ad  8708 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d0100000001ce011d60067f7e7f7f7f7f00...
#         eosio <= eosio::setabi                {"account":"eosio.token","abi":{"types":[],"structs":[{"name":"transfer","base":"","fields":[{"name"...
```
### 创建EOS Token

您可以在`contracts/eosio.token/eosio.token.hpp`头文件中查看`eosio.token`合约提供的操作(`actions`)：
```cpp
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
要创建一个新的`token`，我们必须用适当的参数调用`create(...)`操作。该命令将使用最大供应量(`maximum_supply`)的token符号来唯一标识该`token`。发行人(issuer)
将有权调用发行(issue)操作，和执行其他操作，例如冻结(freezing)，召回(recalling)以及将用户列入白名单(whitelisting)。

调用此操作的简洁方法如下(序列化参数)：
```cpp
$ cleos push action eosio.token create '[ "eosio", "1000000000.0000 EOS", 0, 0, 0]' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```
或者，使用命名参数调用此操作的更为详细的的方法：
```cpp
$ cleos push action eosio.token create '{"issuer":"eosio", "maximum_supply":"1000000000.0000 EOS", "can_freeze":0, "can_recall":0, "can_whitelist":0}' -p eosio.token
executed transaction: 0e49a421f6e75f4c5e09dd738a02d3f51bd18a0cf31894f68d335cd70d9c0e12  260 bytes  1000 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```
该命令创建了一个名为`EOS`的新token，其精度为小数点后4位，最大供应量为1000000000.0000 EOS。

为了创建这个`token`，我们需要`eosio.token`合约账户的授权，因为它"拥有"token符号命名空间（例如，“EOS”）。本合约未来的版本可能允许其他账户购买`token`符号名称。所以，我们必须通过`-p eosio.token`对此调用进行授权。

### 给账户“User”发行Token

现在我们已经创建了名为`EOS`的`token`，发行人可以向我们之前创建的`user`帐户发行新的`token`。

我们使用序列化参数方式调用操作（或者使用命名参数方式）。
```cpp
$ cleos push action eosio.token issue '[ "user", "100.0000 EOS", "memo" ]' -p eosio
executed transaction: 822a607a9196112831ecc2dc14ffb1722634f1749f3ac18b73ffacd41160b019  268 bytes  1000 cycles
#   eosio.token <= eosio.token::issue           {"to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> issue
#   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
>> transfer
#         eosio <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
#          user <= eosio.token::transfer        {"from":"eosio","to":"user","quantity":"100.0000 EOS","memo":"memo"}
```
这次输出包含了几个不同的操作：一次发行(issue)和三次转账(transfer)。虽然我们执行的唯一一项操作是`issue`，但该`issue`操作执行了“内联转账
(`inline transfer`)”，“内联转账”通知发送者和收款人帐户。输出内容展示了操作中所有被调用的处理程序被调用的顺序，以及其产生的所有输出。

从技术上讲，`eosio.token`合同可以跳过"内联转账"`inline transfer`步骤，并选择直接修改余额。然而，在`eosio.token`合约中遵循了token转账的惯例做法，即要求所有账户的余额都可以通过引用它们的转账行为的总和来推导。它还要求通知资金的发送者和收款人，以便他们能够自动处理存款和提款。

如果你想看到交易(transaction)被广播的实际情况，可以使用`-d -j`选项来表示“不要广播”和“以json格式返回交易执行情况”。
```cpp
$ cleos push action eosio.token issue '["user", "100.0000 EOS", "memo"]' -p eosio -d -j
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
### 给账户“Tester”转账

现在`user`账户有token了，我们会将一些token转账给账户`tester`。我们用授权参数`-p user`表示`user`授权了此操作。
```cpp
$ cleos push action eosio.token transfer '[ "user", "tester", "25.0000 EOS", "m" ]' -p user
executed transaction: 06d0a99652c11637230d08a207520bf38066b8817ef7cafaab2f0344aafd7018  268 bytes  1000 cycles
#   eosio.token <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#          user <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
#        tester <= eosio.token::transfer        {"from":"user","to":"tester","quantity":"25.0000 EOS","memo":"m"}
```
### 部署Exchange合同

与上述示例类似，我们可以部署exchange合约，该合约提供了创建和交易货币的功能。跟以前一样，我们假定下面的命令是从EOSIO源代码的根目录运行的。
```cpp
$ cleos create account eosio exchange  EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
executed transaction: 4d38de16631a2dc698f1d433f7eb30982d855219e7c7314a888efbbba04e571c  364 bytes  1000 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"exchange","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJxe...

$ cleos set contract exchange build/contracts/exchange -p exchange
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: 5a63b4de8a1da415590778f163c5ed26dc164c960185b20fd834c297cf7fa8f4  35172 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"exchange","vmtype":0,"vmversion":0,"code":"0061736d0100000001f0023460067f7e7f7f7f7f00600...
#         eosio <= eosio::setabi                {"account":"exchange","abi":{"types":[{"new_type_name":"account_name","type":"name"}],"structs":[{"n...
```
### 部署Eosio.msig合同

`eosio.msig`合约允许多方异步签署单个交易。作为一种基础功能，EOSIO提供了多重签名（`multisig`）支持，但它需要一个同步侧通道，数据在这个通道中传输并签名。
`Eosio.msig`是一个对用户更加友好的方式，异步提出、批准并最终发布多方同意的交易。

以下步骤可用于部署`eosio.msig`合同。
```cpp
$ cleos create account eosio eosio.msig  EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"eosio.msig","owner":{"threshold":1,"keys":[{"key":"EOS7ijWCBmoXBi3CgtK7DJ...
  
$ cleos set contract eosio.msig build/contracts/eosio.msig -p eosio.msig
Reading WAST...
Assembling WASM...
Publishing contract...
executed transaction: a113a7db8c878dfd894671792770b59a04efb3aa8295f5b3d585daf89c314ec9  8964 bytes  10000 cycles
#         eosio <= eosio::setcode               {"account":"eosio.msig","vmtype":0,"vmversion":0,"code":"0061736d0100000001bd011b60047f7e7e7f0060047...
#         eosio <= eosio::setabi                {"account":"eosio.msig","abi":{"types":[{"new_type_name":"account_name","type":"name"},{"new_type_na...
```
下一步 - Hello World教程
----------------------
请继续阅读[Hello World教程](https://github.com/eostea/eos-wiki/blob/master/docs/10.3.Hello%20World%E5%90%88%E7%BA%A6.md)。
