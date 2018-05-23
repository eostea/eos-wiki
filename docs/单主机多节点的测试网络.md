
本教程介绍如何设置在单个主机上运行的多节点区块链配置。这被称为单个主机，多节点testnet。我们将在本地计算机上设置两个节点，并让它们相互通信。本节中的示例依赖于三个命令行应用程序，nodeos，keosd，和cleos。下图描述了所需的测试网络配置。
![Single Host, Multi-Node Testnet](assets/Single-Host-Multi-Node-Testnet.png)

假设keosd，cleos以及nodeos已经安装在你的路径，或者你知道如何从文件系统中的位置启动这些应用程序。（请参阅设置本地环境） (See [Setting Up A Local Environment](Local-Environment))

打开四个“终端”窗口来执行本教程中的步骤。

### 启动电子钱包管理器
在第一个终端窗口中，启动keosd钱包管理应用程序:
```
keosd --http-server-address 127.0.0.1:8899
```
如果成功，keosd将显示一些信息：
```
2493323ms thread-0   wallet_plugin.cpp:39          plugin_initialize    ] initializing wallet plugin
2493323ms thread-0   http_plugin.cpp:141           plugin_initialize    ] host: 127.0.0.1 port: 8899
2493323ms thread-0   http_plugin.cpp:144           plugin_initialize    ] configured http to listen on 127.0.0.1:8899
2493323ms thread-0   http_plugin.cpp:213           plugin_startup       ] start listening for http requests
2493324ms thread-0   wallet_api_plugin.cpp:70      plugin_startup       ] starting wallet_api_plugin
```

Look for a line saying the wallet is listening on 127.0.0.1:8899. 这将表明已keosd正确启动并正在侦听正确的端口。 如果您看到其他任何内容，或者在“启动wallet_api_plugin”之前看到一些错误报告，则需要诊断问题并重新启动。.

当keosd正确运行时，使钱包应用程序运行时打开该窗口并移至下一个终端窗口。
leave that window open with the wallet app running and move to the next terminal window.

### 创建一个默认钱包
在下一个终端窗口中，使用cleos命令行实用程序创建默认钱包。
```
cleos --wallet-port 8899  wallet create
```
cleos将表明它创建了“默认”钱包，并将为未来的钱包访问提供密码。正如消息所述，请务必保留此密码以备将来使用。这是这个输出的一个例子：
```
Creating wallet: default
Save password to use in the future to unlock this wallet.
Without password imported keys will not be retrievable.
"PW5JsmfYz2wrdUEotTzBamUCAunAA8TeRZGT57Ce6PkvM12tre8Sm"
```

keosd将在其窗口中生成一些状态输出。我们将继续使用第二个窗口进行后续cleos命令。

### 启动第一个生产者节点
我们现在可以启动第一个生产者节点。在第三个终端窗口中运行：

:
```
nodeos --enable-stale-production --producer-name eosio --plugin eosio::chain_api_plugin --plugin eosio::net_api_plugin
```

这创建了一个特殊的制作人，被称为“bios”制作人。假设所有内容都已正确执行，您应该看到nodeos创建进程报告块的输出。.

