| Release (tag)                 | Date        | Version     | Comments             |
|-------------------------------|-------------|-------------|----------------------|
| Dawn v1.0.0 (dawn-v1.0.0)     | 2017 Sep 14 | 6294dea [1] | First beta release   |
| Dawn v2.0.0 (dawn-v2.0.0)     | 2017 Dec 4  | 9703495     | Second beta release  |
| STAT-2017-12-12            | 2017 Dec 12 | 00db493  | reverts primary genesis.json|
| STAT-2017-12-13            | 2017 Dec 13 | 941fa2f  | connect via remote **`nodeos`** |
| STAT-2017-12-21            | 2017 Dec 21 | 233ba4e  | p2p stability and other fixes |
| DAWN-2018-01-05            | 2018 Jan 5 | 2cc40a4  | Stability release for DAWN 2.x |
| DAWN-2018-01-09-ALPHA            | 2018 Jan 9 | ede87e6  | Stability release for DAWN 2.x |
| DAWN-2018-01-18-ALPHA            | 2018 Jan 18 | 466b4ce  | Stability release for DAWN 2.x |
| DAWN-2018-01-23-ALPHA            | 2018 Jan 23 | 26eecaa  | local nodeos network updates for DAWN 2.x |
| DAWN-2018-01-31-ALPHA            | 2018 Jan 23 | d75c30e  | DAWN 3.0 pre-release, single node only |
| DAWN-2018-02-12           | 2018 Feb 12 | 6992cf4  | Stability release for DAWN 2.x |
| DAWN-2018-02-14           | 2018 Feb 14 | bcb5bf7  | Stability release for DAWN 2.x |
| SUPERDAWN-2018-03-02           | 2018 Mar 2 | 8a9f412  | Pre-release for DAWN 3.0 |
| SUPERDAWN-2018-03-04           | 2018 Mar 4 | 4274b19  | Pre-release for DAWN 3.0 |
| SUPERDAWN-2018-03-18           | 2018 Mar 8 | a731471  | Pre-release for DAWN 3.0 |
| DAWN-2018-03-23           | 2018 Mar 23 | a7f2601  | Pre-release for DAWN 3.0 |
| DAWN-2018-03-28-ALPHA           | 2018 Mar 28 | 5a18389  | ALPHA Pre-release for DAWN 3.0 |
| DAWN-2018-03-29-ALPHA           | 2018 Mar 29 | d6c3633  | ALPHA Pre-release for DAWN 3.0 |
| DAWN-2018-03-30-ALPHA           | 2018 Mar 30 | 1c1e93d  | ALPHA Pre-release for DAWN 3.0 |
| EOSIO DAWN 3.0           | 2018 Apr 5 | d9ad8ee  | DAWN 3.0 Official Release |

NOTES:  
* EOS的所有release版本可以从[这里](https://github.com/EOSIO/eos/releases)找到。
* EOS的TAG可以在[这里](https://github.com/EOSIO/eos/tags)找到。
* tag永远不能提供包含自己的文档。因此，上述列表位于Readme.md时，不能包含最新的tag。在Wiki中发现的这个列表可以包含最新的tag。

[a]第一个hotfix创建于2018，hotfix被标记为"DAWN"和发布此hotfix的年、月和日的格式，即DAWN-YYY-M-DD。如果两个hotfix在同一天，那么第二个会加一个小写字母的"a"作为后缀，第三个小写字母"b"。比如在“DWWN-2018－03-03b”。开发人员可以用hotfix tag 来更新到每周一个sprint所产生的最新的代码（译者注：这里的sprint应该是敏捷开发中的一个阶段,一个sprint可以是一周也可以是两周），当然访问的这个hotfix也有可能是在一个sprint的中间。可能没有任何注释去解释的一个hotfix，即使有可能也非常简短。文档不会在hotfix计划中发布；大家伙也别指望找到关于每一个hotfix tag的文档。（但是DoxGEY自动化文档除外，它可以与每个tag保持一致）。任何不是官方发布的tag将包括后缀"APLHA"，表示这个版本可能有未解决的bug。


[b]版本发布始于2018年，每个版本被标记为"dawn"和"-R"，然后是Major版本和Feature版本，格式为DAWN-Rmaj.fea。例如，“DAWN-R3.1”——它的Major版本是3，Major版本中的feature版本是1。每一个开发人员使用可以使用版本的tag来下载包含最新文档和发行说明的稳定代码。
