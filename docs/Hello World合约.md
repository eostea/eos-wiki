# Hello World合约
Corey Lederer 12天前 编辑了这个页面 · 有 3处 修订

## Hello World合约

本教程假定您已完成教程：[智能合约入门](https://github.com/eostea/eos-wiki/blob/master/docs/10.1%20%E6%99%BA%E8%83%BD%E5%90%88%E7%BA%A6%E5%85%A5%E9%97%A8.md)
和[Eosio.token，Exchange和Eosio.msig合约](https://github.com/eostea/eos-wiki/blob/master/docs/10.2%20Eosio.token%EF%BC%8CExchange%E5%92%8CEosio.msig%E5%90%88%E7%BA%A6.md)。

我们现在创建第一个“Hello world”合约。创建一个名为“hello”的新文件夹，cd进入该文件夹，然后使用以下内容创建一个文件“hello.cpp”：
```cpp
hello/ hello.cpp

#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>
using namespace eosio;

class hello : public eosio::contract {
  public:
      using contract::contract;

      /// @abi action 
      void hi( account_name user ) {
         print( "Hello, ", name{user} );
      }
};

EOSIO_ABI( hello, (hi) )
```
您可以用如下命令将代码编译为Web程序集（.wast）：
```cpp
$ eosiocpp -o hello.wast hello.cpp
```
注意： 编译器可能会生成警告，不用理会，可以安全地忽略。

现在生成abi：
```cpp
$ eosiocpp -g hello.abi hello.cpp
Generated hello.abi
```
创建一个账户并上传合约：
```cpp
$ cleos create account eosio hello.code EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4 EOS7ijWCBmoXBi3CgtK7DJxentZZeTkeUnaSDvyro9dq7Sd1C3dC4
...
$ cleos set contract hello.code ../hello -p hello.code
...
```
现在我们可以运行该合约：
```cpp
$ cleos push action hello.code hi '["user"]' -p user
executed transaction: 4c10c1426c16b1656e802f3302677594731b380b18a44851d38e8b5275072857  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"user"}
>> Hello, user
```
现在这个合约允许任何人授权，我们也可以这样调用：
```cpp
$ cleos push action hello.code hi '["user"]' -p tester
executed transaction: 28d92256c8ffd8b0255be324e4596b7c745f50f85722d0c4400471bc184b9a16  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"user"}
>> Hello, user
```
在这种情况下，`tester`是授权的人，`user`只是一个参数。如果我们希望合约对说"hi"的用户进行身份验证（下面演示的是只能自己对自己说"hi"），我们需要修改合同以进行身份验证。

修改hello.cpp中的hi（）函数，如下所示：
```cpp
void hi( account_name user ) {
   require_auth( user );
   print( "Hello, ", name{user} );
}
```
重复上述步骤，编译wast文件并生成abi，然后再次设置合约以部署更新。（*译者注：再次发布合约可能会碰到问题，同一个账户重复发布相同的合约，即使代码做了修改还是可能会碰到问题。这时，可以另外随便选择一个合约发布一次，然后再发布更新后的合约。*)

现在，如果我们操作的户和权限不匹配，那么合约会抛出一个错误：
```cpp
$ cleos push action hello.code hi '["tester"]' -p user
Error 3030001: missing required authority
Ensure that you have the related authority inside your transaction!;
If you are currently using 'cleos push action' command, try to add the relevant authority using -p option.
Error Details:
missing authority of tester
```
我们可以用`tester`账户的授权来解决此问题：
```cpp
$ cleos push action hello.code hi '["tester"]' -p tester
executed transaction: 235bd766c2097f4a698cfb948eb2e709532df8d18458b92c9c6aae74ed8e4518  244 bytes  1000 cycles
#    hello.code <= hello.code::hi               {"user":"tester"}
>> Hello, tester
```
## Hello World 李嘉图(Ricardian)合约

每个智能合约都必须有一个匹配的李嘉图合约。李嘉图合约规定了与智能合约的每个行为相关的具有法律约束力的行为。此处列出了Hello World合约的李嘉图合约。
```cpp
##  HELLO WORLD合约

### 参数
输入参数：无

隐含参数： 

*  _ ** account_name ** _（调用和签署合同的一方名称）

### 意图
意图。作者的意图和本合同的调用者是打印输出。它没有其他作用。

### 术语
术语。本合同在代码执行结束时到期。

### 保修
保证。{{account_name}}应以及时和勤劳的方式维护其在本合同项下的义务，使用知识和建议来执行符合EOS.IO区块链制作者制定的通用标准的服务。
  
### 默认
默认。发生以下任何情况均构成本合同项下的重大违约行为：

### 补救措施
补救措施。除当事人依法可以提供的任何和所有其他权利外，如果当事人因未能实质履行本合同的任何规定，条款或条件而违约，另一方可以通过书面通知违约派对。本通知应详细描述违约的性质。收到此通知的一方应立即从块生产者中移除，并自动终止本合同。
  
### 不可抗力
不可抗力。如果本合同的履行或本合同项下的任何义务受到任何一方的合理控制（“不可抗力”）以外的原因阻碍，限制或干扰，且该方无法履行义务，则应立即书面通知另一方如果发生此类事件，则援引该条款的当事方的义务将在此类事件所必需的范围内中止。不可抗力一词包括但不限于上帝的行为，火灾，爆炸，破坏行为，暴风雨或其他类似事件，命令或军事或民事当局的行为，或国家紧急情况，叛乱，骚乱，战争或罢工，停工，停工或供应商失败。被请求方应当在当时情况下采取合理努力，避免或者消除不履行的原因，并且应当在此类原因被移除或者停止时，继续以合理调度的方式执行。如果该方或其雇员，管理人员，代理人或分支机构承诺，遗漏或造成该行为或不行为，则该行为或不行为应被视为在一方的合理控制之下。
  
### 争议解决
争议解决。任何由本合同引起或与本合同有关的争议或纠纷都将通过根据EOS.IO区块链规定的默认规则进行约束性仲裁来解决。仲裁员的裁决是最终裁决，任何具有适当管辖权的法院都可以判决仲裁裁决。
  
### 完整协议
整个协议。本合同包含双方的全部协议，而且就本合同的主题而言，无论是口头还是书面形式的其他协议都没有其他承诺或条件。本合同取代双方之间的任何书面或口头协议。

### 可分性
可分割性。如果本合同的任何条款因任何原因被认定为无效或不可执行，则其余条款将继续有效且可强制执行。如果法院认定本合同的任何条款无效或无法执行，但通过限制该条款将变得有效且可强制执行，则该条款将被视为书面，解释并强制执行。

### 修正案
修订。如果书面文件由修改后的义务人签署，则本合同可以通过双方之间的相互协议进行修改或修改。

### 治法
管辖法律。本合同应按照公平准则解释。

### 注意
注意。根据本合同所要求或允许的任何通知或通信，如果交付给可验证的电子邮件地址或其他电子邮件地址，一方可能以书面形式公开提供或在本区块链提供的广播合同中出版以用于提供这种类型的通知。
  
### 放弃契约权利
放弃合同权利。任何一方未能执行本合同的任何规定不得解释为放弃或限制该方随后强制执行并强制严格遵守本合同各项规定的权利。

### 仲裁员对普通方的费用
仲裁员的费用，以避免派对。在本协议下产生的任何诉讼或与本协议有效性有关的任何单独诉讼中，双方应支付初始仲裁费用的一半，胜诉方应给予合理的仲裁员费用和成本。
  
### 构建和解释
建构与解释。要求对起草人施行或解释的规则被放弃。该文件应视为双方共同拟定的文件。
  
### 在见证的情况下
在此情况下，本协议各方已经促使本协议自其执行之日起由其本人或其正式授权代表执行，并且经调用本合同的交易的加密签名证明
```
