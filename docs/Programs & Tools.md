# 程序与工具
- [程序](#程序)
    - [Nodeos](#Nodeos)
    - [Cleos](#Cleos)
    - [keosd](#keosd)
    - [launcher](#launcher)
    - [snapshot](#snapshot)
- [工具](#工具)
## 程序

### Nodeos
这部分是运行一个由多个插件配置的节点的EOSIO核心守护进程，实例的用途主要是生产区块、专用API端、和本地部署。
### Cleos
cleos是一个命令行工具，它与nodeos公开的REST API进行交互。 为了使用cleos，您需要将终端（IP地址和端口号）添加到nodeos实例，并配置cleos以加载'eosio :: chain_api_plugin'。 cleos文件夹中包含所有命令的文档。 有关cleos已知的所有命令，只需简单地运行它，不需要任何参数：

><pre><code>cleos
>ERROR: RequiredError: Subcommand required
>Command Line Interface to EOSIO Client
>Usage: ./cleos [OPTIONS] SUBCOMMAND
>Options:
>  -h,--help                     打印help内容并退出
>  -H,--host TEXT=localhost      其中host 为nodeos运行的服务器地址信息
>  -p,--port UINT=8888           其中port 为nodeos运行的服务器端口信息
>  --wallet-host TEXT=localhost  其中host 为keosd 运行的服务器地址信息
>  --wallet-port UINT=8888       其中port 为keosd 运行的服务器端口信息
>  -v,--verbose                  输出详细错误信息
>Subcommands:
>  version                       返回版本号
>  create                        创建链上或链下条目
>  get                           返回链上对应变量信息
>  set                           设置或更改链状态
>  transfer                      从一个账户向另一个账户发送EOS
>  net                           与P2P网络交互
>  wallet                        与本地钱包交互
>  benchmark                     配置并执行基准
>  push                          向区块链发起交易</code></pre>
要获得有关任何特定子命令的帮助，请不要使用参数，并运行它：
><pre><code>cleos create
>ERROR: RequiredError: Subcommand required
>Create various items, on and off the blockchain
>Usage: ./cleos create SUBCOMMAND
>Subcommands:
>  key                          创建一个新的公私钥对，并打印出公钥与私钥
>  account                      创建一个新的账户
>  producer                     创建一个新的区块生产者</code></pre>

><pre><code>cleos create account
>ERROR: RequiredError: creator
>Create a new account on the blockchain
>Usage: ./cleos create account [OPTIONS] creator name OwnerKey ActiveKey
>Positionals:
>  creator TEXT                正在创建新账户的账户名称
>  name TEXT                   新账户名称
>  OwnerKey TEXT               该账户对应的owner公钥
>  ActiveKey TEXT              该账户对应的Active公钥
>Options:
>  -s,--skip-signature         设置不应使用解锁的钱包密钥来签署交易
>  -x,--expiration             设置交易最长等待确认时间，以秒为量级, 缺省值为 30s
>  -f,--force-unique           强制交易特殊，这将消耗额外带宽，并移除为防止多次意外发布相同交易的任何保护措施</code></pre>
### keosd
这是载入由插件配置的钱包的EOSIO钱包守护进程，如HTTP端或RPC API。

### launcher

launcher(启动器)可以使在局域网或广域网中发布多个eosd节点更加方便。 可以通过CLI（命令行）来对launcher进行操作，包括用来修改每个节点的配置文件，并且在对等主机之间安全地分发这些配置文件，然后启动eosd节点的多个实例。

### snapshot
snapshot引用了EOSIO/genesis仓库中相关应用模块，其中包含了用于crowdsale合约快照生成的nodejs应用程序，用于配置创世区块的Web图形界面以及其他与创世相关的工具。