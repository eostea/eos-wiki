# C++ API
## eosio::multi_index

本节介绍`eosio::multi_index C ++ API`。 该接口实际上是Boost Multi-Index容器库的改编版本。 它直接使用`boost/multi_index/const_mem_fun`类模板进行密钥提取。 它还使用Boost Hana库进行元编程。 有关其他概念信息和详细信息，请参阅www.boost.org上的Boost文档。

在下面的描述中，以下别名将用于通用模板声明。

|别号|描述|
|---|---|
|object_type|Multi-Index表中的对象的类型|
|secondary_index|多索引表的相应二级索引的类型|

## 构造函数

|方法|描述|
|---|---|
|multi_index( uint64_t code, uint64_t scope )|构造一个多索引表的实例|

|参数|描述|
|---|---|
|code|拥有表格的帐户|
|scope|代码层次结构中的范围标识符|
|后置条件|多索引表的新的空实例被创建|
|`code`和`scope`成员属性被初始化|多索引表的新的空实例被创建|
|代码和作用域成员属性被初始化| |
|每个二级索引表初始化| |
#### 注意：`eosio::multi_index`模板具有模板参数<uint64_t TableName,typename T，typename ... Indices>，其中：

- `TableName` 是表格的名称，最长12个字符，名称中的字符由小写字母，数字1至5和“.”组成。
- `T` 是对象类型.
- `Indices` 是最多16个二级指标的清单。
  - 每个必须是默认的可构造类或结构.
  - 每个函数都必须有一个函数调用操作符，它接受对表格对象类型的const引用并返回辅助键类型或对辅助键类型的引用.
  - 建议使用`eosio::const_mem_fun`模板，它是`boost::multi_index::const_mem_fun`的一个类型别名。有关更多详细信息，请参阅Boost const_mem_fun密钥提取器的文档。

## 复制和分配

不支持复制构造函数和赋值运算符。

## 表操作

### emplace
|方法|描述|
|----|----|
|const_iterator emplace( unit64_t payer, Lambda&& constructor )|向表中添加一个新对象（即row）。|

|参数|描述|
|----|----|
|payer|新对象的存储使用的`payer`帐户名称|
|constructor|lambda函数，它可以在表中就地创建要创建的对象|

|返回|描述|
|----|----|
|返回|一个主键迭代器到新创建的对象|

|前提|描述|
|----|----|
|前提|`payer`是授权当前操作的有效帐户（因此允许为存储使用收费）|


|后置条件|描述|
|----|----|
|在多索引表中创建一个新对象，并使用唯一的主键（如对象中指定的）。该对象被序列化并写入表中。如果该表不存在，则创建该表。| |
|二级索引被更新以引用新添加的对象。如果辅助索引表不存在，则会创建它们。| |
|`payer`收取新对象的存储使用费，并且如果必须创建表（和二级索引表），则为表创建的开销。| |
|Exceptions|当前接收者不是拥有该表格的帐户（multi_index的代码）。|

### get

|方法|描述|
|----|----|
|const object_type& get( uint64_t primary )const|使用主键从表中检索现有对象。|

|参数|描述|
|----|----|
|primary|对象的主键值|

|返回|描述|
|----|----|
|返回|对包含指定主键的对象的常量引用。|

|前提|描述|
|----|----|
|前提|None|


|后置条件|描述|
|----|----|
|Exceptions|没有任何对象与给定的键匹配|

### find

|方法|描述|
|----|----|
|const_iterator find( uint64_t primary )const|使用主键在表中搜索现有对象。|

|参数|描述|
|----|----|
|primary|对象的主键值|

|返回|描述|
|----|----|
|返回|找到的对象的迭代器，其主键等于主键,|
||如果一个对象主键`primary`未找到,`end`被引用表的迭代器.|

|前提|描述|
|----|----|
|前提|None||


|后置条件|描述|
|----|----|
|后置条件|None|
|Exceptions|None|

### modify

|方法|描述|
|----|----|
|void modify( const_iterator itr, uint64_t payer, Lambda&& updater )|修改表中的现有对象。|
|void modify( const object_type &obj, uint64_t payer, Lambda&& updater )| |

