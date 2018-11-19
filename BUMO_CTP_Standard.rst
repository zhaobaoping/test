BUMO CTP Standards
==================

Overview
---------

CTP (Contract Token Protocol) provides protocol standards for issuing tokens based on BUMO contracts. 
CTP also provides interfaces for third parties to transfer and use tokens.
BUMO smart contracts which are implemented in javascript include an initialization function ``init`` and two entry functions ``main`` and ``query``.
The ``init`` function is mainly for initializing parameters, and ``main`` function for data writing, ``query`` function for data query.



Purpose
--------

CTP protocol is aimed to provide interfaces for applications, like Wallets or exchanges, to use any tokens on BUMO conveniently.


Attributes of Tokens
---------------------

The attributes of tokens are stored in the smart contract account, and you can check them by using the ``tokenInfo`` function. The attributes of tokens are as follows:


+--------------+---------------------------------------+
| Variables    | Description                           |
+==============+=======================================+
| ctp          | version of Contract Token Protocol.   |
+--------------+---------------------------------------+
| name         | token name.                           |
+--------------+---------------------------------------+
| symbol       | token symbol.                         |
+--------------+---------------------------------------+
| decimals     | decimal places of tokens.              |
+--------------+---------------------------------------+
| totalSupply  | total amount of tokens.                |
+--------------+---------------------------------------+
| contractOwner| token owner.                          |	
+--------------+---------------------------------------+


.. note:: 

 - name: full spelled words with initial letters capitalized is recommanded, such as **Demo Token**.
 - symbol: capitalized acronyms is recommanded, such as **DT**.
 - decimals: the number of decimal places which is in the range of 0~8, 0 means no decimal places.
 - totalSupply: the value is in the range of 1~2^63-1.


Functions
-----------

The functions provided in BUMO CTP standards include `contractInfo`_, `name`_, `symbol`_, `decimals`_, `totalSupply`_, `balanceOf`_, `transfer`_, `transferFrom`_, `approve`_, `assign`_, `changeOwner`_ and `allowance`_.

contractInfo
^^^^^^^^^^^^^

``contractInfo`` function is used to check basic information of tokens, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
    "method":"contractInfo"
 }

**function form**

::
 
 function contractInfo ()

**returned value**

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

``name`` function is used to check the token name, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
    "method":"name"
 }

**function form**

::
 
 function name ()

**returned value**

::

 {
    "result":{
        "name":"XXXCOIN"
    }
 } 

symbol
^^^^^^^

``symbol`` function is used to check the token symbol, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
    "method":"symbol"
 }

**function form**

::
 
 function symbol ()

**returned value**

::

 {
    "result":{
        "symbol":"XXX"
    }
 } 

decimals
^^^^^^^^^

``decimals`` function is used to check the decimal places of tokens, for example, 5 means the amount of tokens is 100000, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
    "method":"decimals"
 }

**function form**

::
 
 function decimals ()

**returned value**

::

 {
    "result":{
        "decimals":5
    }
 } 


totalSupply
^^^^^^^^^^^^^

``totalSupply`` function is used to check the total supply of tokens, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
    "method":"totalSupply"
 }

**function form**

::

 function totalSupply ()

**returned value**

::

 {
    "result":{
        "totalSupply":"10000000000000000000"
    }
 } 

balanceOf
^^^^^^^^^^

``balanceOf`` function is used to check the balance of the owner account, and its entry function is ``query``.

**parameter form in json** 

::
 
 {
      "method":"balanceOf",
      "params":{
        "address":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj"
    }
 }

**parameter description**

address: account address.

**function form**

::
 
 function balanceOf ()

**returned value**

::

 {
    "result":{
        "balanceOf":"100000000000000"
    }
 } 

transfer
^^^^^^^^

``transfer`` function is used to transfer tokens with amount of **value** to the destination address **to**, and the **log** event must be triggerd.
An exception will be thrown if the source account does not have enough tokens. Its entry function is ``main``.

**parameter form in json** 

