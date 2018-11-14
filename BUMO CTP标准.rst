BUMO CTP标准
==============

概述
----

CTP1.0(Contract Token Protocol)指基于BUMO合约发行token的协议标准。该协议提供了转移token的基本功能，并允许token授权给第三方使用。
BUMO智能合约由javascript实现,包含初始化函数init和两个入口函数main、query。init函数主要负责合约创建时初始化，main函数主要负责数据写入，query函数主要负责数据查询。

目标
--------

BUMO CTP标准中所设计的接口可以让BUMO上的任何token被其他应用程序使用，比如钱包和交易所。

Token属性参数
-------------

Token属性可以通过合约的tokenInfo功能函数查询到，存储在智能合约的账号里。Token属性包含以下内容：

+--------------+----------------------------+
| 变量         | 描述                       |
+==============+============================+
| name         | Token 名称                 |
+--------------+----------------------------+
| symbol       | Token 符号                 |
+--------------+----------------------------+
| decimals     | Token 小数位数             |
+--------------+----------------------------+
|totalSupply   | Token 总量                 |
+--------------+----------------------------+
|contractOwner | Token 所有者               |	
+--------------+----------------------------+	


.. note:: 

 - name：推荐使用单词全拼，每个首字母大写。如 Demo Token。
 - symbol：推荐使用大写首字母缩写。如 DT。
 - decimals：小数位在 0~8 的范围，0 表示无小数位。
 - totalSupply：范围是 1~2^63-1。


操作
-----

BUMO CTP标准中的函数如下：

contractInfo
^^^^^^^^^^^^^

contractInfo函数用于返回token的基本信息，其入口函数为query。

**参数json结构** 

::
 
 {
    "method":"contractInfo"
 }

**函数**

::
 
 function contractInfo()

**返回值**

::

 {
    "result":{
        "type": "string",
        "value": {
            "contractInfo": {
                "ctp": "1.0",
                "name": "cccpt-bu",
                "symbol": "CBG",
                "decimals": 0,
                "totalSupply": "100000",
                "contractOwner": "buQBv4pqtNMs6ueBhx7mJULhAFYV3rSHo2Zg",
                "balance": "100000"
            }
        }
    }
 } 

name
^^^^^

name函数用于返回token的名称，其入口函数是query。

**参数json结构** 

::
 
 {
    "method":"name"
 }

**函数**

::
 
 function name()

**返回值**

::

 {
    "result":{
        "name":"XXXCOIN"
    }
 } 

symbol
^^^^^^^

symbol函数用于返回token的符号，其入口函数是query。

**参数json结构** 

::
 
 {
    "method":"symbol"
 }

**函数**

::
 
 function symbol()

**返回值**

::

 {
    "result":{
        "symbol":"XXX"
    }
 } 

decimals
^^^^^^^^^

decimals函数用于返回token使用的小数点后几位， 比如 5,表示分配token数量为100000，其入口函数为query。

**参数json结构** 

::
 
 {
    "method":"decimals"
 }

**函数**

::
 
 function decimals()

**返回值**

::

 {
    "result":{
        "decimals":5
    }
 } 


totalSupply
^^^^^^^^^^^^^

totalSupply函数用于返回token的总供应量，其入口函数为query。

**参数json结构** 

::
 
 {
    "method":"totalSupply"
 }

**函数**

::

 function totalSupply()

**返回值**

::

 {
    "result":{
        "totalSupply":"10000000000000000000"
    }
 } 

balanceOf
^^^^^^^^^^

balanceOf函数用于返回owner账户的账户余额，其入口函数为query。

**参数json结构** 

::
 
 {
      "method":"balanceOf",
      "params":{
        "address":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj"
    }
 }

**参数说明**

address: 账户地址。

**函数**

::
 
 function balanceOf()

**返回值**

::

 {
    "result":{
        "balanceOf":"100000000000000"
    }
 } 

transfer
^^^^^^^^

transfer函数用于转移value数量的token到目的地址to，并且必须触发log事件。 如果资金转出账户余额没有足够的token来支出，该函数应该被throw，其入口函数为main。

**参数json结构** 