|参数|描述|
|----|----|
|itr|指向要更新的对象的迭代器|
|obj|对要更新的对象的引用|
|payer|`payer`的账户名称用于更新行的存储使用情况;值为0表示修改后的行的`payer`与现有`payer`相同。|
|updater|用于更新目标对象的lambda函数|

|前提|描述|
|----|----|
|前提|itr指向，或obj是表中的现有对象。|
|前提|`payer`是授权当前操作的有效帐户（因此允许为存储使用收费）|


|后置条件|描述|
|----|----|
|后置条件|修改的对象被序列化，然后替换表中的现有对象。|
||二级指数更新;更新对象的主键不会更改。|
||`payer`收取更新对象的存储使用费。|
|后置条件|如果付款人与现有付款人相同，付款人仅支付现有和更新对象之间的使用差额（如果差额为负数，则退款）。|
|后置条件|如果付款人与现有付款人不同，现有付款人将退还现有物品的存储使用权。|
|Exceptions|如果用无效的前提条件调用，则中​​止执行。|
|Exceptions|当前接收者不是拥有该表格的帐户（multi_index的代码）。|

## erase

|方法|描述|
|----|----|
|const_iterator erase( const_iterator itr )|使用主键从表中删除现有的对象。|
|void erase( const object_type& obj )| |

|参数|描述|
|----|----|
|itr|指向要删除的对象的迭代器|
|obj|对要删除的对象的引用|

|返回|描述|
|----|----|
|返回|对于使用const_iterator的签名，返回指向移除对象后面的对象的指针。|

|前提|描述|
|----|----|
|前提|None|


|后置条件|描述|
|----|----|
|后置条件|该对象将从表格中删除，并且所有关联的存储都将被回收。|
| |与表格相关的二级索引被更新。|
| |对于该对象的存储使用情况的现有付款人将退还已移除对象的表和次级索引使用情况，并且如果移除了表和索引，则为相关联的开销。|
|后置条件|如果`payer`与现有`payer`相同，`payer`仅支付现有和更新对象之间的使用差额（如果差额为负数，则退款）。|
|Exceptions|要删除的对象不在表格中。|
|Exceptions|该操作无权修改表格。|
|Exceptions|给定的迭代器是无效的。|

# 成员访问

## get_code
|方法|描述|
|----|----|
|uint64_t get_code() const|返回代码成员属性。|
|Returns|拥有主表的代码的帐户名称|

## get_scope

|方法|描述|
|----|----|
|uint64_t get_scope() const|返回作用域成员属性。|
|Returns|在当前接收器的代码内的范围的范围ID，在该范围内可以找到期望的主表实例|

# Utilities

## available_primary_key

|方法|描述|
|----|----|
|uint64_t available_primary_key()const|返回一个可用（未使用的）主键值，打算用于表中主键必须为自动递增的表中，因此决不会将该键设置为自定义值。 由于无法分配可用的主键，违反此期望可能会导致表格显示为已满。|

# Iterators
多指数迭代器遵循C ++迭代器常用的模式。 所有迭代器都是双向常量，可以是const_iterator或const_reverse_iterator。 迭代器可以取消引用以提供对多索引表中的对象的访问。

## begin and cbegin

|方法|描述|
|----|----|
|const_iterator begin()const|返回指向多指标表中具有最低主键值的object_type的迭代器。|
|const_iterator cbegin()const|　|

## end and cend

|方法|描述|
|----|----|
|const_iterator end()const|返回一个迭代器，指向表示刚刚过去表的最后一行的虚拟行（假设存在）;不能被解除引用;可以向后迭代，但不能向前迭代。|
|const_iterator cend()const	|　|

## rbegin and crbegin

|方法|描述|
|----|----|
|const_iterator rbegin()const|返回指向多指标表中具有最高主键值的object_type的反向迭代器。|
|const_iterator crbegin()const|　|

## rend and crend