::
 
 {
    "method":"transfer",
    "params":{
        "to":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
 }

**parameter description**

to: address of the destination account.

value: the amount of tokens allowed to be transferred (string).

**function form**

::
 
 function transfer (to, value)

**returned value**

Returns **true** or throws an exception.

transferFrom
^^^^^^^^^^^^^

``transferFrom`` function is used to transfer tokens with amount of **value** from source address **from** to destination address **to**, 
and the **log** event must be triggerd. Before ``transferFrom`` function is called, **from** must have authorized **to** by calling the ``approve`` function for transferring a certain amount of tokens.
If the amount of tokens in **from** account is insufficient or if **from** has not authorized  **to** for transferring enough amount of tokens, then the ``transferFrom`` function will throw an exception. Its entry function is ``main``.


**parameter form in json** 

::
 
 {
    "method":"transferFrom",
    "params":{
        "from":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "to":"buQYH2VeL87svMuj2TdhgmoH9wSmcqrfBner",
        "value":"1000000"
    }
 }

**parameter description**

from: source address.

to: destination address.

value: the amount of tokens allowed to be transferred (string).

**function form**

::
 
 function transferFrom (from, to, value)

**returned value**

Returns **true** or throws an exception.

approve
^^^^^^^^

``approve`` function is used to authorize **spender** for transferring tokens with amount of **value** from the account of transaction sender.
Its entry function is ``main``.

**parameter form in json** 

::
 
 {
    "method":"approve",
    "params":{
        "spender":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
    }
 }

**parameter description**

spender: account address of the spender.

value: the amount of tokens an account is authorized to transfer (string).

**function form**

::
 
 function approve (spender, value)

**returned value**

Returns **true** or throws an exception.

assign
^^^^^^^

``assign`` function can be used by token owners to allocate tokens with amount of **value** to **to**. Its entry function is ``main``.


**parameter form in json** 

::
 
 {
    "method":"assign",
    "params":{
        "to":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "value":"1000000"
    }
 }

**parameter description**

to: address of the receipient account.

value: the amount of tokens allocated.

**function form**

::
 
 function assign (to, value)

**returned value**

Returns **true** or throws an exception.

changeOwner
^^^^^^^^^^^^

``changeOwner`` function is used to transfer the ownership of the contract tokens, whose default owner is the creation account, 
and only the token owner has this priviledge. Its entry function is ``main``.


**parameter form in json** 

::
 
 {
    "method":"changeOwner",
    "params":{
        "address":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj"
    }
 }

**parameter description**

address: account address.

**function form**

::
 
 function changeOwner (address)

**returned value**

Returns **true** or throws an exception.

allowance
^^^^^^^^^^

``allowance`` function is used to check the amount of tokens still allowed to be transferred out from the token owner.


**parameter form in json** 

::
 
 {
    "method":"allowance",
    "params":{
        "owner":"buQnTmK9iBFHyG2oLce7vcejPQ1g5xLVycsj",
        "spender":"buQYH2VeL87svMuj2TdhgmoH9wSmcqrfBner"
    }
 }

**parameter description**

owner: account address of the token owner.

spender: account address of the spender.

**function form**

::
 
 function allowance (owner, spender)

**returned value**

::
 
 {
    "result":{
        "allowance":"1000000",
    }
 } 

Entry Functions
----------------

BUMO smart contract provides an `init <Initialization Function init>`_, an `main <Entry Function main>`_ and an `query <Entry Function query>`_.

Initialization Function init
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``init`` function is mainly for initializing parameters, the following are its function form, parameter form in json, parameter description and returned value.

**function form**

::

 function init (input_str){
 }

**parameter form in json**

::

 {
    "params":{
        "name":"RMB",
        "symbol":"CNY",
        "decimals":8,
        "supply":"1500000000"
    }
 }

**parameter description**

name: token name.

symbol: token symbol.

decimals: decimal places.

supply: total supply of tokens (integer part).

**returned value**

Returns **true** or throws an exception.

Entry Function main
^^^^^^^^^^^^^^^^^^^^

``main`` function is mainly for data writing, which includes ``transfer``, ``transferFrom``, ``approve``, ``assign`` and 
``changeOwner``. The following is the function body of  ``main``.
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

Entry Function query
^^^^^^^^^^^^^^^^^^^^^^

``query`` function is mainly for data query, which includes ``name``, ``symbol``, ``decimals``, ``totalSupply``, 
``contractInfo``, ``balanceOf`` and ``allowance``. The following is the function body of ``query``.

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