### 启动第二个生产者节点
以下命令假定您正在从您的${EOSIO_SOURCE}目录运行本教程，从中您可以运行该目录./eosio_build.sh来构建。如果不清楚  查看[Getting the Code](Local-Environment#1-getting-the-code) 获取更多信息.

T要启动其他节点，您必须先加载eosio.bios合约。通过此合约，您可以直接控制其他帐户的资源分配并访问其他特权API调用。  返回到第二个终端窗口并运行以下命令来加载合同:
```
cleos --wallet-port 8899 set contract eosio build/contracts/eosio.bios
```

我们将使用帐户名称创建一个帐户成为制作人inita。要创建该帐户，我们需要生成密钥与帐户关联，并将其导入我们的钱包。

运行create key命令：
```
cleos create key
```

这将报告新生成的公钥和私钥对，其类似于以下内容。

.

重要提示：后面的命令行指令使用下面显示的按键。为了能够直接从本教程中剪切并粘贴命令行指令，请不要使用刚刚生成的密钥。相反，如果您想使用新生成的密钥，则需要用命令中的密钥值替换密钥值。
```
Private key: 5JgbL2ZnoEAhTudReWH1RnMuQS6DBeLZt4ucV6t8aymVEuYg7sr
Public key: EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg
```

现在将私钥部分导入您的钱包。如果成功，则会报告匹配的公钥。这应该匹配先前生成的公钥：

:

```
cleos --wallet-port 8899 wallet import 5JgbL2ZnoEAhTudReWH1RnMuQS6DBeLZt4ucV6t8aymVEuYg7sr
imported private key for: EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg
```

创建inita我们将用来成为制作人的帐户。该create account命令需要两个公钥，一个用于账户的所有者密钥，另一个用于其活动密钥。在此示例中，新创建的公钥将作为拥有者密钥和活动密钥两次使用。显示create命令的输出示例:

```
cleos --wallet-port 8899 create account eosio inita EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg
executed transaction: d1ea511977803d2d88f46deb554f5b6cce355b9cc3174bec0da45fc16fe9d5f3  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"inita","owner":{"threshold":1,"keys":[{"key":"EOS6hMjoWRF2L8x9YpeqtUEcsDK...
```
我们现在有一个账户可以分配给它一个合同，使它能够做有意义的工作。在其他教程中，该帐户已被用于建立简单的合同。在这种情况下，账户将被指定为区块生产者。

在第四个终端窗口中，启动第二个nodeos实例。请注意，该命令行比我们上面用来创建第一个生产者的命令行要长得多。这是必要的，以避免与第一nodeos例的冲突。幸运的是，您可以剪切并粘贴此命令行并调整键值：



```
nodeos --producer-name inita --plugin eosio::chain_api_plugin --plugin eosio::net_api_plugin --http-server-address 127.0.0.1:8889 --p2p-listen-endpoint 127.0.0.1:9877 --p2p-peer-address 127.0.0.1:9876 --config-dir node2 --data-dir node2 --private-key [\"EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg\",\"5JgbL2ZnoEAhTudReWH1RnMuQS6DBeLZt4ucV6t8aymVEuYg7sr\"]

```

这个新节点的输出会显示一些活动，但是直到本教程的最后一步，当inita帐户注册为生产者帐户并激活时，才会停止报告。以下是新启动节点的一些输出示例。您的输出可能看起来有点不同，具体取决于您输入每个命令的时间。而且，这个例子只是输出的最后几行：

```
2393147ms thread-0   producer_plugin.cpp:176       plugin_startup       ] producer plugin:  plugin_startup() end
2393157ms thread-0   net_plugin.cpp:1271           start_sync           ] Catching up with chain, our last req is 0, theirs is 8249 peer dhcp15.ociweb.com:9876 - 295f5fd
2393158ms thread-0   chain_controller.cpp:1402     validate_block_heade ] head_block_time 2018-03-01T12:00:00.000, next_block 2018-04-05T22:31:08.500, block_interval 500
2393158ms thread-0   chain_controller.cpp:1404     validate_block_heade ] Did not produce block within block_interval 500ms, took 3061868500ms)
2393512ms thread-0   producer_plugin.cpp:241       block_production_loo ] Not producing block because production is disabled until we receive a recent block (see: --enable-stale-production)
2395680ms thread-0   net_plugin.cpp:1385           recv_notice          ] sync_manager got last irreversible block notice
2395680ms thread-0   net_plugin.cpp:1271           start_sync           ] Catching up with chain, our last req is 8248, theirs is 8255 peer dhcp15.ociweb.com:9876 - 295f5fd
2396002ms thread-0   producer_plugin.cpp:226       block_production_loo ] Previous result occurred 5 times
2396002ms thread-0   producer_plugin.cpp:244       block_production_loo ] Not producing block because it isn't my turn, its eosio

```

在这一点上，第二个nodeos是闲置的生产者。要将其转化为活跃的生产者，inita需要注册为bios节点的生产者，并且bios节点需要执行更新生产者计划的操作。



```
cleos --wallet-port 8899 push action eosio setprods "{ \"version\": 1, \"producers\": [{\"producer_name\": \"inita\",\"block_signing_key\": \"EOS6hMjoWRF2L8x9YpeqtUEcsDKAyxSuM1APicxgRU1E3oyV5sDEg\"}]}" -p eosio@active
executed transaction: 2cff4d96814752aefaf9908a7650e867dab74af02253ae7d34672abb9c58235a  272 bytes  105472 cycles
#         eosio <= eosio::setprods              {"version":1,"producers":[{"producer_name":"inita","block_signing_key":"EOS6hMjoWRF2L8x9YpeqtUEcsDKA...
```

恭喜，您现在已经配置了一个双节点测试网！您可以看到原始节点不再生成块但正在接收它们。您可以通过get info针对每个节点运行命令来验证这一点。

获取有关第一个节点的信息：


```
cleos get info
```
这应该产生看起来类似于这样的输出：

```
{
  "server_version": "223565e8",
  "head_block_num": 11412,
  "last_irreversible_block_num": 11411,
  "head_block_id": "00002c94daf7dff456cd940bd585c4d9b38e520e356d295d3531144329c8b6c3",
  "head_block_time": "2018-04-06T00:06:14",
  "head_block_producer": "inita"
}
```
现在为第二个节点：

```
cleos --port 8889 get info
```

这应该产生看起来类似于这样的输出：
```
{
  "server_version": "223565e8",
  "head_block_num": 11438,
  "last_irreversible_block_num": 11437,
  "head_block_id": "00002cae32697444fa9a2964e4db85b5e8fd4c8b51529a0c13e38587c1bf3c6f",
  "head_block_time": "2018-04-06T00:06:27",
  "head_block_producer": "inita"
}
```

在稍后的教程中，我们将探讨如何使用更高级的工具来运行多主机，多节点测试网。