|方法|描述|
|----|----|
|const_iterator rend()const|返回一个迭代器，指向表示刚刚过去表的最后一行的虚拟行（假设存在）;不能被解除引用;可以向前迭代，但不能向后迭代。|
|const_iterator crend()const|　|

## lower_bound

|方法|描述|
|----|----|
|const_iterator lower_bound( uint64_t primary )const|使用大于或等于给定主键的最低主键搜索object_type。|
|Parameters||
|primary|为下限搜索建立目标值的主键|
|Returns|　|
|指向具有大于或等于主键的最小主键的object_type的迭代器|　|
|如果找不到对象或表不存在，则返回`end`迭代器|　|

## upper_bound

|方法|描述|
|----|----|
|const_iterator upper_bound( uint64_t primary )const|使用大于给定主键的最小主键搜索object_type。|
|Parameters|　|
|primary|为上限搜索建立目标值的主键|
|Returns|　|
|一个迭代器，指向具有大于主键的最低小键的object_type|　|
|如果找不到对象或表不存在，则返回`end`迭代器。|　|

## get_index

|方法|描述|
|----|----|
|secondary_index get_index<IndexName>()	|返回一个适当类型的二级索引。|
|secondary_index get_index<IndexName>()　const|　|
|Parameters|　|
|IndexName|所需的二级索引的ID|
|Returns|适当类型的索引，即以下之一:|
|idx64|原始的64位无符号整数密钥|
|idx128|原始128位无符号整数密钥|
|idx256|256位固定大小的字典键|
|idx_double|双精度浮点键|
|idx_long_double|四倍精度浮点键|

## iterator_to

|方法|描述|
|----|----|
|const_iterator iterator_to( const object_type& obj )const|返回多索引表中给定对象的迭代器。|
|secondary_index get_index<IndexName>() const|　|
|Parameters||
|obj|对所需对象的引用|
|Returns|给定对象的迭代器|

## indexed_by

indexed_by结构用于实例化多索引表的索引。在EOSIO中，最多可以指定16个二级索引。
```
template<uint64_t IndexName, typename Extractor>
struct indexed_by {
   enum constants { index_name   = IndexName };
   typedef Extractor secondary_extractor_type;
};
```
### 参数

- IndexName 是索引的名称。 该名称必须作为EOSIO base32编码的64位整数提供，并且必须符合最多13个字符的EOSIO命名要求，前十二个来自小写字符az，数字0-5和“.”，并且如果有第13个字符，它仅限于小写字母a-p和“.”。

- Extractor 是一个函数调用操作符，它接受对表格对象类型的const引用并返回辅助键类型或对辅助键类型的引用。 建议使用`eosio::const_mem_fun模板`，它是`boost::multi_index::const_mem_fun`的一个类型别名。 有关更多详细信息，请参阅Boost const_mem_fun密钥提取器的文档

## 例子

```
multi_index<mytable, record,
     indexed_by< N(bysecondary), const_mem_fun<record, uint128_t, &record::get_secondary> >
  > table( code, scope);
```
在此示例中，多索引表名为`mytable`，具有对象（行）类型`record`，`bysecondary`为辅助索引，其名称使用N宏转换为所需的编码格式。 为记录对象类指定const_mem_fun键提取器; 键类型是uint128_t，使用`record::get_secondary`函数获得。

# 该 eosio::multi_index::index API（二级索引）

EOSIO多索引表最多可以有16个二级索引。 索引可以使用`get_index`方法从`multi_index`对象中检索，然后可以使用通用的C++迭代器模式进行迭代。

这里描述的索引API在许多方面都与上述的`multi_index`相同，但它在一些重要方面也有所不同。

- 索引旨在提供替代手段来访问多索引表。
- 二级索引表的内容基本上是映射到主键的键。该映射对用户是透明的。
- 有些操作不适用于二级索引，最值得注意的是，一种操作不使用二级索引来放置内容。可以使用辅助索引修改和删除对象。
- 使用辅助表检索主要内容的操作实际上是相同的，主要区别在于使用的键值（您无法将对象传递到辅助索引以在表中找到它）。 键值将是支持的二级索引类型之一：
  - idx64 - 原始的64位无符号整数密钥.
  - idx128 - 原始128位无符号整数密钥.
  - idx256 - 256位固定大小的字典键.
  - idx_double - 双精度浮点键.
  - idx_long_double - 四倍精度浮点键.

