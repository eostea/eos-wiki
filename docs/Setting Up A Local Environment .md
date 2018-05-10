
# 设置本地环境

----
## 目录
- [概览](#概览)
- [获取代码](#获取代码)
-


## Getting the Code

To download all of the code, clone the eos repository and its submodules.
> git clone https://github.com/EOSIO/eos --recursive

If a repository is cloned without the --recursive flag, the submodules can be retrieved after the fact by running this command from within the repo:

> git submodule update --init --recursive

Throughout the EOSIO documentation and tutorials, reference will be made to the top level of your local EOSIO source repository. The location where you just cloned the eos repository is that location. The notation ${EOSIO_SOURCE} represents the same thing. For example, if you ran the git clone operation in a folder called ~/myprojects, then ${EOSIO_SOURCE}=~/myprojects/eos. Note that ${EOSIO_SOURCE} is used in the documentation for notational purposes only. No environment variable is implied or required. More commonly, for simplicity, this and other documents might simply refer to eos. This is equivalent to ${EOSIO_SOURCE}.


## 获取代码

下载所有的代码，克隆eos项目库和子模块.

> git clone https://github.com/EOSIO/eos --recursive

如果某个项目库已经被克隆没有 `--recursive` 标记 ，则可以在repo内运行以下命令来检索子模块：

> git submodule update --init --recursive

通过整个EOSIO文档和教程，将参考你本地最顶层资源库.你刚刚克隆EOS存储路径就是该位置.这个符号 `$ {EOSIO_SOURCE}`表示相同位置。例如， 如果你在名为`~/myprojects`文件夹中运行`git clone`操作，那么 `${EOSIO_SOURCE}=~/myprojects/eos`.请注意`$ {EOSIO_SOURCE}` 在文档中仅被用于标记目的.没有环境变量是被默认或者必须的.更多常用的，为简单起见，这个文档和其他文档可能只是简单地引用了`eos`.这相当于 `${EOSIO_SOURCE}`.