::
 
 {
    "method":"transfer",
    "params":{
        "to":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
 }

**参数说明**

to: 目标账户地址。
value: 转移数量（字符串类型）。

**函数**

::
 
 function transfer(to, value)

**返回值**

true或者抛异常。

transferFrom
^^^^^^^^^^^^^

transferFrom函数用于从地址from发送数量为value的token到地址to，必须触发log事件。 在transferFrom之前，from必须已经调用过approve向to授权了额度。
如果from账户余额没有足够的token来支出或者from授权给to的额度不足，该函数应该被throw,其入口函数 main。

**参数json结构** 

::
 
 {
    "method":"transferFrom",
    "params":{
        "from":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "to":"buQYH2VeL87svMuj2TdhgmoH9wSmcqrfBner",
        "value":"1000000"
    }
 }

**参数说明**

from: 源账户地址。
to: 目标账户地址。
value: 转移数量（字符串类型）。

**函数**

::
 
 function transferFrom(from,to,value)

**返回值**

true或者抛异常。

approve
^^^^^^^^

approve函数用于授权账户spender从交易发送者账户转出数量为value的token，其入口函数为main。

**参数json结构** 

::
 
 {
    "method":"approve",
    "params":{
        "spender":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
    }
 }

**参数说明**

spender: 账户地址。
value: 被授权可转移数量（字符串类型）。

**函数**

::
 
 function approve(spender, value)

**返回值**

true或者抛异常。

assign
^^^^^^^

assign函数用于实现合约token拥有者向to分配数量为value的token，其入口函数为main。

**参数json结构** 

::
 
 {
    "method":"assign",
    "params":{
        "to":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
    }
 }

**参数说明**

to: 收账账户地址。
value: 分配数量（字符串类型）。

**函数**

::
 
 function assign(to, value)

**返回值**

true或者抛异常。

changeOwner
^^^^^^^^^^^^

changeOwner函数用于将合约token拥有权（默认拥有者为合约资产的创建账户）转移给address，只有合约token拥有者才能执行此权限，其入口函数为main。

**参数json结构** 

::
 
 {
    "method":"changeOwner",
    "params":{
        "address":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj"
    }
 }

**参数说明**

address: 账户地址。

**函数**

::
 
 function changeOwner(address)

**返回值**

true或者抛异常。

allowance
^^^^^^^^^^

allowance函数用于返回spender仍然被允许从owner提取的金额，其入口函数为query。

**参数json结构** 

::
 
 {
    "method":"allowance",
    "params":{
        "owner":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "spender":"buQYH2VeL87svMuj2TdhgmoH9wSmcqrfBner"
    }
 }

**参数说明**

owner: 拥有者的账户地址。
spender: 花费者的账户地址。

**函数**

::
 function allowance(owner, spender)

**返回值**

::
 
 {
    "result":{
        "allowance":"1000000",
    }
} 

入口函数
---------

入口函数init
^^^^^^^^^^^^^

**函数**

::

 function init(input_str){
 }

**参数json结构**

::

 {
    "params":{
        "name":"RMB",
        "symbol":"CNY",
        "decimals":8,
        "supply":"1500000000"
    }
 }

**参数说明**

name: 资产名称。
symbol: 资产符号。
decimals：小数位数。
supply：发型总量(整数部分)。

**返回值**

true或者抛异常。

入口函数main
^^^^^^^^^^^^^

::

 function main(input_str){
    let input = JSON.parse(input_str);

    if(input.method === 'transfer'){
        transfer(input.params.to, input.params.value);
    }
    else if(input.method === 'transferFrom'){
        transferFrom(input.params.from, input.params.to, input.params.value);
    }
    else if(input.method === 'approve'){
        approve(input.params.spender, input.params.value);
    }
    else if(input.method === 'assign'){
        assign(input.params.to, input.params.value);
    }
    else if(input.method === 'changeOwner'){
        changeOwner(input.params.address);
    }
    else{
        throw '<undidentified operation type>';
    }
 }

入口函数query
^^^^^^^^^^^^^

::

 function query(input_str){
    loadGlobalAttribute();

    let result = {};
    let input  = JSON.parse(input_str);
    if(input.method === 'name'){
        result.name = name();
    }
    else if(input.method === 'symbol'){
        result.symbol = symbol();
    }
    else if(input.method === 'decimals'){
        result.decimals = decimals();
    }
    else if(input.method === 'totalSupply'){
        result.totalSupply = totalSupply();
    }
    else if(input.method === 'contractInfo'){
        result.contractInfo = contractInfo();
    }
    else if(input.method === 'balanceOf'){
        result.balance = balanceOf(input.params.address);
    }
    else if(input.method === 'allowance'){
        result.allowance = allowance(input.params.owner, input.params.spender);
    }
    else{
       	throw '<unidentified operation type>';
    }

    log(result);
    return JSON.stringify(result);
 }