- 二级索引具有`code`和`scope`成员属性，与多索引表上的相应属性相同。

|别号|描述|
|---|---|
|object_type|Multi-Index表中的对象的类型|
|secondary_index|多索引表的相应二级索引的类型|

## 构造函数
辅助索引是作为多指数表构造的一部分而创建的。不支持直接构建索引。

## 复制和分配

不支持复制构造函数和赋值运算符。

## 表操作

### get

|方法|描述|
|----|----|
|const object_type& get( secondary_key_type&& secondary )const|使用其辅助键从表中检索现有对象。多个签名可用。|
|const object_type& get( const secondary_key_type& secondary )const| |

|参数|描述|
|----|----|
|secondary|对象的辅助键值|

|返回|描述|
|----|----|
|返回|对包含指定主键的对象的常量引用。|

|前提|描述|
|----|----|
|前提| |


|后置条件|描述|
|----|----|
|Exceptions|没有任何对象与给定的键匹配|

### find

|方法|描述|
|----|----|
|const_iterator find( secondary_key_type&& secondary )const|使用辅助键在表格中搜索现有对象。|
|const_iterator find( const secondary_key_type&& secondary )const||

|参数|描述|
|----|----|
|secondary|对象的辅助键值|

|返回|描述|
|----|----|
|返回|找到值等于辅助键的二级键的对象的迭代器，未找到具有辅助键辅助键的对象时，返回`end`迭代器|

|前提|描述|
|----|----|
|前提|None|


|后置条件|描述|
|----|----|
|后置条件|None|
|Exceptions|None|

### modify

|方法|描述|
|----|----|
|void modify( const_iterator itr, uint64_t payer, Lambda&& updater )|修改表中的现有对象。|
|void modify( const object_type &obj, uint64_t payer, Lambda&& updater )| |

|参数|描述|
|----|----|
|itr|指向要更新的对象的迭代器|
|obj|对要更新的对象的引用|
|payer|`payer`的账户名称用于更新行的存储使用情况;值为0表示修改后的行的`payer`与现有`payer`相同。|
|updater|用于更新目标对象的lambda函数|

|前提|描述|
|----|----|
|前提|itr指向表中的现有对象。|
|前提|`payer`是授权当前操作的有效帐户（并因此允许为使用的存储收费）|


|后置条件|描述|
|----|----|
|后置条件|修改的对象被序列化，然后替换表中的现有对象。|
|　|二级索引更新;更新对象的主键不会更改。|
|　|`payer`收取更新对象的存储使用费。|
|后置条件|如果`payer`与现有`payer`相同，`payer`仅支付现有和更新对象之间的使用差额（如果差额为负数，则退款）。|
|后置条件|如果`payer`与现有`payer`不同，现有`payer`将退还现有物品的存储使用权。|
|Exceptions|如果用无效的前提条件调用，则中​​止执行。|
|Exceptions|当前接收者不是拥有该表格的帐户（multi_index的代码）。|

## erase

|方法|描述|
|----|----|
|const_iterator erase( const_iterator itr )|使用其辅助键从表中删除现有对象。|
|void erase( const object_type& obj )	|　|

|参数|描述|
|----|----|
|itr|指向要删除的对象的迭代器|
|obj|对要删除的对象的引用|

|返回|描述|
|----|----|
|返回|使用const_iterator，返回指向移除对象后面的对象的指针。|

|前提|描述|
|----|----|
|前提|None|


|后置条件|描述|
|----|----|
|后置条件|该对象将从表格中删除，并且所有关联的存储都将被回收。|
|　|与表格相关的二级索引被更新。|
|　|对于该对象的存储使用情况的现有`payer`将退还已移除对象的表和次级索引使用情况，并且如果移除了表和索引，则为相关联的开销。|
|后置条件|与`payer`现有`payer`相同，`payer`仅支付现有和更新对象之间的使用差额（如果差额为负数，则退款）。|
|Exceptions|要删除的对象不在表格中。|
|Exceptions|该操作无权修改表格。|
|Exceptions|给定的迭代器是无效的。|

# 成员访问

## get_code
|方法|描述|
|----|----|
|uint64_t get_code() const|返回代码成员属性。|
|Returns|拥有次要索引表的代码的账户名称|

## get_scope

|方法|描述|
|----|----|
|uint64_t get_scope() const|返回作用域成员属性。|
|Returns|范围在当前接收器的代码范围内，在该范围内可找到所需的二级索引表实例|

## name

|方法|描述|
|----|----|
|constexpr static uint64_t name()|返回index_table_name成员常量。|
|Returns|二级索引的表名|

## number

|方法|描述|
|----|----|
|constexpr static uint64_t number()|返回index_number成员常量。|
|Returns|二级索引的number|

# Iterators
EOSIO二级索引迭代器遵循C++迭代器常用的模式。

## const_iterator

|方法|描述|
|----|----|
|struct const_iterator : public std::iterator<std::bidirectional_iterator_tag, const object_type>|多索引表中的对象的双向迭代器。|
|参数| |
|object_type|表中对象的类型名称|

## const_reverse_iterator

|方法|描述|
|----|----|
|typedef std::reverse_iterator<const_iterator> const_reverse_iterator|多引用表中的对象的反向双向迭代器。|


## begin and cbegin

|方法|描述|
|----|----|
|const_iterator begin()const|返回指向次级索引表中最小次级键值的迭代器。|
|const_iterator cbegin()const	|　|

## end and cend

|方法|描述|
|----|----|
|const_iterator end()const|返回一个迭代器，指向表示刚刚过去表的最后一行的虚拟行（假设存在）;不能被解除引用;可以向后推进，但不能向前推进。|
|const_iterator cend()const	|　|

## rbegin and crbegin

|方法|描述|
|----|----|
|const_iterator rbegin()const	|返回指向次级索引表中最高次级键值的反向迭代器。|
|const_iterator crbegin()const|　|

## rend and crend

|方法|描述|
|----|----|
|const_iterator rend()const|返回一个迭代器，指向表示刚刚过去表的最后一行的虚拟行（假设存在）;不能被解除引用;可以向前推进，但不能向后推进。|
|const_iterator crend()const|　|

## lower_bound

|方法|描述|
|----|----|
|const_iterator lower_bound( secondary_key_type&& secondary )const|搜索具有大于或等于给定次级索引值的最小次级索引值的object_type。|
|const_iterator lower_bound( const secondary_key_type&& secondary )const	||
|Parameters||
|secondary|建立下界搜索的目标值的辅助键|
|Returns| |
|指针指向次级索引中具有大于或等于次级的最小次级索引值的对象的迭代器，如果找不到对象，则返回`end`迭代器，包括如果表不存在||

## upper_bound

|方法|描述|
|----|----|
|const_iterator upper_bound( secondary_key_type&& secondary )const|使用大于给定次级索引值的最小次级索引值搜索object_type。|
|const_iterator upper_bound( const secondary_key_type&& secondary )const	|　|
|Parameters|　|
|secondary|为上限搜索建立目标值的辅助键|
|Returns|　|
|指向次级索引中具有大于次级的最小次级索引值的对象的迭代器，或者如果找不到对象，则返回`end`迭代器，包括如果表不存在|　|

## iterator_to

|方法|描述|
|----|----|
|const_iterator iterator_to( const object_type& obj )const|返回Multi-Index表中给定对象的二级索引迭代器。|
|Parameters|　|
|obj|对所需对象的引用|
|Returns|给定对象的迭代器|

# Utilities

## extract_secondary_key

|方法|描述|
|----|----|
|static auto extract_secondary_key(const object_type& obj)|从辅助索引中返回对象的辅助键值。|
|参数|　|
|obj|对所需对象的const引用|
|Return|该索引的给定对象的关键值|
